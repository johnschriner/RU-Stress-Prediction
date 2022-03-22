# RU-Stress-Prediction
using Zaliznjak's dictionary and stresscodes I use FairSeq to predict Russian stress

## The Data
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
44874/301472 = 14%
поуро́дливей = uglier
поуде́льнее = more private<br>
понорма́льней = more normal<br>
понебезопа́сней = more secure<br>
починне́е = better<br>
поанорма́льнее = more abnormal<br>
поненорма́льнее = more abnormal<br>


Verbs:
Because the data takes into account morphology, many words, particularly verbs have 10 or more entries.  <br>
шосси́ровавшей (on the road; to highway(?)) has 48 entries.  эшелонировавшие (to arrange in echelon formation) has 24 entries.<br>
This could skew the training as it gives more weigh to certain stress placement, and even provides exact copies in dev and test that the NN was trained on.<br>






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
Source: а х н у в ш и м ахнуть<br>
Target: а́ х н у в ш и м ахнуть<br>
<br>
Stress Prediction with morphological feature - Given the word's morphological features, predict the stress placement<br>
n.b. Word Error Rate (WER) will be calculated on predicting the stress, we'll note the errors in copying the morph features.<br>
Source: а х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>
Target: а́ х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>
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
  
  
## Experiment01
### Baseline Prediction - Given the grapheme without a stress, predict stress placement <br>
Source: а х н у в ш и м<br>
Target: а́ х н у в ш и м<br>
  
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

  ```  
Correctly predicted:
S-26026    э к р а н е
T-26026    э к р а ́ н е
H-26026    -0.21708765625953674    э к р а ́ н е
D-26026    -0.21708765625953674    э к р а ́ н е

Incorrectly predicted:
S-2786    о р у щ е м
T-2786    о р у ́ щ е м
H-2786    -0.13091009855270386    о ́ р у щ е м
D-2786    -0.13091009855270386    о ́ р у щ е м
```
  
## Experiment02
### Stresscode Prediction α - Trained on a grapheme and its calculated stresscode, predict the stresscode<br>
Source: а х н у в ш и м<br>
Target: 2<br>
<br>
```
Correctly predicted (электросталеплави́льными):
S-9651	э л е к т р о с т а л е п л а в и л ь н ы м и
T-9651	2
H-9651	-0.3887440264225006	2
D-9651	-0.3887440264225006	2

Incorrectly predicted (и́бисам):
S-5229	и б и с а м
T-5229	2
H-5229	-0.44318196177482605	1
D-5229	-0.44318196177482605	1
```  
`WER = 25.81`<br>
  
## Experiment03
### Stresscode Prediction β - Given the grapheme with a trailing stresscode, predict stress placement.<br>
Source: а х н у в ш и м 2<br>
Target: а́ х н у в ш и м 2<br>
<br>
```
Correctly predicted:
S-17488	а н г е л ы 2
T-17488	а ́ н г е л ы 2
H-17488	-0.12254472076892853	а ́ н г е л ы 2
D-17488	-0.12254472076892853	а ́ н г е л ы 2
  
Incorrectly predicted (due to error in the source data!):
  From Zaliznjak:
  Pronunciation (orthographic with stress): ['с', 'у́', 'ю́', 'щ', 'и', 'й', 'с', 'я']
  Segments (with stress):су́ю́щийся

S-7883	с у ю щ и м с я 2
T-7883	с у ́ ю ́ щ и м с я 2
H-7883	-0.13421708345413208	с у ́ ю щ и м с я 2
D-7883	-0.13421708345413208	с у ́ ю щ и м с я 2

S-7336	о н т о г е н и и 1
T-7336	о н т о г е н и ́ и 1
H-7336	-0.22257477045059204	о н т о г е ́ н и и 1
D-7336	-0.22257477045059204	о н т о г е ́ н и и 1

`WER = 14.94`<br>
```

## Experiment04
### Stress Prediction with lemma feature - Given the word's lemma, predict the stress placement<br>
n.b. Word Error Rate (WER) will be calculated on predicting the stress, and the lemma form should always be copied correctly<br>
Source: а х н у в ш и м ахнуть<br>
Target: а́ х н у в ш и м ахнуть<br>
<br>

```
Correctly predicted:  
S-419	и с к а л а искать
T-419	и с к а ́ л а искать
H-419	-0.11312980204820633	и с к а ́ л а искать
D-419	-0.11312980204820633	и с к а ́ л а искать

Correctly predicted stress, incorrectly predicted lemma:
S-16358	о р г и я х оргия
T-16358	о ́ р г и я х оргия
H-16358	-1.0655725002288818	о ́ р г и я х иней
D-16358	-1.0655725002288818	о ́ р г и я х иней
  
Incorrectly predicted (ё):
S-6917	я с ы р ё м ясырь
T-6917	я с ы р ё м ясырь
H-6917	-1.1663931608200073	я с ы ́ р ё м ярый
D-6917	-1.1663931608200073	я с ы ́ р ё м ярый

Incorrectly predicted:
S-813	и з б н о й избной
T-813	и з б н о ́ й избной
H-813	-0.8719756007194519	и ́ з б н о й шоковый
D-813	-0.8719756007194519	и ́ з б н о й шоковый
```
`WER = 53.20`<br>
High WER due primarily to incorrect lemma prediction.. I will compute WER for stress only.<br>
  
## Experiment05
### Stress Prediction with morphological feature - Given the word's morphological features, predict the stress placement<br>
n.b. Word Error Rate (WER) will be calculated on predicting the stress, not the precise morphological features<br>
Source: а х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>
Target: а́ х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>

```
Correctly predicted:
n.b. the morphological features are being copied properly
S-9938	щ у р о в N Msc Anim Pl Acc
T-9938	щ у ́ р о в N Msc Anim Pl Acc
H-9938	-0.13937562704086304	щ у ́ р о в N Msc Anim Pl Acc
D-9938	-0.13937562704086304	щ у ́ р о в N Msc Anim Pl Acc

Incorrectly predicted:
S-18445	ш п и к и N Msc Inan Pl Acc
T-18445	ш п и ́ к и N Msc Inan Pl Acc
H-18445	-0.1895066499710083	ш п и к и ́ N Msc Inan Pl Acc
D-18445	-0.1895066499710083	ш п и к и ́ N Msc Inan Pl Acc

S-4805	н е н а к о м Pron Neg Loc
T-4805	н е ́ н а к о м Pron Neg Loc
H-4805	-0.3482401371002197	н е н а к о ́ м Pron Neg Loc
D-4805	-0.3482401371002197	н е н а к о ́ м Pron Neg Loc
```
`WER = 25.70`<br>
  
## Conclusions and Discussion
While morphological features may be copied over successfully during training, deriving the lemma isn't nearly as straight-forward.<br>
TO DO: Look at adjectives -- is it true that they are REGULARLY stressed on the first syllable? (Jouravlev and Lupker) No, I don't think so.<br>
TO DO: Summarize Zaliznjak's work and what the data are<br>
TO DO: Look more into consistency, lexical stress.<br>
TO DO: Hall & Sproat's contribution<br>

  

  

  
