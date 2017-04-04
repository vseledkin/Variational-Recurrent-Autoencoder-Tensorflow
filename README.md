# Gerating Sentences from a Continuous Space

Tensorflow implementation of [Generating Sentences from a Continuous Space](https://arxiv.org/abs/1511.06349).

## Prerequisites

1. Python packages:
    - Python 3.4 or higher
    - Tensorflow r0.12
    - Numpy

2. Clone this repository:
```shell=
git clone https://github.com/Chung-I/Variational-Recurrent-Autoencoder-Tensorflow.git
```

## Usage


Training:
```shell=
python vrae.py  --model_dir models --do train --new True
```

Reconstruct:
```shell=
python vrae.py --model_dir models --do reconstruct --new False --input input.txt --output output.txt
```

Sample:
```shell=
python vrae.py --model_dir models --do sample --new False --input input.txt --output output.txt
```

Interpolate:
```shell=
python vrae.py --model_dir models --do interpolate --new False --input input.txt --output output.txt
```

`model_dir`: The location of the config file `config.json` and the checkpoint file.

`do`: Accept 4 values: `train`, `encode_decode`, `sample`, or `interpolate`.

`new`: create models with fresh parameters if set to `True`; else read model parameters from checkpoints in `model_dir`.

## config.json

Hyperparameters are not passed from command prompt like that in [tensorflow/models/rnn/translate/translate.py](https://github.com/tensorflow/tensorflow/blob/r0.12/tensorflow/models/rnn/translate/translate.py). Instead, [vrae.py](https://github.com/Chung-I/Variational-Recurrent-Autoencoder-Tensorflow/blob/master/vrae.py) reads hyperparameters from [config.json](https://github.com/Chung-I/Variational-Recurrent-Autoencoder-Tensorflow/blob/master/models/config.json) in `model_dir`.

Below are hyperparameters in [config.json](https://github.com/Chung-I/Variational-Recurrent-Autoencoder-Tensorflow/blob/master/models/config.json):

- `model`:
    - `size`: embedding size, and encoder/decoder state size.
    - `latent_dim`: latent space size.
    - `in_vocab_size`: source vocabulary size.
    - `out_vocab_size`: target vocabulary size.
    - `data_dir`: path to the corpus.
    - `num_layers`: number of layers for encoder and decoder.
    - `use_lstm`: use lstm for encoder and decoder or not. Use `BasicLSTMCell` if set to `True`; else `GRUCell` is used.
    - `buckets`: A list of pairs of [input size, output size] for each bucket.
    - `bidirectional`: `bidirectional_rnn` is used if set to `True`.
    - `probablistic`: variance is set to zero if set to `False`.
    - `orthogonal_initializer`: `orthogonal_initializer` is used if set to `True`; else `uniform_unit_scaling_initializer` is used.
    - `iaf`: [inverse autoregressive flow](https://github.com/openai/iaf) is used if set to `True`.
    - `activation`: activation for encoder-to-latent layer and latent-to-decoder layer.
        - `elu`: exponential linear unit.
        - `prelu`: parametric linear unit. (default)
        - `None`: linear.
- `train`:
    - `batch_size`
    - `beam_size`: beam size for decoding. __Warning__: beam search is still under implementation. `NotImplementedError` would be raised if `beam_size` is set to be greater than 1.
    - `learning_rate`: learning rate parameter passed into `AdamOptimizer`.
    - `steps_per_checkpoint`: save checkpoint every `steps_per_checkpoint` steps.
    - `anneal`: do [KL cost annealing](https://aclweb.org/anthology/K/K16/K16-1002.pdf#page=4) if set to `True`.
    - `kl_rate_rise_factor`: KL term weight is increasd by this much every `steps_per_checkpoint` steps.
    - `max_train_data_size`: Limit on the size of training data (0: no limit).
    - `feed_previous`: If `True`, only the first of decoder_inputs will be
      used (the "GO" symbol), and all other decoder inputs will be generated by: `next = embedding_lookup(embedding, argmax(previous_output))`. In effect, this implements a greedy decoder. It can also be used during training to emulate http://arxiv.org/abs/1506.03099. If `False`, `decoder_inputs` are used as given (the standard decoder case).
    - `kl_min`: the [minimum information constraint](https://arxiv.org/pdf/1606.04934v1.pdf#page=7). Should be a non-negative float (where 0 is no constraint).
    - `max_gradient_norm`: gradients will be clipped to maximally this norm.
    - `word_dropout_keep_prob`: probability of  randomly replacing some fraction of the conditioned-on word tokens with the generic unknown word token `UNK`. when equal to 0, the decoder sees no input.

- reconstruct:
    - `feed_previous`
    - `word_dropout_keep_prob`
- sample:
    - `feed_previous`
    - `word_dropout_keep_prob`
    - `num_pts`: sample `num_pts` points.
- interpolate:
    - `feed_previous`
    - `word_dropout_keep_prob`
    - `num_pts`: sample `num_pts` points.

## Data

Penn TreeBank corpus is included in the repo. We also provide a Chinese poem corpus, which can be download [here](https://drive.google.com/open?id=0B08WmZIVGFtGclpleFpiV1BxeTA). A model trained on the above Chinese peom corpus can be download [here](https://drive.google.com/open?id=0B08WmZIVGFtGc2J3N3lZeHMycFU). The corresponding vocabulary file is [here](https://drive.google.com/drive/folders/0B08WmZIVGFtGSVZnUU9qbHNtMEk).
