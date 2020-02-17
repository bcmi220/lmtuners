# Discriminative Language Modeling

The task is based on [ELECTRA](https://openreview.net/forum?id=r1xMH1BtvB).

![ELECTRA diagram](/assets/electra_diagram.png)

The trainer is written on top of [pytorch-lightning](https://github.com/PyTorchLightning/pytorch-lightning),
so it supports all of their features for checkpointing, accumulating gradients, multi-gpu, etc...

The generator can be any masked language model (aka any `*ForMaskedLM` model from [huggingface/transformers](https://github.com/huggingface/transformers)),
and similarly, the discriminator can be any `*ForSequenceClassification` model.

## Notes

- This is still very much in development.
- Expected dataset format: `data_path` points to a directory containing:
        
        ```
        data_dir/
            train/
                text1.txt
                text2.txt
            val/
                text3.txt
            test/
                text4.txt
        ```

    where each text file is consumed line-by-line.
- We currently use `tokenizers==0.2.1` directly.  `BertTokenizerFast` was not stable at time of dev.
- pytorch-lightning supports distributed training but this doesn't yet because:
    - 1. Tokenizers aren't pickleable so we need to fix how they're passed around
    - 2. I don't have the resources to test it.
- I wrote this as a trainer and not as just a one-off script because the original ELECTRA implementation
    recommends you to tie weights, and do some abnormal transformations on the models.  You should write your 
    own script that does this and then invoke this trainer [(example)](/experiments/electra_small)
- Uses LAMB optimizer by default and there's no configurability for that yet. 