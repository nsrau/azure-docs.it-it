---
title: Associazione di domande e risposte con Azure Machine Learning Workbench | Microsoft Docs
description: Come usare diversi metodi di apprendimento automatico efficaci per associare query aperte a coppie di domande frequenti e risposte preesistenti.
services: machine-learning
documentationcenter: 
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
manager: tihazen
ms.openlocfilehash: 33f807a4a0bbc4afd1f2fbe017f8913eccacc34b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Associazione di domande e risposte con Azure Machine Learning Workbench
È difficile rispondere a domande aperte e spesso a questo scopo è necessario l'intervento di esperti in materia. Per ridurre l'esigenza di ricorrere a esperti in materia interni, le società spesso creano elenchi di domande frequenti come strumento per offrire assistenza agli utenti. Questo esempio mostra diversi metodi di apprendimento automatico efficaci per associare query aperte a coppie di domande frequenti e risposte preesistenti. Questo esempio mostra un semplice processo di sviluppo per creare la soluzione con Azure Machine Learning Workbench. 

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Panoramica

Questo esempio permette di risolvere il problema di associare domande degli utenti a coppie di domande e risposte preesistenti, come quelle normalmente fornite in un elenco di domande frequenti o nelle coppie di domande e risposte presenti in siti Web come [Stack Overflow](https://stackoverflow.com/). Esistono molti approcci per associare una domanda alla sua risposta corretta, ad esempio individuando la risposta più simile alla domanda. Tuttavia, in questo esempio vengono associate domande aperte a domande poste in precedenza, presupponendo che ogni risposta alla domanda frequente può essere usata per più domande semanticamente equivalenti.

I passaggi principali necessari per creare questa soluzione sono i seguenti:

1. Pulire ed elaborare i dati di testo.
2. Acquisire frasi informative, ovvero sequenze di più parole che forniscono più informazioni se visualizzate in sequenza rispetto a quando vengono considerate singolarmente.
3. Estrarre le caratteristiche dai dati di testo.
4. Eseguire il training dei modelli di classificazione del testo e valutare le prestazioni dei modelli.


## <a name="prerequisites"></a>prerequisiti

I prerequisiti per eseguire questo esempio sono i seguenti:

1. Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).
2. Una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installata seguendo la [guida introduttiva all'installazione](./quickstart-installation.md) per installare il programma e creare un'area di lavoro.
3. Questo esempio può essere eseguito in qualsiasi contesto di calcolo. Tuttavia, è consigliabile eseguirlo in un computer multicore con almeno 16 GB di memoria e 5 GB di spazio su disco.

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto)
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) digitare "Q & A Matching" (Associazione di domande e risposte) e selezionare il modello
5.  Fare clic su **Crea**

## <a name="data-description"></a>Descrizione dei dati

Il set di dati usato in questo esempio è un dump di dati di Stack Exchange archiviato in [archive.org](https://archive.org/details/stackexchange). Questi dati sono un dump reso anonimo di tutto il contenuto fornito dagli utenti nella [rete di Stack Exchange](https://stackexchange.com/). Ogni sito nella rete è formattato come archivio separato costituito da file XML compressi tramite 7-zip con compressione bzip2. Ogni archivio del sito include post, utenti, voti, commenti, cronologia dei post e collegamenti dei post. 

### <a name="data-source"></a>Origine dati

Questo esempio usa i [dati dei post (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) e i [dati dei collegamenti dei post (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) dal sito Stack Overflow. Per informazioni complete sullo schema, vedere il file [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

Il campo `PostTypeId` nei dati dei post indica se un post è una domanda (`Question`) o una risposta (`Answer`). Il campo `PostLinkTypeId` nei dati dei collegamenti dei post indica se due post sono collegati o duplicati. I post di domanda includono in genere alcuni tag, che sono parole chiave che permettono di classificare la domanda in base a una categoria di domande simili/duplicate. Alcuni tag hanno una frequenza elevata, ad esempio `javascript`, `java`, `c#`, `php` e così via, e sono costituiti da un numero maggiore di post di domanda. In questo esempio viene estratto un subset di coppie di domande e risposte con il tag `javascript`.

Inoltre, un post di domanda può essere correlato a più post di risposta o post di domanda duplicati. Per creare un elenco di domande frequenti da questi due set di dati, vengono presi in considerazione alcuni criteri di raccolta dati. I tre set di dati compilati vengono selezionati tramite uno script SQL, che non è incluso in questo esempio. La descrizione dei dati risultante è la seguente:

- `Original Questions (Q)`: i post di domanda vengono immessi e ricevono risposta nel sito Stack Overflow.
- `Duplications (D)`: i post di domanda duplicano altre domande preesistenti (`PostLinkTypeId = 3`), ovvero le domande originali. I duplicati sono considerati semanticamente equivalenti alle domande originali, nel senso che la risposta fornita alla domanda originale risponde anche alla nuova domanda duplicata.
- `Answers (A)`: ogni domanda originale e il suo duplicato possono essere collegati a più di un post di risposta. Questo esempio seleziona solo il post di risposta accettato dall'autore originale (filtrato in base a `AcceptedAnswerId`) o associato al punteggio più alto (classificato in base a `Score`) nelle domande originali. 

La combinazione di questi tre set di dati crea coppie di domande e risposte in cui una risposta (A) viene mappata alla domanda originale (Q) e a più domande duplicate (D), come mostrato nel diagramma di dati seguente:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Struttura dei dati

Lo schema dei dati e i collegamenti per il download diretto dei tre set di dati sono indicati nella tabella seguente:

| Set di dati | Campo | type | DESCRIZIONE
| ----------|------------|------------|--------
| [questions](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | ID | string | ID domanda univoco (chiave primaria)
|  | AnswerId | string | ID risposta univoco per ogni domanda
|  | Text0 | string | Dati di testo non elaborati che includono il titolo e il corpo della domanda
|  | CreationDate | Timestamp | Timestamp del momento in cui è stata posta la domanda
| [dupes](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | ID | string | ID duplicazione univoco (chiave primaria)
|  | AnswerId | string | ID risposta associato alla duplicazione
|  | Text0 | string | Dati di testo non elaborati che includono il titolo e il corpo del duplicato
|  | CreationDate | Timestamp | Timestamp del momento in cui è stata posta la domanda duplicata
| [answers](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | ID | string | ID risposta univoco (chiave primaria)
|  | text0 | string | Dati di testo non elaborati della risposta


## <a name="scenario-structure"></a>Struttura dello scenario

L'esempio di associazione di domande e risposte viene presentato tramite tre tipi di file. Il primo tipo è una serie di notebook di Jupyter che mostra le descrizioni dettagliate dell'intero flusso di lavoro. Il secondo tipo è un set di file Python che contiene moduli Python personalizzati per l'apprendimento di frasi e l'estrazione di caratteristiche. Questi moduli Python sono abbastanza generici per gestire non solo questo esempio, ma anche altri casi d'uso. Il terzo tipo è un set di file Python per ottimizzare gli iperparametri e tenere traccia delle prestazioni dei modelli con Azure Machine Learning Workbench.

I file in questo esempio sono organizzati come indicato di seguito.

| File Name | type | DESCRIZIONE
| ----------|------------|--------
| `Image` | Cartella | Cartella usata per salvare le immagini per il file README
| `notebooks` | Cartella | Cartella dei notebook di Jupyter
| `modules` | Cartella | Cartella dei moduli Python
| `scripts` | Cartella | Cartella dei file Python
| `notebooks/Part_1_Data_Preparation.ipynb` | Notebook Jupyter | Accesso ai dati di esempio, analisi preliminare del testo e preparazione dei set di dati di training e test
| `notebooks/Part_2_Phrase_Learning.ipynb` | Notebook Jupyter | Apprendimento di frasi informative e tokenizzazione del testo in rappresentazioni bag-of-words (BOW)
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Notebook Jupyter | Estrazione delle caratteristiche, training dei modelli di classificazione del testo e valutazione delle prestazioni dei modelli
| `modules/__init__.py` | File Python | File di inizializzazione del pacchetto Python
| `modules/phrase_learning.py` | File Python | Moduli Python usati per trasformare i dati non elaborati e acquisire frasi informative
| `modules/feature_extractor.py` | File Python | I moduli Python estraggono le caratteristiche per il training del modello
| `scripts/naive_bayes.py` | File Python | Script Python per ottimizzare gli iperparametri nel modello Naive Bayes
| `scripts/random_forest.py` | File Python | Script Python per ottimizzare gli iperparametri nel modello Random Forest
| `README.md` | File markdown | File markdown README

> [!NOTE]
> La serie di notebook è compilata in Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Inserimento e trasformazione dei dati

I tre set di dati compilati vengono archiviati nel servizio Archiviazione Blob e vengono recuperati nel notebook `Part_1_Data_Preparation.ipynb`.  

Prima del training dei modelli di classificazione del testo, il testo nelle domande viene pulito e analizzato in modo preliminare per escludere i frammenti di codice. Al materiale di training viene applicato l'apprendimento di frasi senza supervisione per acquisire sequenze informative di più parole. Le frasi vengono rappresentate come singole unità di parole composte nella rappresentazione bag-of-words (BOW) a valle usata dai modelli di classificazione del testo.

Le descrizioni dettagliate dell'analisi preliminare del testo e dell'apprendimento delle frasi sono disponibili rispettivamente nei notebook `Part_1_Data_Preparation.ipynb` e `Part_2_Phrase_Learning.ipynb`.

### <a name="modeling"></a>Modellazione

Questo esempio è progettato per applicare un punteggio alle domande rispetto alle coppie di domande e risposte preesistenti tramite il training dei modelli di classificazione del testo in cui ogni coppia di domanda e risposta preesistente è una classe univoca ed è disponibile un subset delle domande duplicate per ogni coppia di domanda e risposta come materiale di training. Nell'esempio le domande originali e il 75% delle domande duplicate vengono conservati per il training, mentre il 25% delle domande duplicate pubblicate più di recente viene reso disponibile come dati di valutazione.

Il modello di classificazione usa un metodo di insieme per aggregare tre classificatori di base, **Naive Bayes**, **Support Vector Machine** e **Random Forest**. In ogni classificazione di base viene usato `AnswerId` come etichetta di classe e le rappresentazioni BOW vengono usate come caratteristiche.

Il processo di training del modello viene descritto in `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Versione di valutazione

Per valutare le prestazioni, vengono usate due metriche di valutazione diverse. 
1. `Average Rank (AR)`: indica la posizione media in cui si trova la risposta corretta nell'elenco delle coppie di domande e risposte recuperate (rispetto al set completo delle 103 classi di risposta). 
2. `Top 3 Percentage`: indica la percentuale delle domande di test la cui risposta corretta può essere recuperata nelle prime tre scelte all'interno dell'elenco ordinato restituito. 

La valutazione viene mostrata in `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Conclusioni

Questo esempio mostra come usare tecniche di analisi del testo ben note, ad esempio l'apprendimento delle frasi e la classificazione del testo, per produrre un modello affidabile. L'esempio mostra anche come usare Azure Machine Learning Workbench per lo sviluppo di soluzioni interattive e il rilevamento delle prestazioni dei modelli. 

Ecco alcune delle principali caratteristiche di questo esempio:

- Il problema di associazione di domande e risposte può essere risolto efficacemente tramite modelli di apprendimento delle frasi e i classificazione del testo.
- Descrizione dello sviluppo di modelli interattivi con Azure Machine Learning Workbench e notebook di Jupyter.
- Azure Machine Learning Workbench gestisce la cronologia di esecuzione e i modelli acquisiti con la registrazione delle metriche di valutazione per scopi di confronto. Queste funzionalità permettono la rapida ottimizzazione degli iperparametri e aiutano a identificare i modelli dalle prestazioni migliori.


## <a name="references"></a>Riferimenti

Timothy J. Hazen, Fred Richardson, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Spoken Language Technology Workshop (SLT), 2012 IEEE. IEEE, 2012.

Timothy J. Hazen, [_MCE Training Techniques for Topic Identification of Spoken Audio Documents_](http://ieeexplore.ieee.org/abstract/document/5742980/) in IEEE Transactions on Audio, Speech, and Language Processing, vol. 19, n. 8, pagine 2451-2460, novembre 2011.
