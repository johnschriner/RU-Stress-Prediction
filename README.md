# RU-Stress-Prediction
using Zaliznjak's dictionary and stresscodes I use FairSeq to predict Russian stress<br>

## The Data

Parts of Speech:<br>
Adjectives = 97795/301472 = 32.43%<br>
Adverbs = 159/301472 = .05%<br>
Verbs = 141081/301472 = 46.79%<br>
Nouns = 51344/301472 = 17.03%<br>
Determiners = 7668/301472 = 2.54%<br>
Pronoun = 3285/301472 = 1.08%<br>
Pr = 19/301472 = .006%<br>
CC = 10/301472 = .003%<br>
CS = 10/301472 = .003%<br>
Interjection = 84/301472 = .02%<br>


Adjectives:<br>
антисейсми́ческой = anti-seismic<br>
антиле́нинская = anti-Leninist (n.b. Leninist is not in the dictionary)<br>
антиамерика́нск = anti-American (n.b. American is not in the dictionary)<br>
южноамерика́нско = South American<br>
шутовско́ = clownish<br>
экспе́ртна = expert<br>
элега́нтной = elegant<br>
ма́лом = small<br>
у́треннею = morning<br>
экстерриториа́льные = extraterritorial<br>
юлиа́нским = Julian (as in the calendar)<br>
шо́рт = short<br>
языкотво́рческому = language-creative<br>
<br>
Comparative Adjectives:<br>
44874/301472 = 14% of all words<br>
поуро́дливей = uglier
поуде́льнее = more private<br>
понорма́льней = more normal<br>
понебезопа́сней = more secure<br>
починне́е = better<br>
поанорма́льнее = more abnormal<br>
поненорма́льнее = more abnormal<br>


Verbs:<br>
Because the data takes into account morphology, many words, particularly verbs have 10 or more entries.  <br>
шосси́ровавшей (on the road; to highway(?)) has 48 entries.  эшелонировавшие (to arrange in echelon formation) has 24 entries.<br>
This could skew the training as it gives more weigh to certain stress placement, and even provides exact copies in dev and test that the NN was trained on.<br>

Nouns:<br>


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

Experiment01 - Baseline Prediction - Given the grapheme without a stress, predict stress placement <br>
n.b. in this notation (and depending on your device), the accent is on the preceding vowel (e.g. on the \<a>\)<br>
Source: а х н у в ш и м <br>
Target: а́ х н у в ш и м <br>
<br>
Experiment02 - Stresscode Prediction α - Trained on a grapheme and its calculated stresscode, predict the stresscode<br>
Source: а х н у в ш и м<br>
Target: 2<br>
<br>
Experiment03 - Stresscode Prediction β - Given the grapheme with a trailing stresscode, predict stress placement.<br>
n.b. this experiment is to guage the efficacy of the stresscodes; the results should be near-perfect <br>
Source: а х н у в ш и м 2<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment04 - Stress Prediction with lemma feature - Given the word's lemma, predict the stress placement<br>
Source: а х н у в ш и м ахнуть<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment05 - Stress Prediction with POS feature - Given the word's lemma, predict the stresscode<br>
Source: а х н у в ш и м ахнуть<br>
Target: 2   <br>
Experiment06 - Stress Prediction with morphological feature  α - Given all of the word's morphological features, predict the stress placement<br>
n.b. Word Error Rate (WER) will be calculated on predicting the stress, we'll note the errors in copying the morph features.<br>
Source: а х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment07 - Stress Prediction with morphological feature β - Given only word's part of speech feature, predict the stress placement<br>
Source: а х н у в ш и м V<br>
Target: а́ х н у в ш и м<br>
<br>
  
Oxytone Stress:  17111/301472 = 5.67%<br>
Paroxytone Stress:  75196/301472 = 24.94%<br>
Proparoxytone Stress:  101209/301472 = 33.57%<br>
<br>
Fourth syllable from the end:   68878/301472 = 22.84%<br>
Fifth syllable from the end:   31325/301472 = 10.39%<br>
Sixth syllable from the end:  6378/301472 = 2.11%<br>
Seven or more syllables from the end: 302/301472 = .10%<br>

<br>
No stress indicated: 1073/301472 = .35%<br>

Simply predicting Paroxytone (stresscode 1) through the fourth syllable from the end (stresscode 3) is correct for 81.35% of the data.<br>

  
|INFO|Number of Lines|
|---|---|
|INFO: Total set:|	301,472 lines|
|INFO: Train set:|	241,177 lines|
|INFO: Development set:|	30,147 lines|
|INFO: Test set:|	30,148 lines|

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

  ```  
Experiment01 - Baseline Prediction - Given the grapheme without a stress, predict stress placement <br>
n.b. in this notation (and depending on your device), the accent is on the preceding vowel (e.g. on the \<a>\)<br>
Source: а х н у в ш и м <br>
Target: а́ х н у в ш и м <br>
<br>
Experiment02 - Stresscode Prediction α - Trained on a grapheme and its calculated stresscode, predict the stresscode<br>
Source: а х н у в ш и м<br>
Target: 2<br>
<br>
Experiment03 - Stresscode Prediction β - Given the grapheme with a trailing stresscode, predict stress placement.<br>
n.b. this experiment is to guage the efficacy of the stresscodes; the results should be near-perfect <br>
Source: а х н у в ш и м 2<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment04 - Stress Prediction with lemma feature - Given the word's lemma, predict the stress placement<br>
Source: а х н у в ш и м ахнуть<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment05 - Stress Prediction with POS feature - Given the word's lemma, predict the stresscode<br>
Source: а х н у в ш и м ахнуть<br>
Target: 2   <br>
<br>
Experiment06 - Stress Prediction with morphological feature  α - Given all of the word's morphological features, predict the stress placement<br>
n.b. Word Error Rate (WER) will be calculated on predicting the stress, we'll note the errors in copying the morph features.<br>
Source: а х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment07 - Stress Prediction with morphological feature β - Given only word's part of speech feature, predict the stress placement<br>
Source: а х н у в ш и м V<br>
Target: а́ х н у в ш и м<br>
<br>
  
## Conclusions and Discussion
While morphological features may be copied over successfully during training, deriving the lemma isn't nearly as straight-forward.<br>
TO DO: Look at adjectives -- is it true that they are REGULARLY stressed on the first syllable? (Jouravlev and Lupker) No, I don't think so.<br>
TO DO: Look at other POS
TO DO: Summarize Zaliznjak's work and what the data are<br>
TO DO: Look more into consistency, lexical stress.<br>
TO DO: Hall & Sproat's contribution<br>
TO DO: lexicon of stress homographs, a quantitative write-up and appendix<br>
  

  
