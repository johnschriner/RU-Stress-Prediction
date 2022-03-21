# RU-Stress-Prediction
using Zaliznjak's dictionary and stresscodes I use FairSeq to predict Russian stress

## Stresscodes
Each grapheme has been given an associated stresscode:<p>

0 for stress on the last syllable, oxytone stress<br>
1 for stress on the penultimate syllable, paroxytone stress<br>
2 for stress on the third-from-last syllable, proparoxytone stress<br>
3+ for stress towards the beginning of the word<br>
<br>
-1 for words without explicit stress in the dataset<br>
<br>
## There will be five experiments:<p>

Baseline Prediction - Given the grapheme without a stress, predict stress placement <br>
n.b. in this notation (and depending on your device), the accent is on the preceding vowel (e.g. on the \<a>\)<br>
Source: а х н у в ш и м <br>
Target: а́ х н у в ш и м <br>
<br>
Stresscode Prediction α - Trained on a grapheme and its calculated stresscode, predict the stresscode<br>
Source: а х н у в ш и м<br>
Target: 2<br>
<br>
Stresscode Prediction β - Given the grapheme with a trailing stresscode, predict stress placement.<br>
Source: а х н у в ш и м 2<br>
Target: а́ х н у в ш и м 2<br>
<br>
Stress Prediction with lemma feature - Given the word's lemma, predict the stress placement<br>
n.b. Word Error Rate (WER) will be calculated on predicting the stress, not the lemma form<br>
Source: а х н у в ш и м ахнуть<br>
Target: а́ х н у в ш и м ахнуть<br>
<br>
Stress Prediction with morphological feature - Given the word's morphological features, predict the stress placement<br>
n.b. Word Error Rate (WER) will be calculated on predicting the stress, not the precise morphological features<br>
Source: а х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>
Target: а́ х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>
  
|INFO|Number of Lines|
|---|---|
|INFO: Total set:|	301,472 lines|
|INFO: Train set:|	241,177 lines|
|INFO: Development set:|	30,147 lines|
|INFO: Test set:|	30,148 lines|
  
  <code>wc -l *.s</code>
  
  > 30147 dev.ru.s<br>
  > 30148 test.ru.s<br>
  > 241177 train.ru.s<br>
  > 301472 total<br>

  <code>wc -l *.t</code>
   
  > 30147 dev.ru.t<br>
  > 30148 test.ru.t<br>
  > 241177 train.ru.t<br>
  > 301472 total<br>

```
  fairseq-preprocess \
      --source-lang ru.s \
      --target-lang ru.t \
      --trainpref train \
      --validpref dev \
      --testpref test \
      --tokenizer space \
      --thresholdsrc 2 \
      --thresholdtgt 2
```

```
fairseq-train \
    data-bin \
    --source-lang ru.s \
    --target-lang ru.t \
    --encoder-bidirectional \
    --seed 230 \
    --arch lstm \
    --dropout 0.2 \
    --lr .001 \
    --max-update 800\
    --no-epoch-checkpoints \
    --batch-size 50 \
    --clip-norm 1 \
    --label-smoothing .1 \
    --optimizer adam \
    --clip-norm 1 \
    --criterion label_smoothed_cross_entropy\
    --encoder-embed-dim 128 \
    --decoder-embed-dim 128 \
    --encoder-layers 2 \
    --decoder-layers 2 \
```  

```
fairseq-generate \
    data-bin \
    --source-lang ru.s \
    --target-lang ru.t \
    --path checkpoints/checkpoint_best.pt \
    --gen-subset valid \
    --beam 8 \
    > predictions.txt  
```
`WER = 26.41`<br>
  
