# RU-Stress-Prediction
using Zaliznjak's dictionary and stresscodes I use FairSeq to predict Russian stress<br>

## The Data

*Parts of Speech:*<br>
Verbs = 141081/301472 = 46.79%<br>
Adjectives = 97795/301472 = 32.43%<br>
Nouns = 51344/301472 = 17.03%<br>
Determiners = 7668/301472 = 2.54%<br>
Pronouns = 3285/301472 = 1.08%<br>
Adverbs = 159/301472 = .05%<br>
Pr = 19/301472 = .006%<br>
CC = 10/301472 = .003%<br>
CS = 10/301472 = .003%<br>
Interjections = 84/301472 = .02%<br>


*Adjectives:*<br>
антисейсми́ческой = anti-seismic<br>
антиле́нинская = anti-Leninist (n.b. Leninist is not in the dictionary)<br>
антиамерика́нск = anti-American (n.b. American is not in the dictionary)<br>
южноамерика́нско = South American<br>
шутовско́ = clownish<br>
эо́ловы = Aeolian<br>
экспе́ртна = expert<br>
элега́нтной = elegant<br>
ма́лом = small<br>
у́треннею = morning<br>
экстерриториа́льные = extraterritorial<br>
юлиа́нским = Julian (as in the calendar)<br>
шо́рт = short<br>
языкотво́рческому = language-creative<br>
<br>
*Comparative Adjectives (Comp):*<br>
44874/301472 = 14% of all words<br>
поуро́дливей = uglier<br>
поуде́льнее = more private<br>
понорма́льней = more normal<br>
понебезопа́сней = more secure<br>
починне́е = better<br>
поанорма́льнее = more abnormal<br>
поненорма́льнее = more abnormal<br>
поглазне́е = more eye-catching<br>
<br>
96771 adjectives (removed unstressed adjectives)<br>
3667 with a stresscode of 0: 3.78%<br>
31554 with a stresscode of 1: 32.6%<br>
42052 with a stresscode of 2: 43.45%<br>
17223 with a stresscode of 3: 17.79%<br>
2022 with a stresscode of 4: 2.08%<br>
241 with a stresscode of 5: .25% <br>
5 with a stresscode of 6: .005%<br>

The adjectives have an average length of 13 characters so our data suggests that even with consonant clusters the majority of adjectives have the stress towards the end of the word or nearer the middle, e.g. понорма́льней <br>

*Verbs:*<br>
Because the data takes into account morphology, many words, particularly verbs have 10 or more entries.  <br>
шосси́ровавшей (on the road; to highway(?)) has 48 entries.  эшелонировавшие (to arrange in echelon formation) has 24 entries.<br>
яровизировавшему (vernalizing, to cool a seed during germination)<br>
шептать (imperfect, 708 distinct morphological entries; шептал = whispered; шепчущему = whispering)
This could skew the training as it gives more weigh to certain stress placement, and even provides exact copies (for morphological experiments) in dev and test that the NN was trained on.<br>
A solution is lexeme-aware splitting of the data.<br>

*Nouns:*<br>
No known or very little known regularity.<br>
We know that nouns that end in -ism have the stress on the и́:<br>
эмпириомони́змом<br>
n.b. Empiriomonism (“Empiriomonizm”) is the fundamental philosophical work of Alexander Bogdanov, published by him in 1906 in St. Petersburg. The book is an attempt at a synthesis of Marxism and positivism. It was sharply criticized by the author's associates (Lenin and Plekhanov) for revisionism and subjective idealism.<br>

## Stresscodes
Each grapheme has been given an associated stresscode:<p>

0 for stress on the last syllable, oxytone stress<br>
1 for stress on the penultimate syllable, paroxytone stress<br>
2 for stress on the third-from-last syllable, proparoxytone stress<br>
3+ for stress towards the beginning of the word<br>
<br>
-1 for words without explicit stress in the dataset<br>
Stress is marked on [ё] although there are 2000 exceptions in the data (e.g. шёлкотка́ной, or with secondary stress, e.g потрё̀хме́сячней)<br>
12790 contain an [ё].  2000 of those also contain an explicit stress: 15.63%<br>
<br>
## There will be eight experiments:<p>
<br>
Experiment 01 - Baseline Prediction - Given the grapheme without a stress, predict stress placement <br>
n.b. in this notation (and depending on your device), the accent is on the preceding vowel (e.g. on the \<a>\)<br>
Source: а х н у в ш и м <br>
Target: а́ х н у в ш и м <br>
<br>
Experiment 02 - Stresscode Prediction α - Given a grapheme, predict the stresscode<br>
Source: а х н у в ш и м<br>
Target: 2<br>
<br>
Experiment 03 - Stresscode Prediction β - Given the grapheme with a trailing stresscode, predict stress placement.<br>
n.b. this experiment is to gauge the efficacy of the stresscodes; the results should be near-perfect <br>
Source: а х н у в ш и м 2<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment 04 - Stress Prediction with lemma feature α- Given the word's lemma, predict the stress placement<br>
Source: а х н у в ш и м ахнуть<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment 05 - Stress Prediction with lemma feature β- Given the word's lemma, predict the stresscode<br>
Source: а х н у в ш и м ахнуть<br>
Target: 2   
<br>
Experiment 06 - Stress Prediction with morphological feature  α - Given all of the word's morphological features, predict the stress placement<br>
Source: а х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment 07 - Stress Prediction with morphological feature β - Given the word's part of speech feature, predict the stress placement<br>
Source: а х н у в ш и м V<br>
Target: а́ х н у в ш и м<br>
<br>
Experiment 08 - Stresscode Prediction with morphological feature - Given the word's part of speech feature, predict the stresscode<br>
Source: а х н у в ш и м V<br>
Target: 2<br>
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
These words are almost entirely hyphenated adjectives <br>
  e.g. покальциево-фосфорнее (pocalcium-phosphorus(?)) and <br>
  подекоративно-демонстрационнее (decorative-demonstrative) <br>
  поумильно-патриархальней (wisely patriarchal)<br>
  эллинско-христианское (Hellenic-Christian)<br>
  They contain no stress information.<br>
  Another example is щенках (puppies), a noun that contains no stress information.<br>
<br>
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
    --seed {variable} \
    --arch lstm \
    --dropout 0.2 \
    --lr .001 \
    --max-update 800\
    --no-epoch-checkpoints \
    --batch-size 5000 \
    --clip-norm 1 \
    --label-smoothing .1 \
    --optimizer adam \
    --clip-norm 1 \
    --criterion label_smoothed_cross_entropy \
    --encoder-embed-dim 128 \
    --decoder-embed-dim 128 \
    --encoder-layers 1 \
    --decoder-layers 1 \
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
<br>
  
## Experiment 01 - Baseline Prediction - Given the grapheme without a stress, predict stress placement <br>
  
n.b. in this notation (and depending on your device), the accent is on the preceding vowel (e.g. on the \<a>\)<br>
Source: а х н у в ш и м <br>
Target: а́ х н у в ш и м <br>
<br>

```
fairseq-train \
    data-bin \
    --source-lang ru.s \
    --target-lang ru.t \
    --encoder-bidirectional \
    --seed {variable} \
    --arch lstm \
    --dropout 0.2 \
    --lr .001 \
    --max-update 800\
    --no-epoch-checkpoints \
    --batch-size 3000 \
    --clip-norm 1 \
    --label-smoothing .1 \
    --optimizer adam \
    --clip-norm 1 \
    --criterion label_smoothed_cross_entropy \
    --encoder-embed-dim 128 \
    --decoder-embed-dim 128 \
    --encoder-layers 1 \
    --decoder-layers 1 \
```
<br>
  
`WER on dev = 22.18`<br>
`WER on test = 22.28`<br>
<br>
|Target |Hypothesis | Notes |
|------|------------|-----------|
|т ё р ш и е|т ё р ш и е|Correctly omitted a stress likely due to the [ё]|
|о п ё р с я|о ́ п ё р с я|Incorrectly added a stress despite the [ё]. n.b. In the corpus, 15.63% of the words containing an [ё] also contain an explicit stress|
|ш т ы р ё м|ш т ы ́ р ё м|While our stresscode would stop when discovering the [ё], this prediction erroneously predicted a stress on [ы]|
|я с н о о ́ к|я ́ с н о о к||
|ш у ́ х е р а|ш у х е ́ р а||
|и ́ з г н а н н ы е|и ́ з г н а н н ы е|Correct with a stress on the first syllable|
|ш п а н о ́ ю|ш п а ́ н о ю||
|У ́ в о д и|И ́ в о д и|????|
|п о г л а з н е ́ е|п о г л а ́ з н е е|Incorrectly predicted a stress before the double [е] ending|
|я с н е ́ я|я ́ с н е я|Incorrectly predicted stress on the onset||
|ш о с с и ́ р о в а в ш е е|ш о с с и ́ р о в а в ш е е||
|ю р о ́ д с т в о в а в ш и м и|ю р о ́ д с т в о в а в ш и м и|Correct.  Having many possible vowels to place the stress, correctly predicted near the beginning|
|н и с р е д ь к о т о ́ р о г о|н и с р е д ь к о т о ́ р о г о|Correct|
|э п и г е н е т и ́ ч е с к о г о|э п и г е н е т и ́ ч е с к о г о|Correct, likely because of a recognized pattern|

<br>
Hypothesis: the longer words tend to be have better success with stress-prediction.<br>

Test contains 30148 words, 1347 contain [ё], 4.46%.<br>
Of these, 223 contained stress: an error 16% of the time, save for very few exceptions.
<br>
<br>
  
## Experiment 02 - Stresscode Prediction α - Given a grapheme, predict the stresscode<br>
  
Source: а х н у в ш и м<br>
Target: 2<br>
<br>
  
`WER on dev = 14.00`<br>
`WER on test = 13.93`<br>
<br>
To recall: Simply predicting Paroxytone (stresscode 1) through the fourth syllable from the end (stresscode 3) is correct for 81.35% of the data.<br>

|Grapheme|Target Stresscode|Hypothesis | Notes |
|------|--------|--------|-----------|
|щ е н к а м |NULL|1|.35% of words contained no stress data|
|ш и т ь я м|0|0|Correct prediction|
|я м и с т а|2|1|Incorrectly predicted the [и] whereas stress is on the first syllable| 
|ш а ф е р а|0|2|Incorrectly predicted the first syllable, where stress was on the last syllable|
|о п у с а х|2|2|Correctly predicted stress on the first syllable|
|у д о ч к у|2|2|Correctly predicted stress on the first syllable|
|и в у ш к у|2|1|Incorrectly predicted stress on the [у]|
<br>
<br>
  
## Experiment 03 - Stresscode Prediction β - Given the grapheme with a trailing stresscode, predict stress placement.<br>
  
n.b. this experiment is to gauge the efficacy of the stresscodes; the results should be very good <br>
Source: а х н у в ш и м 2<br>
Target: а́ х н у в ш и м<br>
<br>
  
`WER on dev = 11.47`<br>
`WER on test = 11.61`<br>
<br>
|Target |Hypothesis | Notes |
|------|------------|-----------|
|п л о х о ́ м|п л о х о ́ м|Correctly predicted|
|ё р ш и к и|ё р ш и к и|Correctly predicted the stress on [ё]|
|к о ̀ е н а с ч ё ̀ т к а к о ́ г о|к о е н а с ч ё т к а к о ́ г о|Correctly predicted stress and disregarded secondary stress|
|п о д в а д ц а т и ̀ ч е т ы р ё х л е ́ т н е й|п о д в а д ц а т и ч е т ы р ё х л е ́ т н е й|Correctly predicted stress because it located after the [ё]; the derived stresscode would be different if the [ё] was after the stressed vowel|
|ё ̀ ж - р ы ́ б у|ё ж - р ы ́ б у|The stresscode system is unaware of placing secondary stress properly|
|к о ̀ е п у т ё ̀ м ч е г о ́|к о е п у т ё м ч е г о ́|The predictions are correct for the primary stress but not for any secondary stress (in this case two characters|
|о ̀ л и г о м е ́ р н о м у|о л и г о м е ́ р н о м у|Another example of this, indicating why there's a word error rate of 11.61|
<br>  
<br>
  
## Experiment 04 - Stress Prediction with lemma feature α- Given the word's lemma, predict the stress placement<br>
  
Source: а х н у в ш и м ахнуть<br>
Target: а́ х н у в ш и м<br>
<br>
  
`WER on dev = 15.89`<br>
`WER on test = 16.37`<br>
  
<br>
<br>

|Target |Hypothesis |Lemma | Notes |
|------|----------|----- |--------|
|ш и б а ́ т ь|ш и б а ́ т ь|шибать | The lemma is the same as the grapheme, and the stress was correctly predicted|
|о ́ р у щ е й|о р у ́ щ е й|орать | Incorrect|
|э о ́ л о в ы|э о ́ л о в ы|эолов | With roots and regular conjugation|
|ё ж и к о м|ё ж и к о м|ёжик | Correctly predicted|
|щ е н к а м|щ е н к а ́ м|щенок | .35% of the data has no stress indicated; the predicted stress on [а] is incorrect|
|ш е ́ п ч у щ е ю|ш е ́ п ч у щ е ю | шептать||
|е ́ щ у щ е г о|е ́ щ у щ е г о|блистать | The root with the gloss _to shine_ is seemingly unrelated; the result is correct|
|ш о ́ р т и к а х|ш о ́ р т и к а х|шортики | Correctly predicted given the lemma|
<br>
  
## Experiment 05 - Stress Prediction with lemma feature β- Given the word's lemma, predict the stresscode<br>
  
Source: а х н у в ш и м ахнуть<br>
Target: 2   <br>
<br>
  
`WER on dev = 6.5`<br>
`WER on test = 6.39`<br>
<br>

|Target |Hypothesis | Lemma | Notes |
|------|------------|--------|--------|
|д о ч е р и|0|дочь|Incorrectly predicted 0 instead of [о]|
|щ е р и в ш и е с я|4|щериться|Correctly predicted the [е] in [ери]|
|у р о б а к т е р и и|2|уробактерия|Correctly predicted the [е] in [ери]|
|щ е р и в ш е г о с я|4|щериться|Correctly predicted the [е] in [ери]|
|ш л а к о в щ и к о м|2|шлаковщик|Correctly predicted the [а] present in the lemma|
|щ ё л к а ю щ и е с я|5|щёлкать|Correctly predicted the [ё] present in the lemma|
|и г р ы в а ю щ у ю с я|5|игрывать|Incorrectly predicted stress on [ы] instead of the initial [и]|
<br>
<br>
  
## Experiment 06 - Stress Prediction with morphological feature  α - Given all of the word's morphological features, predict the stress placement<br>
  
Source: а х н у в ш и м V Perf PstAct Neu AnIn Sg Ins<br>
Target: а́ х н у в ш и м<br>
<br>
  
`WER on dev = 24.03`<br>
`WER on test = 24.06`<br>
  
<br>
  
|Target |Hypothesis |Morph | Notes |
|------|------------|-------|------|
|о ́ б щ е й|о ́ б щ е й|A Fem AnIn Sg Loc|Correctly predicted stress on initial [о]|
|у ш н о ́ й|у ́ ш н о й|A Fem AnIn Sg Ins|Incorrectly predicted stress on initial [у]|
|щ и т о в о ́ й|щ и т о в о ́ й|A Msc Inan Sg Acc|Correctly predicted oxytone stress||
|и з б о ́ й|и ́ з б о й|N Fem Inan Sg Ins|Incorrectly predicted stress on initial [и]|
|ш н у р е ́|ш н у ́ р е|N Msc Inan Sg Loc|Incorrectly predicted the [у]|
|о ́ б у х у|о ́ б у х у|N Msc Inan Sg Dat|Correctly predicted this noun's stress|
|ш п и к а ́|ш п и ́ к а|N Msc Anim Sg Acc|Incorrectly predicted this noun|
|ш к о д л и ́ в е е|ш к о д л и ́ в е е|A Cmpar Pred|Correctly predicted this adjective|
|щ е к о ́ в е е|щ е к о ́ в е е|A Cmpar Pred|Correctly predicted the proparoxytone stress|
|п о п л ё в е е|п о п л ё в е е|A Cmpar Pred Att|Correctly predicted stress on the [ё]|
|п о и ́ в о в е е|п о и в о ́ в е е|A Cmpar Pred Att|Incorrecly predicted the proparoxytone stress|

<br>
<br>
  
## Experiment 07 - Stress Prediction with morphological feature β - Given only word's part of speech feature, predict the stress placement<br>
  
Source: а х н у в ш и м V<br>
Target: а́ х н у в ш и м<br>
<br>
  
`WER on dev = 22.89`<br>
`WER on test = 22.53`<br>
<br>
  
|Target |Hypothesis | Part of Speech | Notes |
|------|------------|---------|--------|
|у н я ́ т ы м|у н я ́ т ы м|Verb|Correctly predicted the paroxytone stress| 
|щ у н я ́ н ы|щ у н я ́ н ы|Verb|Correctly predicted the paroxytone stress|
|ш а ́ с т а л|ш а ́ с т а л|Verb|Correctly predicted the paroxytone stress|
|ш к о ́ л к а|ш к о ́ л к а|Noun|Correctly predicted the paroxytone stress|
|О ́ р к а м и|О ́ р к а м и|Noun|Correctly predicted the proparoxytone stress|
|э х о - к а ́ м е р ы|э х о - к а м е ́ р ы|Noun|Incorrectly predicted paroxytone stress|
|ш а ́ ш к а м и|ш а ́ ш к а м и|Noun|Correctly predicted stress before [ками]|
|ю ́ ш к а м и|ю ́ ш к а м и|Noun|Correctly predicted stress before [ками]|
|у ́ с ь к а е м о г о|у ́ с ь к а е м о г о|Verb|Correcly predicted stress on initial [у]|
|э г о и с т и ́ ч е н|э г о и с т и ́ ч е н|Adjective|Correctly predicted the paroxytone stress|
|п о р а з л и т е ́ й|п о р а з л и ́ т е й|Adjective|Incorrectly predicted a paroxytone stress, stress is on oxytone|
|п о с у ́ м о ч н е й|п о с у м о ́ ч н е й|Adjective|Incorrectly predicted a paroxytone stress, stress is on the proparoxytone|
|ш к в о р н е в о ́ й|ш к в о ́ р н е в о й|Adjective|Incorrectly predicted a proparoxytone stress, stress is on the oxytone|
<br>  
<br>
  
## Experiment 08 - Stresscode Prediction with morphological feature - Given the word's part of speech feature, predict the stresscode<br>
  
Source: а х н у в ш и м V<br>
Target: 2<br>
<br>
  
`WER on dev = 15.84`<br>
`WER on test = 16.14`<br>
<br>
  
|Target (unstressed) |Hypothesis | Part of Speech | Notes |
|------|------------|-------|--------|
|я щ и к о в|2|Noun|Correctly predicted a proparoxytone stress on initial [я]|
|ю м о р а х|2|Noun|Correctly predicted a proparoxytone stress on initial [ю]|
|а п е к с а х|2|Noun|Correctly predicted a proparoxytone stress on initial [а]|
|ш п а н д ы р е|2|Noun|Correctly predicted a proparoxytone stress on the first syllable|
|э г о и с т к и|1|Noun|Correctly predicted a paroxytone stress on the middle [и]|
|а в и а с т р о е н и я|2|Noun|Correctly predicted a proparoxytone stress the [e] near the end of the word|
|ш и ф о н ь е р к е|2|Noun|Incorrectly predicted a proparoxytone stress; stress is on the middle [е]|
|ю т и в ш и х с я|2|Verb|Correctly predicted a paroxytone stress on the middle [и]||
|ш м я к а ю щ е м у|4|Verb|Correctly predicted stress on the middle [а]|  
|ш н у р у ю щ е м у с я|4|Verb|Correctly predicted stress on the middle [у]|
|о с к с к и м|1|Adjective|Correctly predicted a paroxytone stress on the initial [о]|
|щ и п а н ы е|3|Adjective|Correctly predicted stress on the initial syllable on [и]|
|я с н е й ш и|1|Adjective|Correctly predicted a paroxytone stress on the middle [е]|
|я г у а р о в о е|3|Adjective|Correctly predicted stress on the middle [а]|
|и с к р и с т у ю|3|Adjective|Incorrectly predicted the initial [и]; stress is on middle [и]|
|п о г р о б о в е й|1|Adjective|Incorrectly predicted a paroxytone stress; stress is on oxytone [е]|
<br>

## Conclusions and Discussion
TO DO: Look at adjectives -- is it true that they are REGULARLY stressed on the first syllable? (Jouravlev and Lupker) No, I don't think so.<br>
TO DO: Look at other POS
TO DO: Look more into consistency, lexical stress.<br>
TO DO: Hall & Sproat's contribution<br>
TO DO: lexicon of stress homographs, a quantitative write-up and appendix<br>
  

  
