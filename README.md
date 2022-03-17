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

