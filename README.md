# NLP_Italian_amylo_EHR
Repository to assess transformer-based NLP models for structuring Italian EHRs, focusing on improving clinical feature extraction and patient classificationin cardiac settings.

# NER

Currently the NER is based on the MULTI-CONER NER baseline https://github.com/amzn/multiconer-baseline .

## Setup Environment

Create a virtual environment:

`python3.9 -m venv .env`

Activate the virtual environment

`source .env/bin/activate`

Install the required packages

`pip install -r multiconer-baseline/custom_requirements.txt`

## Check the GPUs state

```
nvidia-smi
```

## Makefile .

The Makefile, in the current directory, can be used to simplify the execution of some commands.

### Variables .

Currently the available variables, and their default values, are the following:

```
DATASET_DIR=data
LAN=it

CORPUS=b

TRAINSET=$(DATASET_DIR)/train.$(CORPUS).conll
DEVSET=$(DATASET_DIR)/dev.$(CORPUS).conll
TESTSET=$(DATASET_DIR)/test.$(CORPUS).conll

GPU=2
N_GPUS=1
EPOCHS=20

MODEL_NAME=xlm_roberta_base
ENCODER_MODEL=xlm-roberta-base

OUT_DIR=experiments

BATCH=64
LR=0.0001
```

### Dataset

Place the dataset files into the `data` directory, to achieve a similar setup:

```
Proximity_AI/ner$ ls data/
anamnesi.a.iob  anamnesi.b.iob  anamnesi.txt  esami.a.iob  esami.b.iob  esami.txt
```

Create the train, dev and test files:

```
make CORPUS=a data/train.a.conll
```

and

```
make CORPUS=b data/train.b.conll
```

Now the data directory contains the following files:

```
Proximity_AI/ner$ ls data/
anamnesi.a.iob  anamnesi.b.iob  anamnesi.txt  dev.a.conll  dev.b.conll  esami.a.iob  esami.b.iob  esami.txt  test.a.conll  test.b.conll  train.a.conll  train.b.conll
```

### Training a model

The following command:
```
make experiments/xlm_roberta_base_lr0.0001_ep20_batch64
```

run the following command:
```
export CUDA_VISIBLE_DEVICES="2";  train_model.py --iob_tagging ris --train data/train.b.conll --dev data/dev.b.conll --out_dir experiments/xlm_roberta_base_lr0.0001_ep20_batch64 --model_name xlm_roberta_base --gpus 1 --epochs 20 --encoder_model xlm-roberta-base --batch_size 64 --lr 0.0001
```

It is possible to change some Makefile variable passing the variable name and the value:

```
make GPU=3 MODEL_NAME=test_model experiments/test_model_lr0.0001_ep20_batch64 -n
export CUDA_VISIBLE_DEVICES="3";  train_model.py --iob_tagging ris --train data/train.b.conll --dev data/dev.b.conll --out_dir experiments/test_model_lr0.0001_ep20_batch64 --model_name test_model --gpus 1 --epochs 20 --encoder_model xlm-roberta-base --batch_size 64 --lr 0.0001
```

Redirecting the ouput log to a file and running the process in background can be usefull:

```
make GPU=3 MODEL_NAME=test_model experiments/test_model_lr0.0001_ep20_batch64 &> make.out &
```

