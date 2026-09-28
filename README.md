# Lexical & Semantic Similarity — NLP

Projecte desenvolupat per a l'assignatura **Processament del Llenguatge Humà (PLH)** del Grau en Intel·ligència Artificial de la UPC.

L'objectiu del projecte és estudiar diferents tècniques de **representació semàntica del llenguatge** i avaluar fins a quin punt permeten capturar la similitud entre paraules i entre frases.

El projecte combina models d'**embeddings estàtics**, arquitectures neuronals seqüencials i models contextuals basats en Transformers.

## Objectius

El treball es divideix en dues tasques principals:

### 1. Similitud lèxica

Anàlisi de fins a quin punt diferents embeddings capturen la similitud semàntica entre paraules.

Es van entrenar i comparar:

- **Word2Vec**
- **fastText**
- fastText preentrenat com a model de referència

Els models propis es van entrenar sobre el **Wikicorpus en espanyol**, explorant l'efecte de:

- dimensionalitat dels embeddings,
- mida del corpus d'entrenament,
- cobertura del vocabulari,
- paraules Out-Of-Vocabulary (OOV),
- i representació mitjançant n-grames.

L'avaluació es va realitzar sobre **Multi-SimLex Spanish**, utilitzant la correlació de **Spearman** entre la similitud cosinus dels embeddings i les puntuacions humanes.

### 2. Similitud semàntica entre frases

La segona part del projecte aborda la tasca de **Semantic Textual Similarity (STS)**: predir fins a quin punt dues frases tenen el mateix significat.

Es van comparar diferents nivells de complexitat:

- Mitjana d'embeddings
- Mitjana ponderada amb **TF-IDF**
- **BiLSTM siamès amb mecanisme d'atenció**
- **BETO siamès**, basat en BERT per a espanyol

L'avaluació es va realitzar sobre el dataset **Spanish STS**, utilitzant la correlació de **Pearson** entre les prediccions del model i les puntuacions humanes.

---

## Metodologia

### Preprocessament

El Wikicorpus es va preprocessar abans de l'entrenament dels embeddings:

- conversió a minúscules,
- tokenització,
- eliminació de puntuació,
- filtratge de frases molt curtes,
- i eliminació de paraules amb freqüència molt baixa.

El corpus processat conté aproximadament **101 milions de tokens**.

### Word2Vec i fastText

Es van entrenar múltiples configuracions per estudiar l'impacte de la dimensionalitat i la quantitat de dades.

Es van explorar embeddings de diferents dimensions i subconjunts progressius del corpus, permetent analitzar experimentalment quins factors tenen més impacte sobre la qualitat de les representacions.

També es va estudiar una diferència important entre les dues arquitectures:

- **Word2Vec** només pot representar paraules presents al vocabulari.
- **fastText** pot construir representacions per paraules desconegudes mitjançant n-grames de caràcters.

---

## Model Siamès BiLSTM

Per passar de la similitud entre paraules a la similitud entre frases, es va implementar una arquitectura **siamesa**.

Les dues frases són processades per la mateixa xarxa:

```text
Sentence A ──► Embeddings ──► BiLSTM ──► Attention ──► Vector A
                                                        │
                                                        ├──► Similarity prediction
                                                        │
Sentence B ──► Embeddings ──► BiLSTM ──► Attention ──► Vector B
```

La **BiLSTM** processa cada frase en ambdues direccions, mentre que el mecanisme d'**atenció** permet donar més pes als tokens més rellevants.

Les representacions finals de les dues frases es combinen utilitzant:

- concatenació,
- diferència absoluta,
- producte element a element.

Aquesta representació conjunta s'introdueix en una xarxa neuronal que prediu el nivell de similitud.

---

## BETO Siamès

Finalment, es va implementar un model siamès basat en **BETO**, una versió de BERT preentrenada específicament per a castellà.

A diferència dels embeddings estàtics, BETO genera representacions **contextuals**, de manera que el vector associat a una paraula depèn de la frase en què apareix.

L'arquitectura utilitza el mateix encoder BETO per processar les dues frases:

```text
Sentence A ──► BETO ──► Mean Pooling ──► Vector A
                                             │
                                             ├──► MLP ──► Similarity score
                                             │
Sentence B ──► BETO ──► Mean Pooling ──► Vector B
```

Aquest enfocament permet capturar informació semàntica i contextual més rica que les representacions estàtiques.

---

## Resultats

Els experiments mostren una millora progressiva a mesura que s'utilitzen representacions més contextuals.

### Similitud lèxica

Entre els models entrenats durant la pràctica, **Word2Vec va obtenir millors resultats que fastText propi** en similitud semàntica pura.

Els experiments també van mostrar que:

- augmentar la dimensionalitat millora el rendiment fins a cert punt,
- augmentar la mida del corpus té un impacte important sobre la qualitat dels embeddings,
- la capacitat de fastText de generar vectors per paraules OOV no garanteix necessàriament una millor representació semàntica.

### Semantic Textual Similarity

Resultats principals sobre el conjunt de test de Spanish STS:

| Model | Pearson test |
|---|---:|
| BETO siamès | **0.7315** |
| fastText oficial + TF-IDF | 0.6523 |
| BiLSTM siamès + fastText propi | 0.5843 |
| Word2Vec + mean pooling | 0.5786 |
| Word2Vec + TF-IDF | 0.5770 |

El **BETO siamès** va obtenir el millor rendiment, mostrant l'avantatge de les representacions contextuals per capturar el significat global de les frases.

---

## Anàlisi experimental

A més de comparar arquitectures, el projecte inclou diferents experiments d'ablació i visualització de resultats:

- efecte de la dimensionalitat dels embeddings,
- efecte de la mida del corpus,
- cobertura lèxica i OOV,
- comparació Word2Vec vs. fastText,
- anàlisi per categories gramaticals,
- corbes d'entrenament,
- scatter plots de prediccions,
- i anàlisi qualitativa dels errors.

Algunes de les visualitzacions generades durant els experiments es troben al repositori.

---

## Tecnologies

- Python
- PyTorch
- Transformers
- scikit-learn
- NumPy
- pandas
- NLTK
- Gensim
- Word2Vec
- fastText
- BETO / BERT

---

## Principals aprenentatges

Aquest projecte va permetre treballar de manera pràctica conceptes fonamentals de Processament del Llenguatge Natural:

- representacions vectorials de paraules,
- embeddings estàtics i contextuals,
- Word2Vec i fastText,
- tractament de paraules OOV,
- similitud cosinus,
- Semantic Textual Similarity,
- arquitectures siameses,
- BiLSTM i mecanismes d'atenció,
- Transformers i BERT,
- fine-tuning de models preentrenats,
- experimentació i estudis d'ablació,
- i avaluació mitjançant correlacions de Spearman i Pearson.
