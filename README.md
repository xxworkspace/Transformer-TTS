# Transformer-TTS
- Implementation of ["Neural Speech Synthesis with Transformer Network"](https://arxiv.org/abs/1809.08895)  
- This is implemented for [FastSpeech](https://github.com/Deepest-Project/FastSpeech)  
  

## Training  
1. Download and extract the [LJ Speech dataset](https://keithito.com/LJ-Speech-Dataset/)  
2. Make `preprocessed` folder in LJSpeech directory and make `char_seq` & `phone_seq` & `melspectrogram` folder in it  
3. Set `data_path` in `hparams.py` as the LJSpeech folder  
4. Using `prepare_data.ipynb`, prepare melspectrogram and text (converted into indices) tensors.  
5. `python train.py`  

## Training curve (Orange: character / Blue: phoneme)  
- Stop prediction loss (train / val)  
<img src="figures/train_bce_loss.JPG" height="200"> <img src="figures/val_bce_loss.JPG" height="200">  
- Guided attention loss (train / val)    
<img src="figures/train_guide_loss.JPG" height="200"> <img src="figures/val_guide_loss.JPG" height="200">  
- L1 loss (train / val)    
<img src="figures/train_mel_loss.JPG" height="200"> <img src="figures/val_mel_loss.JPG" height="200">  

## Alignments (Left: character / Right: phoneme)  
- Encoder Alignments  
<img src="figures/enc_alignments.JPG" height="600">  
- Decoder Alignments  
<img src="figures/dec_alignments.JPG" height="600">  
- Encoder-Decoder Alignments  
<img src="figures/enc_dec_alignments.JPG" height="600">  
- Melspectrogram (target / before / after POSTNET)  
<img src="figures/melspec.JPG" height="600">  
- Stop prediction  
<img src="figures/gate_prediction.JPG" height="200">  

## Audio Samples    
You can hear the audio samples [here](https://leeyoonhyung.github.io/Transformer-TTS/)

## Notice  
1. Unlike the original paper, I didn't use the encoder-prenet following [espnet](https://github.com/espnet/espnet)  
2. I apply additional ["guided attention loss"](https://arxiv.org/pdf/1710.08969.pdf) to the two heads of the last two layers  
3. Batch size is important, so I use gradient accumulation  
4. You can also use DataParallel. Change the `n_gpus`, `batch_size`, `accumulation` appropriately.  

## TODO
1. Dynamic batch  

## Fastspeech  
1. For fastspeech, generated melspectrograms and attention matrix should be saved for later.  
1-1. Set `teacher_path` in `hparams.py` and make `alignments` and `targets` directories there.  
1-2. Using `prepare_fastspeech.ipynb`, prepare alignmetns and targets.  
  
2. To draw attention plots for every each head, I change return values of the "torch.nn.functional.multi_head_attention_forward()"  
```python
#before
return attn_output, attn_output_weights.sum(dim=1) / num_heads  

#after  
return attn_output, attn_output_weights
```  
3. Among `num_layers*num_heads` attention matrices, the one with the highest focus rate is saved.  

## Reference
1.NVIDIA/tacotron2: https://github.com/NVIDIA/tacotron2  
2.espnet/espnet: https://github.com/espnet/espnet  
3.soobinseo/Transformer-TTS: https://github.com/soobinseo/Transformer-TTS
