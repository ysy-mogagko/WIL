# 20250802

## Study

- LangChain Tutorial w/ Colab
    - Classification
    - Extraction

- DATASCI112: Principles of Data Science Week 4
    - [Cars and Fuel Efficiency (exercise)](https://github.com/Elnya/DATASCI112/blob/main/week4/4.2.cars-and.fuel-efficiency.ipynb)

# Next week

## Study

- DATASCI112 Week 5

# Roadmap to ML (NLP ver.)

```mermaid
flowchart TD

DATASCI112["Principles of Data Science"] --> la
DATASCI112 --> stats
DATASCI112 --> ling

subgraph la[Linear Algebra]
CME100["Vector Calculus for Engineers"] --> ENGR108["Introduction to Matrix Methods"]
end

subgraph stats[Statistics]
CS109["Probability for Computer Scientists"]
end

subgraph ling[Linguistics]
CS124["From Languages to Information"]
end

subgraph nlp_ir [NLP / IR]
CS224N["Natural Language Processing with Deep Learning"] --> CS224U["Natural Language Understanding"]
CS224U --- CS276["Information Retrieval and Web Search"]
end

la --> nlp_ir
stats --> nlp_ir
ling --> nlp_ir

click DATASCI112 "https://web.stanford.edu/class/datasci112/" "Principles of Data Science"
click CME100 "https://www.coursera.org/learn/vector-calculus-engineers" "Vector Calculus for Engineers"
click ENGR108 "https://stanford.edu/class/engr108/" "Introduction to Matrix Methods"
click CS109 "https://web.stanford.edu/class/cs109/" "Probability for Computer Scientists"
click CS124 "https://web.stanford.edu/class/cs124/" "From Languages to Information"
click CS224N "https://web.stanford.edu/class/cs224n/" "Natural Language Processing with Deep Learning"
click CS224U "https://web.stanford.edu/class/cs224u/" "Natural Language Understanding"
click CS276 "https://web.stanford.edu/class/cs276/" "Information Retrieval and Web Search"
```