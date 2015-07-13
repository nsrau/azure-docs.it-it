<properties
	pageTitle="Pre-elaborare e pulire i dati | Microsoft Azure"
	description="Perché pre-elaborare e pulire i dati"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="msolhab;bradsev" />


# Preparazione dei dati per Machine Learning

La pre-elaborazione e la pulizia dei dati rappresentano attività importanti che in genere devono essere effettuate prima di usare un set di dati in modo efficace per Machine Learning. Questa attività fa parte di ADAPT (Advanced Analytics Process and Technology), un processo fornito da Azure Machine Learning. I dati non elaborati sono fastidiosi, non affidabili e potrebbero non contenere alcuni valori. Utilizzare tali dati per la modellazione può produrre risultati fuorvianti.

In questo articolo vengono presentati alcuni concetti e attività di elaborazione dei dati che possono essere effettuati prima dell'inserimento dei dati in Azure Machine Learning. Per ulteriori istruzioni su questo processo, vedere [Guida alla formazione: Elaborazione dati avanzata in Azure](machine-learning-data-science-advanced-data-processing.md)

----------------

## Perché pre-elaborare e pulire i dati?##

I dati reali vengono raccolti da diverse origini e processi e possono contenere irregolarità o dati danneggiati in grado di compromettere la qualità del set di dati. Di seguito sono riportati i tipici problemi relativi alla qualità di dati che si verificano:

* **Dati incompleti**: nei dati mancano attributi o non sono presenti valori.
* **Dati fastidiosi**: i dati contengono record o outlier errati.
* **Dati incoerenti**: i dati contengono record in conflitto o discrepanze.

La qualità dei dati è un prerequisito per modelli predittivi di qualità. Per evitare "spazzatura in entrata e in uscita" e migliorare la qualità dei dati e quindi le prestazioni del modello, è fondamentale condurre un'analisi dell'integrità dei dati per individuare tempestivamente problemi relativi ai dati e prendere decisioni in merito alle fasi di elaborazione e pulizia dei dati corrispondenti.

## Quali sono le tipiche analisi dell'integrità dei dati che vengono impiegate? ##

È possibile controllare la qualità generale dei dati controllando:

* Il numero di record.
* Il numero di attributi (o funzionalità).
* I tipi di dati di attributi (nominali, ordinali o continui).
* Il numero di valori mancanti.
* La formulazione dei dati. Se i dati sono in .tsv o .csv, viene controllato se i separatori di colonne e righe separano correttamente colonne e righe. Se i dati sono in formato HTML o XML, controllare se i dati sono formulati correttamente in base ai rispettivi standard. L'analisi potrebbe inoltre essere necessaria per estrarre informazioni strutturate da dati non strutturati o semistrutturati.
* Record di dati incoerenti. Ad esempio, se i dati contengono la media dello studente, controllare se il GPA è compreso nell'intervallo designato, ad esempio 0 ~ 4.

Quando si verificano problemi con i dati, sono necessari passaggi di elaborazione che spesso comportano la pulizia dei valori mancanti, normalizzazione dei dati, la discretizzazione, l'elaborazione di testo per rimuovere e/o sostituire i caratteri incorporati che possono influire sull'allineamento dei dati, tipi di dati misti nei campi comuni e altro.

Azure Machine Learning utilizza dati tabulari in formato corretto. Se i dati sono già in formato tabulare, la pre-elaborazione dei dati può essere eseguita direttamente con Azure Machine Learning in ML Studio. Se i dati non sono in formato tabulare, bensì in XML, l'analisi potrebbe essere necessaria per convertire i dati in formato tabulare.

## Quali sono alcune delle principali attività di pre-elaborazione dei dati? ##

* **Pulizia dei dati**: compilare o inserire i valori mancanti, individuare e rimuovere dati e outlier fastidiosi.
* **Trasformazione dei dati**: normalizzare i dati per ridurne dimensioni e disturbo.
* **Riduzione dei dati**: esempi di record di dati o attributi per una gestione dei dati semplificata.
* **Discretizzazione dei dati**: convertire gli attributi continui in attributi categorici per facilitare l'uso di alcuni metodi di machine learning.
* **Pulizia del testo**: rimuovere i caratteri incorporati che potrebbero causare problemi di allineamento dei dati, ad esempio, schede incorporate in un file di dati separato da tabulazioni, nuove righe incorporate che potrebbero interrompere record ecc.

Nelle sezioni seguenti vengono riportati alcuni dei passaggi di elaborazione dei dati.

## Come trattare i valori mancanti? ##

Per gestire i valori mancanti, è consigliabile innanzitutto identificare il motivo per cui mancano dei valori per una gestione più efficiente del problema. I metodi di gestione dei valori mancanti tipici sono:

* **Eliminazione**: rimuovere i record con valori mancanti
* **Sostituzione fittizia**: sostituire un valore mancante con un valore fittizio, ad esempio, _unknown_ per valori categorici o 0 per valori numerici.
* **Sostituzione con media**: se i dati mancanti sono numerici, sostituirli con la media.
* **Sostituzione con elementi frequenti**: se i dati mancanti sono categorici, sostituirli con gli elementi più frequenti
* **Sostituzione con regressione**: usare il metodo della regressione per sostituire i valori mancanti con valori regrediti.  

## Come normalizzare i dati? ##

La normalizzazione dei dati fa rientrare i valori numerici in un intervallo specifico. I metodi di normalizzazione dei dati più comuni includono:

* **Normalizzazione Min-Max**: consente di trasformare i dati in un intervallo in modo lineare, ad esempio compreso tra 0 e 1, dove il valore min corrisponde a 0 e il valore massimo a 1.
* **Normalizzazione Z-score**: consente di ridurre i dati in base alla media e alla deviazione standard. Si divide la differenza tra dati e media per la deviazione standard.
* **Scalabilità decimale**: consente di ridurre i dati spostando il punto decimale del valore dell'attributo.  

## Come eseguire la discretizzazione dei dati? ##

I dati possono essere discretizzati convertendo i valori continui in attributi o intervalli nominali. Ciò può essere fatto nei seguenti modi:

* **Creazione di contenitori di uguale ampiezza**: si divide l'intervallo di tutti i possibili valori di un attributo in N gruppi della stessa dimensione a cui si assegnano i valori che rientrano in un contenitore con il numero di contenitore.
* **Creazione di contenitori di uguale altezza**: si divide l'intervallo di tutti i possibili valori di un attributo in N gruppi, ciascuno contenente lo stesso numero di istanze, quindi si assegnano i valori che rientrano in un contenitore con il numero di contenitore.  

## Come ridurre i dati?  ## 

Esistono vari metodi per ridurre le dimensioni dei dati e renderne più semplice la gestione. A seconda della dimensione e del dominio dei dati, è possibile utilizzare i seguenti metodi:

* **Campionamento record**: i record di dati vengono campionati e viene scelto solo un sottoinsieme rappresentativo.
* **Campionamento attributi**: selezionare solo un sottoinsieme degli attributi più importanti dai dati.  
* **Aggregazione**: i dati vengono divisi in gruppi e per ogni gruppo vengono memorizzati i numeri. Ad esempio, i numeri dei ricavi giornalieri di una catena di ristoranti negli ultimi 20 anni possono essere aggregati in ricavi mensili per ridurre le dimensioni dei dati.  

## Come pulire i dati di testo? ##

**I campi di testo nei dati tabulari** possono includere caratteri che influiscono sull'allineamento delle colonne e/o sui limiti dei record. Ad esempio, le schede incorporate in un file separato da tabulazioni possono causare il disallineamento delle colonne e i caratteri di nuove righe incorporate possono interrompere le righe del record. La gestione della codifica del testo non corretta durante la scrittura/lettura del testo comporta la perdita di informazioni, l'introduzione involontaria di caratteri non leggibili, ad esempio, valori null e potrebbe inoltre influenzare l'analisi del testo. Potrebbero essere necessarie un'attenta analisi e la modifica per pulire i campi di testo e consentire il corretto allineamento e/o estrarre i dati strutturati da dati di testo non strutturati o semistrutturati.

L'**esplorazione dei dati** offre una visualizzazione anticipata dei dati. Durante questa fase è possibile individuare una serie di problemi ed è possibile utilizzare metodi idonei per risolverli. È importante porre domande, come ad esempio, qual è l'origine del problema e come il problema potrebbe presentarsi. Ciò consente inoltre di stabilire i passaggi di elaborazione dei dati che devono essere intrapresi per risolvere tali problemi. Il tipo di informazioni che si intende derivare dai dati può inoltre essere utilizzato per classificare lo sforzo di elaborazione dei dati.

## Riferimento ##

>_Data Mining: Concepts and Techniques_, Third Edition, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber, and Jian Pei
 

<!---HONumber=July15_HO1-->