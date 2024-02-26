# HumanEval-XL

This repository contains data and evaluation code for the paper "HumanEval-XL: A Multilingual Code Generation Benchmark for Cross-Lingual Natural Language Generalization".

# Dataset
The data is stored in data/program_language/natural_language. We have 80 parallel problems in different languages.  

# Evaluation
## Installation

Check out and install this repository:
```
git clone git@github.com:FloatAI/humaneval-xl.git
cd mxeval
pip install -e mxeval
```

## Dependencies
We provide scripts to help set up programming language dependencies that are used to execute and evaluate using dataset.
(We use the same scripts from https://github.com/amazon-science/mxeval for code generation evaluation)

### Amazon Linux AMI
```
bash language_setup/amazon_linux_ami.sh
```
### Ubuntu
```
bash language_setup/ubuntu.sh
```

# Usage

**This program exists to run untrusted model-generated code. Users are strongly
encouraged not to do so outside of a robust security sandbox. See the comment in
`execution.py` for more information and instructions.**
(We use the same scripts from https://github.com/amazon-science/mxeval for code generation evaluation)

Each sample is formatted into a single line:
```
{"task_id": "Corresponding task ID", "completion": "Completion only without the prompt",
"language": "programming language name"}
```
We provide `python_chinese_generated_samples.jsonl` to illustrate the format. 

Here is nearly functional example code (you just have to provide
`generate_one_completion` to make it work) that saves generated completions to
`samples.jsonl`.
```
from mxeval.data import write_jsonl, read_problems

problems = read_problems()

num_samples_per_task = 200
samples = [
    dict(task_id=task_id, language=problems[task_id]["language"], completion=generate_one_completion(problems[task_id]["prompt"]))
    for task_id in problems
    for _ in range(num_samples_per_task)
]
write_jsonl("samples.jsonl", samples)
```

To evaluate the samples for, e.g., Python, Chinese evaluation, run 
```
evaluate_functional_correctness python_chinese_generated_samples.jsonl --problem_file data/python/Chinese.jsonl
```

Note: Because there is no unbiased way of estimating pass@k when there are fewer
samples than k, the script does not evaluate pass@k for these cases. To
evaluate with other k values, pass `--k <comma-separated-values-here>`. For
other options, see
```
$ evaluate_functional_correctness --help
```
However, we recommend that you use the default values for the rest.

### Credits
We adapted Amazon-science's mxeval package (https://github.com/amazon-science/mxeval) for the evaluation. We thank Amazon for their pioneering effort in this field including the release of the dataset and evaluation code.
