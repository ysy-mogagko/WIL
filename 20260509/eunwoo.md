# 20260509 eunwoo

## AgentCore 사용

main.py에 내 달력 정보를 조회하는 Agent tool을 추가했습니다.
에이전트가 사용자 요청을 받으면 로컬 API인 /j-planner/v1/calendar/list를 호출하고, startDateTime, endDateTime을 query parameter로 넘깁니다.

날짜 범위를 주지 않으면 자동으로 현재 달 1일 00:00:00부터 마지막 날 23:59:59까지 조회하도록 만들었습니다.

CALENDAR_API_BASE_URL = os.getenv("CALENDAR_API_BASE_URL", "http://localhost:8080")
def _get_current_month_range() -> tuple[str, str]:
    now = datetime.now()
    start = now.replace(day=1, hour=0, minute=0, second=0, microsecond=0)
    next_month = (start.replace(day=28) + timedelta(days=4)).replace(day=1)
    end = next_month - timedelta(seconds=1)
    return start.isoformat(timespec="seconds"), end.isoformat(timespec="seconds")
달력 API 호출 함수입니다.

@tool
def get_calendar_list(startDateTime: str = "", endDateTime: str = "") -> str:
    if not startDateTime or not endDateTime:
        startDateTime, endDateTime = _get_current_month_range()

    query = urlencode({
        "startDateTime": startDateTime,
        "endDateTime": endDateTime,
    })

    url = f"{CALENDAR_API_BASE_URL.rstrip('/')}/j-planner/v1/calendar/list?{query}"
    request = Request(url, headers={"Accept": "application/json"})

    with urlopen(request, timeout=10) as response:
        body = response.read().decode("utf-8")

    data = json.loads(body)
    return _format_calendar_events(data, startDateTime, endDateTime)
그리고 API 응답 JSON을 그대로 넘기지 않고, 일정만 보기 좋게 요약하도록 정리했습니다.

def _format_calendar_events(data: dict, startDateTime: str, endDateTime: str) -> str:
    events = data.get("data", [])

    if not events:
        return f"No calendar events found from {startDateTime} to {endDateTime}."

    events = sorted(events, key=lambda event: event.get("startDatetime") or "")

    lines = [
        f"Calendar events from {startDateTime} to {endDateTime}:",
        f"Total: {data.get('dataCount', len(events))}",
    ]

    for event in events:
        detail = event.get("detail") or {}
        title = event.get("title") or "(No title)"
        location = detail.get("textLocation") or "No location"

        lines.append(
            f"- {title}: {event.get('startDatetime')} ~ {event.get('endDatetime')}, "
            f"Location: {location}"
        )

    return "\n".join(lines)
마지막으로 Agent의 tool 목록에 get_calendar_list를 등록해서, 에이전트가 달력 조회 요청에 이 함수를 사용할 수 있게 했습니다.

tools=[get_return_policy, get_product_info, get_calendar_list]
