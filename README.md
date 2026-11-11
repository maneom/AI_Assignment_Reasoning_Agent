# AI_Assignment_Reasoning_Agent

REASONING AGENT - CSL7610 Assignment
====================================

Task: given a knowledge base (facts + rules) and a query, decide whether the
query is entailed: True or False.

No LLM / generative AI is used. Every answer comes from a classical reasoning
engine (forward chaining, unification, search, resolution/DPLL).


1. SUBMISSION CONTENTS
----------------------
submission/
|-- reasoning_agent.ipynb   complete implementation (prints accuracy + inference time)
|-- report.pdf              representation, algorithms, experiments, reasoning traces
|-- submission.csv          predictions: id, label (True/False)
`-- README.txt              this file

Note: the submission.csv included here is only a SAMPLE output. When the
last cell (Section 13, standalone) is run with TEST_PATH set to the private
test file, a NEW submission.csv is created and replaces this one.


2. REQUIREMENTS
---------------
- Python 3.8 or newer
- pandas            (pip install pandas)
- matplotlib, numpy (only for the EDA plots in Section 2)
No internet, no API key and no GPU are needed.


3. QUICK START: RUN ON A NEW / PRIVATE TEST FILE
------------------------------------------------
The LAST cell of the notebook (Section 13, "STANDALONE CELL") contains
everything: imports, input reader, all engines and the file loader.
It runs on its own, even if no other cell was run.

  1. Put the test file in the same folder as the notebook.
  2. In the last cell, set:
         TEST_PATH = "your_test_file.csv"
  3. Run only that cell.

Output:
  - submission.csv with columns: id, label
  - TOTAL INFERENCE TIME
  - FINAL ACCURACY (only if the test file has a label column)

Accepted file types : .csv  .tsv  .json  .jsonl  .txt
Column names are detected automatically, e.g.
  context   : context, premises, premises-FOL, logic, kb, theory, given_info
              (or separate "facts" and "rules" columns)
  statement : statement, query, question, conclusion, conclusion-FOL, goal
  id        : id, example_id, question_id   (if missing: row number)
  label     : label, flag, answer, expected (optional, for accuracy only)

A .txt file can end each question with a "Query: ..." line, or separate
questions with blank lines (the last line of each block is the query).


4. ACCEPTED INPUT STYLES (inside the context / statement)
---------------------------------------------------------
Unicode FOL : Young(bob)   ∀x (Young(x) → Smart(x))
ASCII       : forall x (Young(x) -> Smart(x))     ~  !  &  |  ->  <->  xor
Prover9     : all x (Young(x) -> -Old(x)).
Prolog      : smart(X) :- young(X).     ?- smart(bob).
English     : Bob is young. If someone is young, then they are smart.
CounterBench: We know that A causes B ... Would B occur if not A instead of A?

Headers (Facts:, Rules:), numbering (1., -), and a "Query:" line inside the
context are handled automatically.


5. RUNNING THE FULL NOTEBOOK (public datasets)
----------------------------------------------
Place the public datasets next to the notebook:

  RuleTaker/train.csv, RuleTaker/test.csv, RuleTaker/ood_test.csv
  CounterBench/data_balanced_alpha_V1.csv
  CounterBench/data_balanced_backdoor_V2.csv
  FOLIO/train.csv, FOLIO/validation.csv

Then run all cells (Kernel -> Restart & Run All).
Missing files are skipped with a message; the run does not stop.

Sections:
   0  Settings
   1  Imports
   2  EDA
   3  Input reader (any format -> clean text)
   4  Engine A: English rules -> forward chaining with unification
   5  Engine B: CounterBench -> search + forward chaining
   6  Engine C: FOL -> Horn forward chaining, else CNF + DPLL refutation
   7  Router
   8  Sanity tests (every input style)
   9  Load public datasets
  10  Final accuracy and inference time
  11  Example reasoning traces
  12  Create submission.csv
  13  STANDALONE CELL (run on a private test file)


6. SETTINGS (Section 0 / top of the last cell)
----------------------------------------------
  TEST_PATH        test file to read
  SUBMISSION_PATH  output file (default: submission.csv)
  DEFAULT_ANSWER   answer when no engine can read a question (False)
  NL_CLOSED_WORLD  True: English "X is not Y" holds when "X is Y" cannot be
                   derived (RuleTaker convention). Set False for open world.
  DPLL_MAX_STEPS, GROUND_LIMIT, CB_MAX_ROOTS
                   safety limits that keep the run time low
