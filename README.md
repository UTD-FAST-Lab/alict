# ALiCT: Automated Linguistic Capability Test

This repository contains implementation results for the  capability testing of NLP Models as described in the following paper:

> Paper: Automated Testing Linguistic Capabilities of NLP Models

Table of Contents
=================

   * [ALiCT: Automated Linguistic Capability Test](#alict-automated-linguistic-capability-test)
      * [ALiCT](#alict)
      * [Prerequisites](#prerequisites)
      * [Usage](#usage)
         * [1. Seed and Expanded Test Case Identification](#1-seed-and-expanded-test-case-identification)
         * [2. Testsuite Generation](#2-testsuite-generation)
         * [3. Run Model on The Generated Testsuites](#3-run-model-on-the-generated-testsuites)
         * [4. Analyze The Testing Results](#4-analyze-the-testing-results)
         * [5. Linguistic Capability of Fairness for Sentiment Analysis Task](#5-linguistic-capability-of-fairness-for-sentiment-analysis-task)
         * [6. Evaluation of LLM using ALiCT test cases](#6-evaluation-of-llm-using-alict-test-cases)
      * [Artifact](#artifact)

# ALiCT

ALiCT is an automated linguistic capability-based testing framework for NLP models. In this implementation, we generate testcases for sentiment analysis and hate speech detection. 
ALiCT generates seed test cases using [SST](https://nlp.stanford.edu/sentiment/) and [HateXplain](https://github.com/hate-alert/HateXplain) datasets as the labeled search dataset for the sentiment analysis and hate speech detection respectively.
Results of the ALiCT is [here](_results/README.md). Supplemental artifacts for the results can be downloaded from [here](#artifact)

Prerequisites
=================
This application is written for ```Python>3.7.11```. All requirements are listed in ```requirements.txt```, and they are installed by pip with the following command.
```bash
pip install -r requirements.txt
```

Usage
=================
## 1. Seed and Expanded Test Case Identification
This step is to generate seed and expanded test cases. 
The test cases are generated with the following command:
```bash
cd alict
# Sentiment analysis
python -m python.sa.main \
       --run template \
       --search_dataset sst \
       --search_selection random

# Hate speech detection
python -m python.hs.main \
       --run template \
       --search_dataset hatexplain \
       --search_selection random
```
Output after running the command are in the result directories of `{PROJ_DIR}/_results/templates_sa_sst_random/` and `{PROJ_DIR}/_results/templates_hs_hatexplain_random/` for sentiment analysis and hate speech detection respectively.

For each task and its result directory, the following files are generated:
```bash
_results/
|- templates_sa_sst_random/
|  |- cfg_expanded_inputs_{CKSUM}.json
|  |- seeds_{CKSUM}.json
|  |- exps_{CKSUM}.json
|  |- cksum_map.txt
|- templates_hs_hatexplain_random/
|  |- cfg_expanded_inputs_{CKSUM}.json
|  |- seeds_{CKSUM}.json
|  |- exps_{CKSUM}.json
|  |- cksum_map.txt
``` 
Where `{CKSUM}` represents the checksum value of each unique linguistic capability (LC). The map between the checksum value and its corresponding LC is described in the `cksum_map.txt`.

The `cfg_expanded_inputs_{CKSUM}.json` contains seed test cases, their Context-Free Grammars (CFGs) and expanded production rules from the seed to expanded test cases.

`seeds_{CKSUM}.json` and `exps_{CKSUM}.json` contain results of seed and expanded test cases respectively, and they are generated from the intermediate result file, `cfg_expanded_inputs_{CKSUM}.json`. 


## 2. Testsuite generation
This step is to convert seed and expanded test cases in `.json` format into `.pkl` testsuite files to evaluate NLP models using huggingface pipelines with the format of checklist test cases. You can run it by executing the following command:
```bash
cd alict
# Sentiment analysis
python -m python.sa.main \
       --run testsuite \
       --search_dataset sst \
       --search_selection random

# Hate speech detection
python -m python.hs.main \
       --run testsuite \
       --search_dataset hatexplain \
       --search_selection random
```
Output testsuite files in `.pkl` in the result directories (the result directories of `{PROJ_DIR}/_results/test_results_sa_sst_random/` and `{PROJ_DIR}/_results/test_results_hs_hatexplain_random/` for sentiment analysis and hate speech detection respectively) are the following:

```bash
_results/
|- test_results_sa_sst_random/
|  |- sa_testsuite_seeds_{CKSUM}.pkl
|  |- sa_testsuite_exps_{CKSUM}.pkl
|- test_results_hs_hatexplain_random/
|  |- hs_testsuite_seeds_{CKSUM}.pkl
|  |- hs_testsuite_exps_{CKSUM}.pkl
```

## 3. Run Model on The Generated Testsuites
This step is to run the model on our generated test cases. You can run it by the following command:
```bash
cd alict
# Sentiment analysis
python -m python.sa.main \
       --run testmodel \
       --search_dataset sst \
       --search_selection random

# Hate speech detection
python -m python.hs.main \
       --run testmodel \
       --search_dataset hatexplain \
       --search_selection random
```
Then, the test results are written in `test_results.txt`
```bash
_results/
|- test_results_sa_sst_random/
|  |- sa_testsuite_seeds_{CKSUM}.pkl
|  |- sa_testsuite_exps_{CKSUM}.pkl
|  |- test_results.txt
|- test_results_hs_hatexplain_random/
|  |- hs_testsuite_seeds_{CKSUM}.pkl
|  |- hs_testsuite_exps_{CKSUM}.pkl
|  |- test_results.txt
```

## 4. Analyze The Testing Results
This step is to analyze the reults from [Step 3](#3-run-model-on-the-generated-testsuites) and get the test results into `test_result_analysis.json`. The test results are ``` The file is parsed version of ``` test_results.txt.
You can run it by the following command:
```bash
cd s2lct
# Sentiment analysis
python -m python.sa.main \
       --run analyze \
       --search_dataset sst \
       --search_selection random

# Hate speech detection
python -m python.hs.main \
       --run analyze \
       --search_dataset hatexplain \
       --search_selection random
```

Output results are `test_result_analysis.json`.
```bash
_results/
|- test_results_sa_sst_random/
|  |- sa_testsuite_seeds_{CKSUM}.pkl
|  |- sa_testsuite_exps_{CKSUM}.pkl
|  |- test_results.txt
|  |- test_result_analysis.json
|- test_results_hs_hatexplain_random/
|  |- hs_testsuite_seeds_{CKSUM}.pkl
|  |- hs_testsuite_exps_{CKSUM}.pkl
|  |- test_results.txt
|  |- test_result_analysis.json
```

## 5. Linguistic Capability of Fairness for Sentiment Analysis Task
This step is to run the aforementioned steps for the LC of fairness for the sentiment analysis task.
```bash
# 1. Seed and Expanded Test Case Identification
python -m python.sa.main \
       --run template_fairness \
       --search_dataset sst \
       --syntax_selection random \
       --num_seeds -1 \
       --num_trials 1

# 2. Testsuite Generation
python -m python.sa.main \
       --run testsuite_fairness \
       --search_dataset sst \
       --syntax_selection random \
       --num_seeds -1 \
       --num_trials 1

# 3. Run Model on The Generated Testsuites
python -m python.sa.main \
       --run testmodel_fairness \
       --search_dataset sst \
       --syntax_selection random \
       --num_seeds -1 \
       --num_trials 1

# 4. Analyze The Testing Results
python -m python.sa.main \
       --run analyze_fairness \
       --search_dataset sst \
       --syntax_selection random \
       --num_seeds -1 \
       --num_trials 1
```

Output testsuite files are in the result directories (the result directories of `{PROJ_DIR}/_results/test_results_sa_sst_random_for_fairness/` and `{PROJ_DIR}/_results/test_results_hs_hatexplain_random_for_fairness/` for sentiment analysis and hate speech detection respectively) are the following:
```bash
_results/
|- test_results_sa_sst_random_for_fairness/
|  |- sa_testsuite_fairness_seeds_dbc8046.pkl
|  |- sa_testsuite_fairness_exps_dbc8046.pkl
|  |- test_results_fairness.txt
|  |- test_result_fairness_analysis.json
|- templates_hs_hatexplain_random_for_fairness/
|  |- hs_testsuite_fairness_seeds_dbc8046.pkl
|  |- hs_testsuite_fairness_exps_dbc8046.pkl
|  |- test_results_fairness.txt
|  |- test_result_fairness_analysis.json
```

## 6. Evaluation of LLM using ALiCT test cases
This step is to run the aforementioned steps for evaluating Large Language Model (LLM), GPT3.5 model in this implementation, over LCs.
```bash
# Sentiment analysis
## 1. Run LLM on ALiCT test cases
python -m python.sa.main \
       --run testmodel_tosem \
       --search_dataset sst \
       --syntax_selection random

## 2. Analysis of result
python -m python.sa.main \
       --run analyze_tosem \
       --search_dataset sst \
       --syntax_selection random \
       --num_seeds -1 \
       --num_trials 1

# Hate speech detection
## 1. Run LLM on ALiCT test cases
python -m python.hs.main \
       --run testmodel_tosem \
       --search_dataset hatexplain \
       --syntax_selection random

## 2. Analysis of result
python -m python.hs.main \
       --run analyze_tosem \
       --search_dataset hatexplain \
       --syntax_selection random \
       --num_seeds -1 \
       --num_trials 1
```
Output results are as follows:
```bash
_results/
|- test_results_sa_sst_random/
|  |- sa_testsuite_tosem_seeds_{CKSUM}.pkl
|  |- sa_testsuite_tosem_exps_{CKSUM}.pkl
|  |- test_results_tosem.txt
|  |- test_result_tosem_analysis.json
|- test_results_hs_hatexplain_random/
|  |- hs_testsuite_tosem_seeds_{CKSUM}.pkl
|  |- hs_testsuite_tosem_exps_{CKSUM}.pkl
|  |- test_results_tosem.txt
|  |- test_result_tosem_analysis.json
```

Artifact
=================
Supplemental artifacts for the results can be downloaded from [here](https://utdallas.box.com/s/ghhjqxyo2zsf7te14h2se4kmazx059zw)