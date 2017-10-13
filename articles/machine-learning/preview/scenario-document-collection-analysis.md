---
title: Analisi di raccolte di documenti - Azure | Microsoft Docs
description: Procedura per riepilogare e analizzare una vasta raccolta di documenti, incluse tecniche quali apprendimento della frase, modellazione dell'argomento e analisi del modello di argomento mediante Azure ML Workbench.
services: machine-learning
documentationcenter: 
author: kehuan
ms.author: kehuan
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 669fc7a9ec5dfb446ef2755919c498fe6f60c9df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="document-collection-analysis"></a>Analisi delle raccolta di documenti

Questo scenario dimostra la procedura per riepilogare e analizzare una vasta raccolta di documenti, incluse tecniche quali apprendimento della frase, modellazione dell'argomento e analisi del modello di argomento mediante Azure ML Workbench. Azure Machine Learning Workbench consente di affrontare facilmente vaste raccolte di documenti e fornisce meccanismi per addestrare e ottimizzare i modelli in un'ampia varietà di contesti di calcolo, che spaziano dal calcolo locale, alle macchine virtuali per data science, al cluster Spark. Lo sviluppo semplificato è fornito tramite notebook Jupyter all'interno di Azure Machine Learning Workbench.

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub della raccolta

Il repository GitHub pubblico per questo scenario reale contiene tutti i materiali, inclusi esempi di codice necessari per questo esempio:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Panoramica

Considerata la grande quantità di dati (in particolare dati di testo non strutturati) raccolti ogni giorno, organizzare, ricercare e comprendere elevate quantità di testi è una vera sfida. Questo scenario di analisi per una raccolta di documenti illustra un flusso di lavoro end-to-end efficiente e automatizzato per l'analisi di vaste raccolte di documenti e l'abilitazione di attività NLP a valle.

Gli elementi chiave offerti da questo scenario sono:

1. Apprendimento delle frasi di più parole salienti dei documenti.

1. Individuazione degli argomenti di base presentati nella raccolta di documenti.

1. Rappresentazione dei documenti mediante la distribuzione degli argomenti.

1. Presentazione di metodi per organizzare, ricercare e riepilogare i documenti in base agli argomenti trattati.

I metodi descritti in questo scenario potrebbero supportare un'ampia gamma carichi di lavoro critici di settore, ad esempio il rilevamento di anomalie nelle tendenze degli argomenti, il riepilogo della raccolta di documenti e la ricerca di documenti simili. Tali metodi sono applicabili a molti tipi diversi di analisi di documenti, ad esempio legislazione, notizie, recensioni di prodotti, commenti di clienti e articoli di ricerca scientifica.

Le tecniche/algoritmi di machine learning utilizzati in questo scenario includono:

1. Elaborazione e pulizia del testo

1. Apprendimento di frase

1. Modellazione di argomenti

1. Riepilogo della raccolta

1. Rilevamento delle tendenze e delle anomalie degli argomenti

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per eseguire questo esempio sono i seguenti:

* Assicurarsi che [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) sia stato installato correttamente seguendo la [Guida introduttiva all'installazione e alla creazione](quickstart-installation.md).

* Questo esempio può essere eseguito in qualsiasi contesto di calcolo, tuttavia è consigliabile eseguirlo in un computer multicore con almeno 16 GB di memoria e 5 GB di spazio su disco.

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto)
3.  Nel riquadro **Crea nuovo progetto** specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Cerca modelli di progetto** digitare "Analisi raccolta documenti" e selezionare il modello
5.  Fare clic su **Crea**

## <a name="data-description"></a>Descrizione dei dati

Il set di dati utilizzato in questo scenario contiene riepiloghi di testo e metadati associati per ogni azione legislativa del Congresso degli Stati Uniti. I dati vengono raccolti da [GovTrack.us](https://www.govtrack.us/) che tiene traccia delle attività del Congresso e aiuta gli americani a partecipare al loro processo legislativo nazionale. I dati in blocco possono essere scaricati tramite [questo collegamento](https://www.govtrack.us/data/congress/) usando uno script manuale, che non è incluso in questo scenario. È possibile trovare i dettagli su come scaricare i dati nella [documentazione dell'API GovTrack](https://www.govtrack.us/developers/api).

### <a name="data-source"></a>Origine dati

In questo scenario i dati non elaborati raccolti sono una serie di azioni legislative introdotte dal Congresso degli Stati Uniti (proposte di leggi e risoluzioni) dal 1973 al giugno 2017 (dal 93° al 115° Congresso). I dati raccolti sono nel formato JSON e contengono un set completo di informazioni sulle azioni legislative. Per una descrizione dettagliata dei campi dati, vedere [questo collegamento a GitHub](https://github.com/unitedstates/congress/wiki/bills). Per lo scopo dimostrativo di questo scenario, è stato estratto solo un sottoinsieme dei campi dai file JSON. In questo scenario è fornito il file TSV pre-compilato `CongressionalDataAll_Jun_2017.tsv` contenente i record di tali azioni legislative. Il file TSV può essere scaricato automaticamente o tramite i notebook `1_Preprocess_Text.ipynb` sotto la cartella dei notebook o `preprocessText.py` nel pacchetto di Python.

### <a name="data-structure"></a>Struttura dei dati

Nel file di dati sono presenti nove campi dati. I nomi e le descrizioni dei campi dati sono elencati come indicato di seguito.

| Nome campo | Tipo | Descrizione | Contiene un valore mancante |
|------------|------|-------------|---------------|
| `ID` | String | L'ID della legge/risoluzione. Il formato di questo campo è [tipo_legge][numero]-[congresso]. Ad esempio "hconres1-93" significa che il tipo di legge è "hconres" (sta per House Concurrent Resolution, vedere [questo documento](https://github.com/unitedstates/congress/wiki/bills#basic-information)), il numero della legge è "1" e il numero del Congresso è "93". | No |
| `Text` | String | Il contenuto della legge/risoluzione. | No |
| `Date` | String | La data in cui la legge/risoluzione è stata proposta inizialmente. Nel formato "aaaa-mm-gg". | No |
| `SponsorName` | String | Il nome del promotore primario che ha proposto la legge/risoluzione. | Sì |
| `Type` | String | Il tipo di titolo dello promotore primario, che può essere "rep" (rappresentante) o "sen" (senatore). | Sì |
| `State` | String | Lo stato del promotore primario. | Sì |
| `District` | Integer | Il numero di zona del promotore primario se il titolo del promotore è "rappresentante". | Sì |
| `Party` | String | Il partito del promotore primario. | Sì |
| `Subjects` | String | I termini della materia aggiunti in modo cumulativo dalla Libreria del Congresso alla legge. I termini sono concatenati con virgole. Questi termini vengono scritti da una persona nella Libreria del Congresso e non sono in genere presenti nella prima pubblicazione delle informazioni sulla legge. Possono essere aggiunti in qualsiasi momento. In questo modo, alla fine del ciclo di vita di una legge, una materia potrebbe non essere più rilevante. | Sì |

## <a name="scenario-structure"></a>Struttura dello scenario

L'esempio di analisi della raccolta di documenti è organizzato in due tipi di risultati finali. Il primo tipo è una serie di notebook iPython che mostrano le descrizioni dettagliate dell'intero flusso di lavoro. Il secondo tipo è un pacchetto Python con esempi di codice relativi all'uso di tale pacchetto. Il pacchetto Python è sufficientemente generico da poter gestire molti casi d'uso.

I file in questo esempio sono organizzati come indicato di seguito.

| File Name | Tipo | Descrizione |
|-----------|------|-------------|
| `aml_config` | Cartella | Cartella di configurazione di Azure Machine Learning Workbench: per la configurazione di esecuzione dettagliata per l'esperimento, vedere [questa documentazione](./experiment-execution-configuration-reference.md) |
| `Code` | Cartella | La cartella del codice utilizzata per salvare gli script Python e il pacchetto Python |
| `Data` | Cartella | La cartella di dati utilizzata per salvare i file intermedi |
| `notebooks` | Cartella | La cartella dei notebook Jupyter |
| `Code/documentAnalysis/__init__.py` | File Python | File di inizializzazione del pacchetto Python |
| `Code/documentAnalysis/configs.py` | File Python | Il file di configurazione utilizzato dal pacchetto Python di analisi dei documenti, incluse le costanti predefinite |
| `Code/documentAnalysis/preprocessText.py` | File Python | Il file Python utilizzato per pre-elaborare i dati per le attività a valle |
| `Code/documentAnalysis/phraseLearning.py` | File Python | Il file Python utilizzato per apprendere frasi dai dati e trasformare i dati non elaborati |
| `Code/documentAnalysis/topicModeling.py` | File Python | Il file Python utilizzato per addestrare un modello Latent Dirichlet Allocation (LDA) |
| `Code/step1.py` | File Python | Passaggio 1 dell'analisi di una raccolta di documenti: pre-elaborazione del testo |
| `Code/step2.py` | File Python | Passaggio 2 dell'analisi di una raccolta di documenti: apprendimento delle frasi |
| `Code/step3.py` | File Python | Passaggio 3 dell'analisi di una raccolta di documenti: addestramento e validazione del modello di argomento LDA |
| `Code/runme.py` | File Python | Esempio di esecuzione di tutti i passaggi in un solo file |
| `notebooks/1_Preprocess_Text.ipynb` | Notebook iPython | Pre-elaborare il testo e trasformare i dati non elaborati |
| `notebooks/2_Phrase_Learning.ipynb` | Notebook iPython | Apprendere le frasi dai dati di testo (dopo la trasformazione dei dati) |
| `notebooks/3_Topic_Model_Training.ipynb` | Notebook iPython | Addestrare il modello di argomento LDA |
| `notebooks/4_Topic_Model_Summarization.ipynb` | Notebook iPython | Riepilogare il contenuto della raccolta di documenti in base a un modello di argomento LDA addestrato |
| `notebooks/5_Topic_Model_Analysis.ipynb` | Notebook iPython | Analizzare gli argomenti del contenuto di una raccolta di documenti di testo e correlare le informazioni sugli argomenti con altri metadati associati alla raccolta di documenti |
| `notebooks/winprocess.py` | File Python | Lo script Python per la multielaborazione utilizzato dai notebook |
| `README.md` | File markdown | Il file markdown LEGGIMI |

### <a name="data-ingestion-and-transformation"></a>Inserimento e trasformazione dei dati

Il set di dati compilato `CongressionalDataAll_Jun_2017.tsv` viene salvato nell'archiviazione BLOB ed è accessibile sia dall'interno dei notebook sia dagli script Python. L'inserimento e la trasformazione dei dati prevedono due passaggi: pre-elaborazione dei dati di testo e apprendimento di frasi.

#### <a name="preprocess-text-data"></a>Pre-elaborare i dati di testo

Il passaggio di pre-elaborazione applica tecniche di elaborazione del linguaggio naturale per pulire e preparare i dati di testo non elaborato. Serve come precursore per l'apprendimento di frasi non supervisionato e la modellazione di argomento latente. La descrizione dettagliata è reperibile nel notebook `1_Preprocess_Text.ipynb`. È disponibile anche uno script Python `step1.py` che corrisponde a questo notebook.

In questo passaggio il file di dati TSV viene scaricato dall'archiviazione BLOB di Azure e importato come dataframe Pandas. Ogni elemento riga del dataframe è una singola stringa di testo lunga e coesiva o "documento". Ogni documento viene quindi suddiviso in blocchi di testo che potrebbero essere periodi, frasi o sottofrasi. La divisione è progettata in modo da impedire che il processo di apprendimento delle frasi utilizzi stringhe di parole che includono più periodi o frasi in fase di apprendimento delle frasi.

Per il passaggio di pre-elaborazione sono disponibili più funzioni definite sia nel notebook che nel pacchetto Python. La maggior parte del lavoro si può completare chiamando queste due righe di codice.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Apprendimento di frase

Il passaggio di apprendimento di frase implementa un framework di base per apprendere le frasi in un'ampia raccolta di documenti. È descritto nell'articolo intitolato "[Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)" (Modellazione di frasi di più parole con una struttura di frase vincolata per una migliorata modellazione degli argomenti delle conversazioni), che è stato presentato in origine nel workshop IEEE 2012 sulla tecnologia della lingua parlata. L'implementazione dettagliata del passaggio dell'apprendimento di frase è illustrata nel Notebook iPython `2_Phrase_Learning.ipynb` e nello script Python `step2.py`.

Questo passaggio prende il testo pulito come input e apprende le frasi più salienti presenti in un'ampia raccolta di documenti. L'apprendimento di frase è un processo iterativo che può essere suddiviso in tre attività: conteggio degli n-grammi, valutazione delle frasi potenziali in base alle informazioni reciproche punto per punto ponderate delle parole che le costituiscono e riscrittuta della frase in testo. Queste tre attività vengono eseguite in sequenza in più iterazioni fino a quando le frasi specificate sono state apprese.

Nel pacchetto Python di analisi dei documenti è definita la classe Python `PhraseLearner` nel file `phraseLearning.py`. Di seguito è riportato il frammento di codice utilizzato per apprendere le frasi.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> Il passaggio di apprendimento di frase viene implementato con la multielaborazione. Tuttavia, la presenza di più core CPU **NON** implica tempi di esecuzione inferiori. Durante i test le prestazioni non si sono dimostrate migliori con più di otto core a causa del sovraccarico della multielaborazione. Sono state necessarie circa due ore e mezza per apprendere 25.000 frasi in un computer con otto core (3,6 GHz).
>

### <a name="topic-modeling"></a>Modellazione di argomenti

Apprendere un modello di argomento latente LDA è il terzo passaggio di questo scenario. Il pacchetto Python [gensim](https://radimrehurek.com/gensim/) è obbligatorio in questo passaggio per apprendere un [modello di argomento LDA](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Il notebook corrispondente per questo passaggio è `3_Topic_Model_Training.ipynb`. Per informazioni su come usare il pacchetto di analisi dei documenti è possibile vedere anche `step3.py`.

L'attività principale in questo passaggio è imparare a usare un modello di argomento LDA e ottimizzare i parametri hyper. Quando si addestra un modello LDA è necessario ottimizzare più parametri, ma il parametro più importante è il numero di argomenti. Un numero troppo ridotto di argomenti potrebbe non fornire informazioni utili sulla raccolta di documenti, mentre la scelta di un numero troppo elevato comporterà l'eccessiva "clusterizzazione" della raccolta in molti piccoli argomenti altamente simili. Lo scopo di questo scenario è illustrare come ottimizzare il numero di argomenti. Azure Machine Learning Workbench offre la libertà di eseguire esperimenti con una configurazione di parametri diversa in contesti di elaborazione differenti.

Nel pacchetto Python per l'analisi dei documenti sono state definite alcune funzioni per aiutare gli utenti a individuare il numero migliore di argomenti. Il primo approccio consiste nella valutazione della coerenza del modello di argomento. Sono supportate quattro metriche di valutazione: `u_mass`, `c_v`, `c_uci` e `c_npmi`. I dettagli di queste quattro metriche sono descritti in [questo documento](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Il secondo approccio consiste nella valutazione della perplessità di una raccolta esclusa.

Per la valutazione di perplessità si ritiene che una curva a forma di "U" individui il miglior numero di argomenti. E la posizione del gomito rappresenta la scelta migliore. È consigliabile eseguire la valutazione più volte con diversi seed casuali e ottenere la media. La valutazione della coerenza è prevista come una forma a "n", ovvero la coerenza aumenta con l'aumentare del numero di argomenti e poi diminuisce. Un tracciato di esempio della perplessità e coerenza `c_v` è riportato di seguito.

![Perplessità](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![Coerenza c_v](./media/scenario-document-collection-analysis/c_v_Coherence.png)

In questo scenario la perplessità aumenta in modo significativo dopo 200 argomenti, mentre il valore della coerenza diminuisce significativamente dopo 200 argomenti. In base a tali grafi e al desiderio di argomenti più generali rispetto agli argomenti eccessivamente clusterizzati, optare per 200 argomenti dovrebbe essere una scelta ottimale.

È possibile addestrare un modello di argomento LDA in una sola esecuzione di un esperimento o addestrare e valutare più modelli LDA con diverse configurazioni per il numero di argomenti in una singola esecuzione di un esperimento. Si consiglia di compiere più esecuzioni di una sola configurazione e quindi ottenere le valutazioni di coerenza e/o perplessità media. I dettagli su come usare il pacchetto di analisi dei documenti sono reperibili nel file `step3.py`. Un frammento di codice di esempio è il seguente.

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> Il tempo di esecuzione per l'addestramento di un modello di argomento LDA dipende da numerosi fattori, ad esempio le dimensioni della raccolta, la configurazione dei parametri hyper e il numero di core del computer. Utilizzando più core CPU, l'addestramento del modello è più veloce. Tuttavia, con lo stesso parametro hyper che configura più core, il numero di aggiornamenti durante l'addestramento è minore. Si consigliano **almeno 100 aggiornamenti per addestrare un modello LDA convergente**. La relazione tra il numero di aggiornamenti e i parametri hyper è trattata in [questo post](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) e in [quest'altro post](http://miningthedetails.com/blog/python/lda/GensimLDA/). Nei test eseguiti sono state necessarie circa 3 ore per addestrare un modello LDA con 200 argomenti usando la configurazione di `workers=15`, `passes=10`, `chunksize=1000` in un computer con 16 core (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Riepilogo e analisi degli argomenti

Il riepilogo e l'analisi degli argomenti consistono di due notebook, mentre non sono presenti funzioni corrispondenti nel pacchetto di analisi dei documenti.

In `4_Topic_Model_Summarization.ipynb` mostra come riepilogare il contenuto del documento in base a un modello di argomento LDA addestrato. Il riepilogo viene applicato a un modello di argomento LDA appreso al passaggio 3. Mostra come misurare l'importanza o la qualità di un argomento usando l'argomento per documentare la misura di purezza. Questa misura di purezza presuppone che gli argomenti latenti che dominano i documenti in cui compaiono siano semanticamente più importanti degli argomenti latenti che sono distribuiti in modo debole tra molti documenti. Questo concetto è stato introdotto nel documento "[Latent Topic Modeling for Audio Corpus Summarization](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)" (Modellazione di argomenti latenti per il riepilogo di raccolte audio).

Il notebook `5_Topic_Model_Analysis.ipynb` mostra come analizzare gli argomenti del contenuto di una raccolta di documenti e correlare le informazioni sugli argomenti con altri metadati associati alla raccolta stessa. Alcuni tracciati sono stati introdotti in questo notebook per consentire agli utenti di comprendere meglio l'argomento appreso e la raccolta di documenti.

## <a name="conclusion"></a>Conclusioni

Questo scenario reale illustra come usare tecniche di analisi del testo note (in questo caso l'apprendimento di frase e la modellazione di argomento LDA) per produrre un modello affidabile e come Azure Machine Learning Workbench consente di tenere traccia delle prestazioni del modello ed eseguire facilmente gli strumenti di apprendimento su scala maggiore. Maggiori dettagli:

* Usare l'apprendimento di frase e la modellazione di argomento per elaborare una raccolta di documenti e compilare un modello solido. Se la raccolta di documenti è enorme, Azure Machine Learning Workbench può scalare verticalmente o orizzontalmente con facilità. Inoltre, gli utenti hanno la possibilità di eseguire esperimenti in contesti di calcolo diversi dall'interno di Azure Machine Learning Workbench.

* Azure Machine Learning Workbench offre sia l'opzione di eseguire i notebook passo per passo sia la possibilità di usare lo script Python per eseguire un intero esperimento.

* Ottimizzazione del parametro hyper usando Azure Machine Learning Workbench per trovare il miglior numero di argomenti necessari per l'apprendimento. Azure Machine Learning Workbench consente di tenere traccia delle prestazioni del modello ed eseguire facilmente diversi strumenti di apprendimento su scala maggiore.

* Azure Machine Learning Workbench può gestire la cronologia di esecuzione e i modelli appresi. Consente ai data scientist di identificare rapidamente i modelli con le migliori prestazioni e di individuare gli script e i dati utilizzati per generarli.

## <a name="references"></a>Riferimenti

* **Timothy J. Hazen, Fred Richardson**, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf) (Modellazione di frasi di più parole con una struttura di frase vincolata per una migliorata modellazione degli argomenti delle conversazioni). Workshop sulla tecnologia della lingua parlata (SLT), IEEE 2012. IEEE, 2012.

* **Timothy J. Hazen**, [_Latent Topic Modeling for Audio Corpus Summarization_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf) (Modellazione di argomenti latenti per il riepilogo di raccolte audio). Dodicesima conferenza annuale dell'Associazione Internazionale di Comunicazione Vocale. 2011.

* **Michael Roder, Andreas Both, Alexander Hinneburg**, [_Exploring the Space of Topic Coherence Measures_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf) (Esplorazione dello spazio delle misure di coerenza degli argomenti). Procedimenti dell'ottava conferenza internazionale ACM sulla ricerca nel Web e il data mining. ACM, 2015.
