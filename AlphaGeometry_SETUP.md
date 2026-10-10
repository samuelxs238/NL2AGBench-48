# Running AlphaGeometry Locally (AG4Masses)

This guide walks through installing [AG4Masses](https://github.com/foodtruck-53/ag4masses), a community-maintained distribution of AlphaGeometry, so you can execute the NL2AGBench translations yourself. It was tested on Ubuntu with Python 3.10.9.

Two errors commonly come up during setup. Both are covered in [Troubleshooting](#troubleshooting) at the end, and the steps below already include the fixes, so following them in order should avoid both.

## 1. Choose file locations

```bash
# Directory where output files go
export TESTDIR=$HOME/ag4mtest
# Directory containing AG4Masses source files
export AG4MDIR=$HOME/ag4masses
# Directory containing external libraries, including ag_ckpt_vocab and meliad
export AGLIB=$HOME/aglib
```

> **Note:** Step 4 restarts your shell, which clears these variables. To keep them across sessions, add the three `export` lines to your `~/.bashrc`, or re-run them after Step 4.

## 2. Download source and data files

```bash
cd $HOME
git clone https://github.com/foodtruck-53/ag4masses.git

mkdir -p $AGLIB
cd $AGLIB
git clone https://github.com/google-research/meliad

# Pin meliad to an older commit (see Troubleshooting: decoder_stack ImportError)
cd $AGLIB/meliad
git checkout e8af054

mkdir -p $AGLIB/ag_ckpt_vocab
mkdir -p $TESTDIR
```

### Model weights and vocabulary

Download the weights and vocabulary directly into `$AGLIB/ag_ckpt_vocab` from the [Hugging Face mirror](https://huggingface.co/Wauplin/alphageometry) of the official AlphaGeometry files:

```bash
cd $AGLIB/ag_ckpt_vocab
wget https://huggingface.co/Wauplin/alphageometry/resolve/main/checkpoint_10999999
wget https://huggingface.co/Wauplin/alphageometry/resolve/main/geometry.757.model
wget https://huggingface.co/Wauplin/alphageometry/resolve/main/geometry.757.vocab
```

The checkpoint is about 1.2 GB. The model weights and vocabulary are released by Google DeepMind as part of [AlphaGeometry](https://github.com/google-deepmind/alphageometry). If the mirror is ever unavailable, the original files are in the Google Drive folder at https://bit.ly/alphageometry.

## 3. Install Linux packages

```bash
sudo apt update
sudo apt install -y git curl make build-essential libssl-dev zlib1g-dev \
  libbz2-dev libreadline-dev libsqlite3-dev libffi-dev liblzma-dev tk-dev \
  xz-utils ca-certificates
```

## 4. Install Python 3.10 and create a virtual environment

AlphaGeometry's pinned dependencies require Python 3.10. Use `pyenv` to install it without replacing your system Python.

```bash
curl https://pyenv.run | bash

echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
exec $SHELL
```

Re-run the `export` lines from Step 1 if you did not add them to `~/.bashrc`, then:

```bash
pyenv install 3.10.9
cd $AG4MDIR
pyenv local 3.10.9

python -m venv .venv
source .venv/bin/activate
python --version
```

The output should be `Python 3.10.x`.

## 5. Install Python dependencies

`jaxlib==0.4.6` is no longer on PyPI, so install `jax` and `jaxlib` from Google's release index first (see Troubleshooting: jaxlib not found):

```bash
pip install --upgrade pip
pip install --find-links https://storage.googleapis.com/jax-releases/jax_releases.html jaxlib==0.4.6 jax==0.4.6
```

Then install the remaining requirements:

```bash
cd $AG4MDIR/alphageometry
pip install --require-hashes --no-deps -r requirements.txt
```

## 6. Run the tests

```bash
cd $TESTDIR
$AG4MDIR/utils/run_tests.sh
```

The last test may fail with a small numerical mismatch. **This is expected and safe to ignore**; it is caused by floating-point differences with the external meliad library, and the test name itself (`test_lm_score_may_fail_numerically_for_external_meliad`) anticipates it.

```
Traceback (most recent call last):
  File "/home/ubuntu/ag4masses/alphageometry/lm_inference_test.py", line 82, in test_lm_score_may_fail_numerically_for_external_meliad
    self.assertEqual(
AssertionError: Lists differ: [-1.1633697, -1.122621] != [-1.1860729455947876, -1.1022869348526]

First differing element 0:
-1.1633697
-1.1860729455947876

- [-1.1633697, -1.122621]
+ [-1.1860729455947876, -1.1022869348526]

----------------------------------------------------------------------
Ran 2 tests in 105.163s

FAILED (failures=1)
```

## Troubleshooting

### Error: `jaxlib==0.4.6` not found (when installing requirements)

```
ERROR: Could not find a version that satisfies the requirement jaxlib==0.4.6 (from versions: 0.4.17, 0.4.18, 0.4.19, 0.4.20, 0.4.21, 0.4.22, 0.4.23, 0.4.24, 0.4.25, 0.4.26, 0.4.27, 0.4.28, 0.4.29, 0.4.30, 0.4.31, 0.4.32, 0.4.33, 0.4.34, 0.4.35, 0.4.36, 0.4.38, 0.5.0, 0.5.1, 0.5.3, 0.6.0, 0.6.1, 0.6.2)
ERROR: No matching distribution found for jaxlib==0.4.6
```

**Cause:** Older `jaxlib` releases have been removed from PyPI, but Google still hosts them on its own release index. (CUDA builds are listed at https://storage.googleapis.com/jax-releases/jax_cuda_releases.html.)

**Fix:** Install `jax` and `jaxlib` from Google's index, then install the rest of the requirements:

```bash
pip install --upgrade pip
pip install --find-links https://storage.googleapis.com/jax-releases/jax_releases.html jaxlib==0.4.6 jax==0.4.6

cd $AG4MDIR/alphageometry
pip install --require-hashes --no-deps -r requirements.txt
```

### Error: `cannot import name 'decoder_stack' from 'transformer'` (when running tests)

```
Traceback (most recent call last):
  File "/home/ubuntu/ag4masses/alphageometry/alphageometry_test.py", line 21, in <module>
    import alphageometry
  File "/home/ubuntu/ag4masses/alphageometry/alphageometry.py", line 29, in <module>
    import lm_inference as lm
  File "/home/ubuntu/ag4masses/alphageometry/lm_inference.py", line 20, in <module>
    import models  # pylint: disable=unused-import
  File "/home/ubuntu/ag4masses/alphageometry/models.py", line 20, in <module>
    import decoder_stack
  File "/home/ubuntu/ag4masses/alphageometry/decoder_stack.py", line 21, in <module>
    from transformer import decoder_stack
ImportError: cannot import name 'decoder_stack' from 'transformer' (/home/ubuntu/aglib/meliad/transformer/__init__.py)
```

The same `ImportError` repeats for `lm_inference_test.py`. You may also see `FutureWarning: jax.tree_util.register_keypaths is deprecated` messages from flax; those warnings are harmless.

**Cause:** Newer versions of meliad removed or moved `transformer/decoder_stack.py`, which AlphaGeometry still imports.

**Fix:** Check out an older meliad commit that still contains it:

```bash
cd $AGLIB/meliad
git checkout e8af054
```

`run_tests.sh` should now get past this error.
