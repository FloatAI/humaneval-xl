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

Each sample is formatted into a single line:
```
{"task_id": "Corresponding task ID", "completion": "Completion only without the prompt",
"language": "programming language name"}
```
We provide `data/mbxp/examples/mbxp_samples.jsonl` to illustrate the format. 

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

To evaluate the samples for, e.g., Java MBJP evaluation, run 
```
evaluate_functional_correctness data/mbxp/examples/mbjp_samples.jsonl --problem_file data/mbxp/mbjp_release_v1.jsonl
```
or to run all languages
```
for lang in mbcpp mbcsp mbgp mbjp mbjsp mbkp mbphp mbplp mbpp mbrbp mbscp mbswp mbtsp;
  do evaluate_functional_correctness --problem_file data/mbxp/${lang}_release_v1.jsonl data/mbxp/examples/${lang}_samples.jsonl;
done
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
We adapted OpenAI's human-eval package (https://github.com/openai/human-eval) for the multi-lingual case. We thank OpenAI for their pioneering effort in this field including the release of the original HumanEval dataset, which we convert to the multi-lingual versions. We also thank Google for their release of the original MBPP Python dataset (https://github.com/google-research/google-research/tree/master/mbpp), which we adapt and convert to other programming languages.
