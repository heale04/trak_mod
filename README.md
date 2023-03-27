[![Python package](https://github.com/MadryLab/trak/actions/workflows/python-package.yml/badge.svg)](https://github.com/MadryLab/trak/actions/workflows/python-package.yml)
<!-- [![arXiv](https://img.shields.io/badge/arXiv-1234.56789-b31b1b.svg?style=flat-square)](https://arxiv.org/abs/1234.56789) -->

[[docs & tutorials]](https://trak.csail.mit.edu/html/index.html)
[[paper]](https://arxiv.org/abs/2303.14186)
[[blog post]](https://gradientscience.org/trak/)
[[website]](https://trak.csail.mit.edu)
[[PyPI]](https://pypi.org/project/traker/)

# TRAK: Attributing Model Behavior at Scale

In our [paper](https://arxiv.org/abs/2303.14186), we introduce a new data attribution method called `TRAK` (Tracing with the
Randomly-Projected After Kernel). Using `TRAK`, you can make  accurate
counterfactual predictions (e.g., answers to questions of the form “what would
happen to this prediction if these examples are removed from the training set?").
Computing TRAK scores comes at a small fraction of the cost of prior data attribution methods
that are comparably effective (e.g., TRAK is 2-3 orders of magnitude cheaper
than comparable methods, on CIFAR-10 and QNLI):

![Main figure](/docs/assets/main_figure.png)

## Usage

Check [our docs](https://trak.csail.mit.edu/html) for more detailed examples and
tutorials on how to use `TRAK`.  Below, we provide a brief blueprint of the
steps needed to get `TRAK` attribution scores with our API.

### Make a `TRAKer` instance

```python
from trak import TRAKer

model, checkpoints = ...
train_loader = ...

traker = TRAKer(model=model, task='image_classification', train_set_size=...)
```

### Compute `TRAK` features on training data

```python
for model_id, checkpoint in enumerate(checkpoints):
  traker.load_checkpoint(ckeckpoint, model_id=model_id)
  for batch in loader_train:
      traker.featurize(batch=batch, ...)
traker.finalize_features()
```

### Compute `TRAK` scores for target examples

```python
targets_loader = ...

for model_id, checkpoint in enumerate(checkpoints):
  traker.start_scoring_checkpoint(ckeckpoint, model_id=model_id, num_targets=...)
  for batch in targets_loader:
    traker.score(batch=batch, ...)

scores = traker.finalize_scores()
```

## Installation

To install the version of our package which contains a fast, custom `CUDA`
kernel for the JL projection step, use
```bash
pip install traker[fast]
```
You will need compatible versions of `gcc` and `CUDA toolkit` to install it. See
the [installation FAQs](https://trak.csail.mit.edu/html/install.html) for tips
regarding this. To install the basic version of our package that requires no
compilation, use
```bash
pip install traker
```


## Citation
If you use this code in your work, please cite using the following BibTeX entry:
```
@inproceedings{park2023trak,
  title = {TRAK: Attributing Model Behavior at Scale},
  author = {Sung Min Park and Kristian Georgiev and Andrew Ilyas and Guillaume Leclerc and Aleksander Madry},
  booktitle = {Arxiv preprint arXiv:2303.14186},
  year = {2023}
}
```

## Maintainers:
[Kristian Georgiev](https://twitter.com/kris_georgiev1)<br>
[Andrew Ilyas](https://twitter.com/andrew_ilyas)<br>
[Guillaume Leclerc](https://twitter.com/gpoleclerc)<br>
[Sung Min Park](https://twitter.com/smsampark)
