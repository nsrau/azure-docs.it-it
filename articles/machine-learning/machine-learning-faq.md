---
title: Domande frequenti su Azure Machine Learning | Documentazione Microsoft
description: "Introduzione ad Azure Machine Learning; domande frequenti su fatturazione, funzionalità e limitazioni di un servizio cloud per la modellazione predittiva semplificata."
keywords: "introduzione al machine learning, modellazione predittiva, cos’è il machine learning"
services: machine-learning
documentationcenter: 
author: garyericson
manager: paulettm
editor: cgronlun
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 21d2c20a3ac8dc4fee0680028d6c004c80a3ae38
ms.openlocfilehash: 2835e4e8be2a11b659118f836d53c84a2c021263
ms.lasthandoff: 04/18/2017


---
# <a name="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Domande frequenti su Azure Machine Learning: fatturazione, funzionalità, limitazioni e supporto
Di seguito sono riportate alcune domande frequenti e le corrispondenti risposte su Azure Machine Learning, un servizio cloud che consente di sviluppare modelli predittivi e rendere operative le soluzioni tramite servizi Web. Questo articolo contiene domande sull'uso del servizio, includendo il modello di fatturazione, le funzionalità, le limitazioni e il supporto.

## <a name="general-questions"></a>Domande generali
**Cos'è Azure Machine Learning?**

Azure Machine Learning è un servizio completamente gestito che è possibile usare per creare, testare, usare e gestire le soluzioni per l'analisi predittiva nel cloud. Con un semplice browser è possibile accedere, caricare i dati e avviare immediatamente esperimenti di Machine Learning. La modellazione predittiva con trascinamento della selezione, un'ampia gamma di moduli e una libreria di modelli iniziali consentono di eseguire le attività comuni di Machine Learning in modo semplice e rapido. Per altre informazioni, vedere la [panoramica del servizio di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Per un'introduzione all'apprendimento automatico, con una spiegazione dei concetti e della terminologia di base, vedere l'[introduzione ad Azure Machine Learning](machine-learning-what-is-machine-learning.md).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Cos'è Machine Learning Studio?**

Machine Learning Studio è un ambiente workbench accessibile con un Web browser. Machine Learning Studio ospita diversi moduli in un'interfaccia di composizione visiva che consente di compilare un flusso di lavoro di analisi scientifica dei dati end-to-end sotto forma di esperimento.

Per altre informazioni su Machine Learning Studio, vedere [Cos'è Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

**Cos'è il servizio API di Machine Learning?**

Il servizio API di Machine Learning consente di distribuire modelli predittivi, ad esempio quelli compilati in Machine Learning Studio, sotto forma di servizi Web scalabili e a tolleranza di errore. I servizi Web creati dal servizio API di Machine Learning sono API REST che offrono un'interfaccia per la comunicazione tra le applicazioni esterne e i modelli di analisi predittiva.

Per altre informazioni, vedere [Connessione a un servizio Web di Azure Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).

**Dove sono elencati i servizi Web classici? Dove sono elencati i nuovi servizi Web basati su Azure Resource Manager?**

I servizi Web creati con il modello di distribuzione classica e quelli creati con il nuovo modello di distribuzione Azure Resource Manager sono elencati nel portale [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) (Servizi Web di Microsoft Azure Machine Learning).

I servizi Web classici sono elencati anche nella scheda **Servizi Web** di [Machine Learning Studio](http://studio.azureml.net).

## <a name="azure-machine-learning-questions"></a>Domande su Azure Machine Learning
**Che cosa sono i servizi Web di Azure Machine Learning?**

I servizi Web di Machine Learning offrono un'interfaccia tra un'applicazione e un modello di assegnazione dei punteggi del flusso di lavoro di Machine Learning. Un'applicazione esterna può usare Azure Machine Learning per comunicare con un modello di assegnazione dei punteggi del flusso di lavoro di Machine Learning in tempo reale. Una chiamata al servizio Web di Machine Learning restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata a un servizio Web, passare una chiave API creata al momento della distribuzione del servizio Web. Un servizio Web di Machine Learning è basato su REST, un'opzione di architettura diffusa per progetti di programmazione Web.

Azure Machine Learning offre due tipi di servizi Web.

* Servizio richiesta-risposta (RRS): un servizio a bassa latenza e scalabilità elevata che offre un'interfaccia per i modelli senza stato creati e distribuiti con Machine Learning Studio.
* Servizio esecuzione batch (BES): un servizio asincrono per l'assegnazione di punteggi a un batch di record di dati.

È possibile utilizzare l'API REST e accedere al servizio Web in diversi modi. È ad esempio possibile scrivere un'applicazione in C#, R o Python usando il codice di esempio generato automaticamente quando è stato distribuito il servizio Web.

Il codice di esempio è disponibile nelle posizioni seguenti:
- Nella pagina di utilizzo del servizio Web nel portale dei servizi Web di Microsoft Azure Machine Learning
- Nella pagina della Guida dell'API nel dashboard del servizio Web in Machine Learning Studio

È anche possibile usare la cartella di lavoro di Microsoft Excel di esempio creata automaticamente e disponibile nel dashboard del servizio Web in Machine Learning Studio.

**Quali sono i principali aggiornamenti di Azure Machine Learning?**

Per informazioni sugli aggiornamenti più recenti, vedere [Novità in Azure Machine Learning](machine-learning-whats-new.md).

## <a name="machine-learning-studio-questions"></a>Domande su Machine Learning Studio
### <a name="import-and-export-data-for-machine-learning"></a>Importare ed esportare dati per Machine Learning
**Quali origini dati sono supportate da Machine Learning?**

Per scaricare dati in un esperimento di Machine Learning Studio sono disponibili tre modi:

- Caricare un file locale come set di dati
- Usare un modulo per importare dati da servizi dati cloud
- Importare un set di dati salvato da un altro esperimento

Per altre informazioni sui formati di file supportati, vedere [Importare dati di training in Azure Machine Learning Studio](machine-learning-data-science-import-data.md).

#### <a id="ModuleLimit"></a>Qual è la grandezza massima dei set di dati per i moduli?
I moduli in Machine Learning Studio supportano set di dati fino a 10 GB di dati numerici ad alta densità per i casi di utilizzo comuni. Se un modulo richiede più input, il valore di 10 GB rappresenta il totale delle dimensioni di tutti gli input. È anche possibile campionare set di dati di dimensioni maggiori con query di Hive o del database SQL di Azure oppure usare la pre-elaborazione di Learning by Counts (Apprendimento in base a conteggi) prima dell'inserimento.  

I tipi di dati seguenti possono espandersi in set di dati di dimensioni maggiori durante la normalizzazione della funzionalità e sono limitati a meno di 10 GB:

* Sparse
* Categorical
* Stringhe
* Dati binari

I moduli seguenti sono limitati a set di dati inferiori a 10 GB:

* Moduli di raccomandazione
* Modulo SMOTE (Synthetic Minority Oversampling Technique)
* Moduli di script: R, Python, SQL
* Moduli in cui la dimensione dei dati di output può essere maggiore della dimensione dei dati di input, come Join (Unione) o Feature Hashing (Hash delle funzionalità)
* Convalida incrociata, ottimizzazione degli iperparametri del modello, regressione ordinale e multiclasse uno-tutti, quando il numero di iterazioni è molto elevato

#### <a id="UploadLimit"></a>Quali sono i limiti per il caricamento di dati?
Per i set di dati di dimensioni maggiori di alcuni GB, caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare Azure HDInsight, anziché eseguire il caricamento diretto da un file locale.

**È possibile leggere i dati da Amazon S3?**

Se la quantità di dati è limitata e si vuole esporli con un URL HTTP, è possibile usare il modulo [Import Data][import-data] (Importa dati). Per quantità di dati maggiori, trasferirli prima di tutto in Archiviazione di Azure e quindi usare il modulo [Import Data][import-data] (Importa dati) per passarli all'esperimento.
<!--

<SEE CLOUD DS PROCESS>
-->

**Esiste una capacità di input dell'immagine predefinita?**

Per informazioni sulla capacità di input dell'immagine, vedere [Import Images][image-reader] (Importare immagini).

### <a name="modules"></a>Moduli
**L'algoritmo, l'origine dati, il formato dei dati o l'operazione di trasformazione dei dati che si sta cercando non è presente in Azure Machine Learning Studio. Quali sono le opzioni disponibili?**

È possibile accedere al [forum dei commenti e suggerimenti degli utenti](http://go.microsoft.com/fwlink/?LinkId=404231) per visualizzare le richieste di funzionalità sotto esame. Se una funzionalità cercata è già stata richiesta, aggiungere il proprio voto alla richiesta. Se la funzionalità cercata non esiste, creare una nuova richiesta. In questo forum è anche possibile visualizzare lo stato della richiesta. Questo elenco viene controllato regolarmente e lo stato di disponibilità delle funzionalità viene aggiornato di frequente. È anche possibile usare il supporto predefinito per R e Python per creare trasformazioni personalizzate in base alle proprie esigenze.

**È possibile trasferire un proprio codice esistente in Machine Learning Studio?**

Sì, è possibile trasferire un proprio codice R o Python esistente in Machine Learning Studio, eseguirlo nello stesso esperimento con gli strumenti di apprendimento di Azure Machine Learning, quindi distribuire la soluzione come servizio Web tramite Azure Machine Learning. Per altre informazioni, vedere [Estendere l'esperimento con R](machine-learning-extend-your-experiment-with-r.md) ed [Eseguire gli script di apprendimento automatico di Python in Azure Machine Learning Studio](machine-learning-execute-python-scripts.md).

**È possibile usare un codice tipo [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) per definire un modello?**

No. Il linguaggio PMML (Predictive Model Markup Language) non è supportato. Per definire un modulo è possibile usare codice R e Python personalizzato.

**Quanti moduli è possibile eseguire in parallelo nell'esperimento?**  

È possibile eseguire fino a quattro moduli in parallelo in un esperimento.

### <a name="data-processing"></a>Elaborazione dei dati
**Esiste la possibilità di visualizzare i dati (oltre alle visualizzazioni R) in modo interattivo all'interno dell'esperimento?**

Fare clic sull'output di un modulo per visualizzare i dati e ottenere le statistiche.

**Quando si visualizza l'anteprima dei risultati o dei dati in un browser, il numero di righe e colonne è limitato. Perché?**

Dato che al browser potrebbero essere inviate grandi quantità di dati, la dimensione dei dati viene limitata per evitare il rallentamento di Machine Learning Studio. Per visualizzare tutti i dati/risultati, è preferibile scaricare i dati e usare Excel o un altro strumento.

### <a name="algorithms"></a>Algoritmi
**Quali algoritmi esistenti sono supportati in Machine Learning Studio?**

Machine Learning Studio offre algoritmi all'avanguardia, ad esempio gli alberi delle decisioni con boosting scalabili, i sistemi di raccomandazione bayesiani, le reti neurali basate su Machine Deep Learning e le giungle delle decisioni sviluppati da Microsoft Research. Sono inclusi anche pacchetti open source scalabili di apprendimento automatico come Vowpal Wabbit. Machine Learning Studio supporta gli algoritmi di Machine Learning per la classificazione multiclasse e binaria, la regressione e il clustering. Vedere l'elenco completo dei [moduli di Machine Learning][machine-learning-modules].

**L'algoritmo di Machine Learning più appropriato per i dati viene suggerito automaticamente?**

No. In Machine Learning Studio, tuttavia, esistono diversi modi per confrontare i risultati di ogni algoritmo in modo da determinare quello più adatto per la risoluzione del problema.

**Sono disponibili delle linee guida su come scegliere un algoritmo rispetto a tutti gli algoritmi forniti?**

Vedere [Come scegliere un algoritmo](machine-learning-algorithm-choice.md).

**Gli algoritmi forniti sono scritti in R o Python?**

No. Questi algoritmi sono scritti principalmente in linguaggi compilati per offrire prestazioni superiori.

**Sono disponibili informazioni dettagliate sugli algoritmi forniti?**

La documentazione offre alcune informazioni sugli algoritmi. Sono inoltre descritti i parametri disponibili per l'uso per ottimizzare l'algoritmo.  

**È previsto il supporto per l'apprendimento online?**

No. Attualmente è supportata solo la ripetizione del training a livello di codice.

**È possibile visualizzare i livelli di un modello di rete neurale usando il modulo predefinito?**

No.

**È possibile creare dei moduli personalizzati in C# o in altri linguaggi?**

Attualmente, è possibile usare solo R per creare nuovi moduli personalizzati.

### <a name="r-module"></a>Modulo R
**Quali pacchetti R sono disponibili in Machine Learning Studio?**

Machine Learning Studio attualmente supporta oltre 400 pacchetti CRAN-R. Vedere l'[elenco aggiornato](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) di tutti i pacchetti inclusi. Vedere anche [Estendere l'esperimento con R](machine-learning-extend-your-experiment-with-r.md) per informazioni su come recuperare personalmente tale elenco. Se il pacchetto desiderato non compare nell'elenco, specificare il nome del pacchetto nel [forum dei commenti e suggerimenti degli utenti](http://go.microsoft.com/fwlink/?LinkId=404231).

**È possibile compilare un modulo R personalizzato?**

Sì. Per altre informazioni, vedere [Creare moduli R personalizzati in Azure Machine Learning](machine-learning-custom-r-modules.md).

**Esiste un ambiente REPL per R?**

No, non esistono ambienti REPL (Read-Eval-Print-Loop) per R in Studio.

### <a name="python-module"></a>Modulo Python
**È possibile compilare un modulo Python personalizzato?**

Anche se non attualmente, è possibile usare uno o più moduli [Execute Python Script][python] (Esegui script Python) per ottenere lo stesso risultato.

**Esiste un ambiente REPL per Python?**

È possibile usare Jupyter Notebook in Machine Learning Studio. Per altre informazioni, vedere [Introducing Jupyter Notebooks in Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)(Introduzione di Jupyter Notebook in Azure Machine Learning Studio).

## <a name="web-service"></a>Servizio Web
### <a name="retrain"></a>Ripetere il training
**Come è possibile ripetere il training dei modelli di Azure Machine Learning a livello di codice?**

Usare le API per la ripetizione del training. Per altre informazioni, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](machine-learning-retrain-models-programmatically.md). È disponibile codice di esempio anche nella [demo sulla ripetizione del training di Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

### <a name="create"></a>Create
**È possibile distribuire il modello in locale o in un'applicazione senza una connessione Internet?**

No.

**Esiste una latenza di base prevista per tutti i servizi Web?**

Vedere l'articolo relativo ai [limiti delle sottoscrizioni di Azure](../azure-subscription-service-limits.md).

### <a name="use"></a>Uso
**Quando si esegue il modello predittivo come servizio di esecuzione batch invece che come servizio richiesta-risposta?**

Il servizio richiesta-risposta (RRS) è un servizio Web a bassa latenza e a scalabilità elevata usato per fornire un'interfaccia ai modelli senza stato creati e distribuiti dall'ambiente dell'esperimento. Il servizio esecuzione batch (BES) è un servizio per l'assegnazione asincrona di punteggi a un batch di record di dati. L'input per BES è simile all'input di dati usato da RRS. La differenza principale è che BES legge un blocco di record da diverse origini, ad esempio un archivio BLOB di Azure, un archivio tabelle di Azure, un database SQL di Azure, HDInsight (query Hive) e origini HTTP. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato](machine-learning-consume-web-services.md).

**Come si aggiorna il modello per il servizio Web distribuito?**

Per aggiornare un modello predittivo per un servizio già distribuito, modificare e rieseguire l'esperimento usato per creare e salvare il modello con training. Quando è disponibile una nuova versione del modello con training, Machine Learning Studio chiede all'utente se vuole aggiornare il servizio Web. Per informazioni dettagliate su come aggiornare un servizio Web distribuito, vedere [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

Inoltre, è possibile utilizzare le API Retraining.
Per altre informazioni, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](machine-learning-retrain-models-programmatically.md). È disponibile codice di esempio anche nella [demo sulla ripetizione del training di Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Come monitorare il servizio Web distribuito in produzione?**

Dopo aver distribuito un modello predittivo, è possibile monitorarlo dal portale di Azure classico (solo in caso di servizi Web classici) o dal portale dei servizi Web di Azure Machine Learning. Ogni servizio distribuito ha un proprio dashboard, in cui è possibile visualizzare le informazioni di monitoraggio per il servizio. Per altre informazioni sulla gestione dei servizi Web distribuiti, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](machine-learning-manage-new-webservice.md) e [Gestire un'area di lavoro di Azure Machine Learning](machine-learning-manage-workspace.md).

**Esiste una posizione in cui è possibile visualizzare l'output di RRS/BES?**

Per RRS, la risposta del servizio web si trova in genere dove si visualizza il risultato. Può anche essere scritta nell'archivio BLOB di Azure. Per BES, l'output viene scritto in un BLOB per impostazione predefinita. È anche possibile scrivere l'output in un database o una tabella usando il modulo [Export Data][export-data] (Esporta dati).

**È possibile creare servizi Web solo da modelli creati in Machine Learning Studio?**

No. È anche possibile creare servizi Web direttamente con Jupyter Notebook e RStudio.

**Dove è possibile trovare informazioni sui codici di errore?**

Per un elenco dei codici di errore e delle relative descrizioni, vedere [Codici di errore dei moduli di Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) .

## <a name="scalability"></a>Scalabilità
**Che cos'è la scalabilità del servizio Web?**

Attualmente, il provisioning dell'endpoint predefinito viene effettuato con 20 richieste RRS simultanee per ogni endpoint. È possibile ridimensionare le richieste simultanee a 200 per endpoint e ridimensionare ogni servizio Web a 10.000 endpoint per servizio Web come descritto in [Ridimensionamento di un servizio Web](machine-learning-scaling-webservice.md). Per BES, ogni endpoint può elaborare 40 richieste alla volta e le richieste aggiuntive oltre 40 vengono messe in coda. Queste richieste in coda vengono eseguite automaticamente allo svuotamento della coda.

**I processi R sono distribuiti in più nodi?**

No.  

**Quanti dati è possibile usare per il training?**

I moduli in Machine Learning Studio supportano set di dati fino a 10 GB di dati numerici ad alta densità per i casi di utilizzo comuni. Se un modulo richiede più input, la dimensione totale di tutti gli input è 10 GB. È anche possibile campionare set di dati di dimensioni maggiori tramite query di Hive o del database SQL di Azure oppure tramite la pre-elaborazione con moduli [Learning with Counts][counts] (Apprendimento in base a conteggi) prima dell'inserimento.  

I tipi di dati seguenti possono espandersi in set di dati di dimensioni maggiori durante la normalizzazione della funzionalità e sono limitati a meno di 10 GB:

* Sparse
* Categorical
* Stringhe
* Dati binari

I moduli seguenti sono limitati a set di dati inferiori a 10 GB:

* Moduli di raccomandazione
* Modulo SMOTE (Synthetic Minority Oversampling Technique)
* Moduli di script: R, Python, SQL
* Moduli in cui la dimensione dei dati di output può essere maggiore della dimensione dei dati di input, come Join (Unione) o Feature Hashing (Hash delle funzionalità)
* Convalida incrociata, ottimizzazione degli iperparametri del modello, regressione ordinale e multiclasse uno-tutti, quando il numero di iterazioni è molto elevato

Per i set di dati di dimensioni maggiori di alcuni GB, caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare HDInsight, anziché eseguire il caricamento diretto da un file locale.

**Esistono limitazioni per la dimensione dei vettori?**

Le righe e le colonne sono limitate singolarmente in base alla limitazione .NET di intervallo massimo: 2.147.483.647.

**È possibile modificare le dimensioni della macchina virtuale che esegue il servizio Web?**

No.  

## <a name="security-and-availability"></a>Sicurezza e disponibilità
**Chi può accedere per impostazione predefinita all'endpoint HTTP per il servizio Web? Come limitare l'accesso all'endpoint?**

Dopo avere distribuito un servizio Web, viene creato un endpoint predefinito per tale servizio. L'endpoint predefinito può essere chiamato usando la chiave API. È possibile aggiungere altri endpoint con le rispettive chiavi dal portale di Azure classico o a livello di codice usando le API Gestione servizi Web. Le chiavi di accesso sono necessarie per effettuare le chiamate al servizio Web. Per altre informazioni, vedere [Connessione a un servizio Web di Azure Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).

**Cosa succede se non viene trovato l'account di archiviazione di Azure?**

Machine Learning Studio si basa su un account di archiviazione di Azure specificato dall'utente per salvare i dati intermedi quando esegue il flusso di lavoro. Questo account di archiviazione viene fornito a Machine Learning Studio quando si crea un'area di lavoro. Dopo la creazione dell'area di lavoro, se l'account di archiviazione viene eliminato e non è più disponibile, l'area di lavoro non funzionerà più e tutti gli esperimenti al suo interno avranno esito negativo.

Se si elimina accidentalmente l'account di archiviazione, ricrearlo con lo stesso nome e nella stessa area dell'account di archiviazione eliminato. Risincronizzare quindi la chiave di accesso.

**Cosa succede se la chiave di accesso dell'account di archiviazione non è sincronizzata?**

Machine Learning Studio si basa su un account di archiviazione di Azure specificato dall'utente per archiviare i dati intermedi quando esegue il flusso di lavoro. Questo account di archiviazione viene fornito a Machine Learning Studio quando si crea un'area di lavoro e le chiavi di accesso sono associate all'area di lavoro. Se le chiavi di accesso vengono modificate dopo la creazione dell'area di lavoro, l'area di lavoro non riuscirà più ad accedere all'account di archiviazione. L'area di lavoro non funzionerà più e tutti gli esperimenti al suo interno avranno esito negativo.

Se sono state modificate le chiavi di accesso dell'account di archiviazione, risincronizzare le chiavi di accesso nell'area di lavoro usando il portale di Azure classico.  

## <a name="support-and-training"></a>Supporto e training
**Dove si trovano i training per Azure Machine Learning?**

Nel [centro di documentazione di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) sono disponibili esercitazioni video e guide alle procedure. Queste guide dettagliate offrono un'introduzione ai servizi e illustrano il ciclo di vita dell'analisi scientifica dei dati per l'importazione dei dati, la pulizia dei dati, la compilazione di modelli predittivi e la relativa distribuzione in produzione con Azure Machine Learning.

Microsoft aggiunge regolarmente nuovo materiale in Machine Learning Center. Per ottenere altro materiale di apprendimento in Machine Learning Center, inviare una richiesta nel [forum dei commenti e suggerimenti degli utenti](https://windowsazure.uservoice.com/forums/257792-machine-learning).

È disponibile formazione anche in [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Come si ottiene il supporto per Azure Machine Learning?**

Per ottenere supporto tecnico per Azure Machine Learning, accedere a [Supporto tecnico di Azure](/support/options/) e selezionare **Machine Learning**.

È anche disponibile un forum della community di Azure Machine Learning su MSDN, in cui è possibile porre domande su Azure Machine Learning. Il forum è monitorato dal team di Azure Machine Learning. Accedere al [forum di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Domande sulla fatturazione
**Come funziona la fatturazione di Machine Learning?**

Azure Machine Learning include due componenti: Machine Learning Studio e i servizi Web di Machine Learning.

Per la valutazione di Machine Learning Studio si può usare il livello di fatturazione Gratuito. Il livello Gratuito consente anche di distribuire un servizio Web classico con capacità limita.

Se si stabilisce che Azure Machine Learning soddisfa le proprie esigenze, è possibile iscriversi al livello Standard. Per effettuare l'iscrizione, è necessario avere una sottoscrizione di Microsoft Azure.

Nel livello Standard viene addebitato un importo mensile per ogni area di lavoro definita in Machine Learning Studio. Quando si esegue un esperimento in Studio, vengono fatturate le risorse di calcolo durante l'esecuzione dell'esperimento. Quando si distribuisce un servizio Web classico, le transazioni e le ore di calcolo vengono fatturate con pagamento in base al consumo.

I nuovi servizi Web basati su Resource Manager introducono piani di fatturazione che consentono una maggiore prevedibilità dei costi. I prezzi a livelli offrono tariffe scontate ai clienti che necessitano di capacità elevata.

Quando si crea un piano, si assume l'impegno di un costo fisso che copre una quantità inclusa di ore di calcolo API e transazioni API. Se sono necessarie più quantità incluse, è possibile aggiungere istanze al piano. Se sono necessarie molte quantità incluse aggiuntive, è possibile scegliere un livello superiore che offre un numero di quantità incluse notevolmente maggiore e un tariffa scontata migliore.

All'esaurimento delle quantità incluse delle istanze esistenti, per l'utilizzo aggiuntivo viene addebitata una tariffa di eccedenza associata al livello del piano di fatturazione.

> [!NOTE]
Le quantità incluse vengono riallocate ogni 30 giorni e quelle inutilizzate non vengono rinnovate nel periodo successivo.

Per altre informazioni sulla fatturazione e sui prezzi, vedere [Prezzi di Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**Machine Learning offre una versione di valutazione gratuita?**

 Azure Machine Learning offre un'opzione di sottoscrizione gratuita, illustrata in [Prezzi di Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/). Machine Learning Studio offre una versione di valutazione rapida di otto ore, disponibile all'accesso a [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2).

 Quando ci si iscrive a una versione di valutazione gratuita di Azure, è inoltre possibile provare tutti i servizi Azure per un mese. Per altre informazioni sulla versione di valutazione gratuita di Azure, vedere le relative [domande frequenti](https://azure.microsoft.com/pricing/free-trial-faq/).

**Che cos'è una transazione?**

Una transazione rappresenta una chiamata API a cui Azure Machine Learning risponde. Le transazioni di chiamate al servizio richiesta risposta (RRS) e al servizio esecuzione batch (BES) vengono aggregate e addebitate nel piano di fatturazione.

**Le quantità incluse di transazioni di un piano possono essere usate per transazioni sia RRS che BES?**

Sì. Le transazioni dei servizi RRS e BES vengono aggregate e addebitate nel piano di fatturazione.

**Che cos'è un'ora di calcolo API?**

Un'ora di calcolo API è l'unità di fatturazione per il tempo impiegato per l'esecuzione di chiamate API con le risorse di calcolo di Machine Learning. Ai fini della fatturazione, tutte le chiamate vengono aggregate.

**Quanto tempo è necessario in genere per una tipica chiamata API di produzione?**

I tempi delle chiamate API di produzione possano variare in modo significativo, in genere da centinaia di millisecondi a pochi secondi. A seconda della complessità dei modelli di elaborazione dei dati e di apprendimento automatico, per alcune chiamate API potrebbero essere necessari alcuni minuti. Il modo migliore per stimare i tempi delle chiamate API di produzione consiste nell'usare come benchmark un modello nel servizio Machine Learning.

**Che cos'è un'ora di calcolo di Studio?**

Un'ora di calcolo di Studio è l'unità di fatturazione per il tempo aggregato in cui gli esperimenti usano risorse di calcolo in Studio.

**Nei nuovi servizi Web basati su Azure Resource Manager, qual è lo scopo del livello Sviluppo/test?**

I servizi Web basati su Resource Manager offrono più livelli per il provisioning del piano di fatturazione. Il piano tariffario Sviluppo/test offre quantità incluse limitate che consentono di testare l'esperimento come servizio Web senza addebito di costi. Viene offerta l'opportunità di osservare il funzionamento.

**Sono previsti addebiti distinti per l'archiviazione?**

Il livello Gratuito di Machine Learning non richiede né consente l'archiviazione separata. Per il livello Standard di Machine Learning, gli utenti devono possedere un account di archiviazione di Azure. L'archiviazione di Azure [viene fatturata separatamente](https://azure.microsoft.com/pricing/details/storage/).

**Machine Learning supporta la disponibilità elevata?**

Sì. Per informazioni dettagliate, vedere [Prezzi di Machine Learning](https://azure.microsoft.com/en-us/pricing/details/machine-learning/) per una descrizione del contratto di servizio.

**In quale tipologia specifica di risorse di calcolo verranno eseguite le chiamate API di produzione?**

Machine Learning è un servizio multi-tenant. Le risorse di calcolo effettive usate nel back-end variano e sono ottimizzate ai fini delle prestazioni e della prevedibilità.

### <a name="management-of-new-resource-manager-based-web-services"></a>Gestione dei nuovi servizi Web basati su Resource Manager
**Cosa succede se si elimina il proprio piano?**

Il piano viene rimosso dalla sottoscrizione e viene fatturato un utilizzo ripartito.

> [!NOTE]
Un piano usato da un servizio Web non può essere eliminato. Per eliminare il piano, è necessario assegnare un nuovo piano al servizio Web oppure eliminare il servizio Web.

**Che cos'è un'istanza del piano?**

Un'istanza del piano è un'unità di quantità incluse che è possibile aggiungere al piano di fatturazione. Quando si seleziona un livello per il piano di fatturazione, questo include un'istanza. Se sono necessarie più quantità incluse, è possibile aggiungere istanze del livello di fatturazione selezionato al piano.

**Quante istanze del piano possono essere aggiunte?**

Una sottoscrizione può includere una sola istanza del piano tariffario Sviluppo/test.

Per i livelli S1 Standard, S2 Standard e S3 Standard è possibile aggiungere tutte le istanze necessarie.

> [!NOTE]
A seconda dell'utilizzo previsto, potrebbe essere più conveniente eseguire l'aggiornamento a un livello con un numero superiore di quantità incluse anziché aggiungere istanze al livello corrente.

**Cosa succede quando si cambiano i livelli del piano (per aggiornamento o downgrade)?**

Il piano precedente viene eliminato e la fatturazione dell'utilizzo corrente viene ripartita. Per il resto del periodo viene creato un nuovo piano con le intere quantità incluse del livello di aggiornamento o downgrade.

> [!NOTE]
Le quantità incluse vengono allocate per periodo e le quantità inutilizzate non vengono rinnovate.

**Cosa succede quando si aumentano le istanze in un piano?**

Le quantità incluse vengono ripartite e potrebbero trascorrere fino a 24 ore prima che vengano applicate.

**Cosa succede quando si elimina un'istanza di un piano?**

L'istanza viene rimossa dalla sottoscrizione e viene fatturato un utilizzo ripartito.

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>Iscriversi ai nuovi servizi Web basati su Resource Manager
**Come ci si iscrive a un piano?**

Per creare i piani di fatturazione sono disponibili due modi.

Quando si distribuisce per la prima volta un servizio Web basato su Resource Manager, si può scegliere un piano esistente o crearne uno nuovo.

I piani creati in questo modo rientrano nell'area predefinita dell'utente, in cui il servizio Web verrà distribuito.

Se si vogliono distribuire i servizi in aree diverse dalla propria area predefinita, può essere opportuno definire i piani di fatturazione prima di distribuire il servizio.

In tal caso, è possibile accedere al portale Azure Machine Learning Web Services (Servizi Web di Microsoft Azure Machine Learning) e andare alla pagina Plans (Piani). In questa pagina è possibile aggiungere ed eliminare piani, nonché modificare i piani esistenti.

**Quale piano è consigliabile scegliere come punto di partenza?**

È consigliabile partire dal livello S1 Standard e monitorare l'utilizzo del servizio. Se si riscontra che le quantità incluse vengono usate rapidamente, è possibile aggiungere istanze oppure passare a un livello superiore e usufruire di tariffe scontate migliori. Il piano di fatturazione può essere modificato in base alle esigenze nell'intero ciclo di fatturazione.

**In quali aree sono disponibili i nuovi piani?**

I nuovi piani di fatturazione sono disponibili nelle tre aree di produzione in cui sono supportati i nuovi servizi Web:

* Stati Uniti centro-meridionali
* Europa occidentale
* Asia sudorientale

**Se si hanno servizi Web in più aree, è necessario un piano per ogni area?**

Sì. I prezzi dei piani variano in base all'area. Quando si distribuisce un servizio Web in un'altra area, è necessario assegnare al servizio un piano specifico di tale area. Per altre informazioni, vedere [Prodotti disponibili in base all'area]( https://azure.microsoft.com/regions/services/).

### <a name="new-web-services-overages"></a>Nuovi servizi Web: eccedenze
**Come si controlla se si è superato l'utilizzo del servizio Web?**

È possibile visualizzare l'uso in tutti i propri piani nella pagina Plans (Piani) del portale Azure Machine Learning Web Services (Servizi Web di Microsoft Azure Machine Learning). Accedere al portale e quindi fare clic sull'opzione di menu **Plans** (Piani).

Nelle colonne **Transactions** (Transazioni) e **Compute** (Calcolo) della tabella vengono visualizzate le quantità incluse del piano e la percentuale usata.

**Cosa succede quando si esauriscono le quantità incluse del piano tariffario Sviluppo/test?**

I servizi a cui è assegnato un piano tariffario Sviluppo/test vengono arrestati fino al periodo successivo o al passaggio a uno dei livelli a pagamento.

**Per i servizi Web classici e le eccedenze dei nuovi servizi Web basati su Resource Manager, come vengono calcolati i prezzi per i carichi di lavoro richiesta-risposta (RRS) e batch (BES)?**

Per un carico di lavoro RRS, vengono addebitati tutte le chiamate di transazioni API effettuate e il tempo di calcolo associato a tali richieste. I costi delle transazioni dell'API di produzione RRS vengono calcolati come numero totale delle chiamate API effettuate moltiplicato per il prezzo per 1.000 transazioni (ripartito per singola transazione). I costi delle ore di calcolo dell'API di produzione RRS vengono calcolati come quantità di tempo necessaria per l'esecuzione di ogni chiamata API moltiplicata per il numero totale di transazioni API moltiplicato per il prezzo per ora di calcolo dell'API di produzione.

Per l'eccedenza in S1 Standard, ad esempio, 1.000.000 transazioni API la cui esecuzione richiede 0,72 secondi ciascuna avranno come risultato $ 500 (1.000.000 * $ 0,50/1.000 transazioni API) come costo delle transazioni dell'API di produzione e $ 400 (1.000.000 * 0,72 secondi * $ 2/ora) per le ore di calcolo dell'API di produzione, per un totale di $ 900.

Per un carico di lavoro BES, l'addebito avviene allo stesso modo, ma il costo delle transazioni API rappresenta il numero di processi batch che si inviano e i costi di calcolo rappresentano il tempo di calcolo associato a tali processi batch. I costi delle transazioni dell'API di produzione BES vengono calcolati come numero totale di processi inviati moltiplicato per il prezzo per 1.000 transazioni (ripartito per singola transazione). I costi delle ore di calcolo dell'API di produzione BES vengono calcolati come quantità di tempo necessaria per l'esecuzione di ogni riga nel processo moltiplicata per il numero totale di righe nel processo moltiplicato per il numero totale di processi moltiplicato per il prezzo per ora di calcolo dell'API di produzione. Quando si usa il calcolatore di Machine Learning, la misurazione delle transazioni rappresenta il numero di processi che si intende inviare, mentre il campo del tempo per transazione rappresenta il tempo combinato necessario per l'esecuzione di tutte le righe in ogni processo.

Con l'eccedenza in S1 Standard, ad esempio, si supponga di inviare 100 processi al giorno ognuno dei quali è costituito da 500 righe che impiegano 0,72 secondi ciascuna. I costi di eccedenza mensili equivarranno a $ 1,55 (100 processi al giorno = 3.100 processi/mese * $ 0,50/1.000 transazioni API) come costo delle transazioni dell'API di produzione e a $ 620 (500 righe * 0,72 secondi * 3.100 processi * $ 2/ora) per le ore di calcolo dell'API di produzione, per un totale di $ 621,55.

### <a name="azure-machine-learning-classic-web-services"></a>Servizi Web classici di Azure Machine Learning
**È ancora disponibile il pagamento in base al consumo?**

Sì. In Azure Machine Learning sono ancora disponibili i servizi Web classici.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Livello Gratuito e Standard di Azure Machine Learning
**Che cosa include il livello Gratuito di Azure Machine Learning?**

Il livello Gratuito di Azure Machine Learning è concepito per offrire un'introduzione approfondita ad Azure Machine Learning Studio. Per iscriversi è sufficiente un account Microsoft. Il livello Gratuito include l'accesso gratuito a un'area di lavoro di Azure Machine Learning Studio per ogni [account Microsoft](https://www.microsoft.com/account/default.aspx). In questo livello, è possibile usare fino a 10 GB di spazio di archiviazione e rendere operativi i modelli come API di staging. I carichi di lavoro del livello Gratuito non sono coperti da un contratto di servizio e sono destinati esclusivamente allo sviluppo e all'uso personale. 

Per le aree di lavoro del livello Gratuito esistono le limitazioni seguenti:

* I carichi di lavoro non possono accedere ai dati connettendosi a un server locale che esegue SQL Server.
* Non è possibile distribuire nuovi servizi Web basati su Resource Manager.


**Cosa includono il livello e i piani Standard di Azure Machine Learning?**

Il livello Standard di Azure Machine Learning è una versione di produzione a pagamento di Azure Machine Learning Studio. La tariffa mensile di Azure Machine Learning Studio viene fatturata mensilmente per area di lavoro ed è ripartita per i mesi parziali. Le ore di sperimentazione in Azure Machine Learning Studio vengono fatturate per ora di calcolo per sperimentazione attiva. La fatturazione è ripartita per ore parziali.  

La fatturazione del servizio API di Azure Machine Learning varia a seconda che si tratti di un servizio Web classico o di un nuovo servizio Web basato su Resource Manager.

Gli addebiti seguenti vengono aggregati per ogni area di lavoro della sottoscrizione.

* Sottoscrizione per area di lavoro di Machine Learning: tariffa mensile che consente di accedere a un'area di lavoro di Machine Learning Studio. Questa sottoscrizione è necessaria per eseguire esperimenti in Studio e utilizzare le API di produzione.
* Ore sperimentazione in Studio: questa misurazione aggrega tutti i costi di calcolo accumulati eseguendo esperimenti in Machine Learning Studio e chiamate API di produzione nell'ambiente di staging.
* È possibile accedere ai dati con la connessione a un server locale che esegue SQL Server nei modelli per il training e l'assegnazione dei punteggi.
* Per i servizi Web classici:
  * Ore di calcolo dell'API di produzione. Questa misurazione include i costi di calcolo accumulati dai servizi Web eseguiti nell'ambiente di produzione.
  * Transazioni dell'API di produzione (in migliaia). Questa misurazione include i costi accumulati per ogni chiamata al servizio Web di produzione.

Oltre agli addebiti precedenti, nel caso dei servizi Web basati su Resource Manager vengono aggregati addebiti al piano selezionato.

* Piano API S1/S2/S3 Standard (unità): questa misurazione rappresenta il tipo di istanza selezionato per i servizi Web basati su Resource Manager.
* Ore di calcolo API S1/S2/S3 Standard in eccedenza: questa misurazione include i costi di calcolo accumulati dai servizi Web basati su Resource Manager che vengono eseguiti nell'ambiente di produzione dopo l'esaurimento delle quantità incluse delle istanze esistenti. L'utilizzo aggiuntivo viene addebitato alla tariffa di eccedenza associata al livello dei piani S1/S2/S3.
* Transazioni API S1/S2/S3 Standard in eccedenza (in migliaia): questa misurazione include i costi accumulati per ogni chiamata al servizio Web basato su Resource Manager di produzione dopo l'esaurimento delle quantità incluse delle istanze esistenti. L'utilizzo aggiuntivo viene addebitato alla tariffa di eccedenza associata al livello dei piani S1/S2/S3.
* Quantità inclusa di ore di calcolo API: con i servizi Web basati su Resource Manager, questa misurazione rappresenta la quantità inclusa di ore di calcolo API.
* Quantità inclusa di transazioni API (in migliaia): con i servizi Web basati su Resource Manager, questa misurazione rappresenta la quantità inclusa di transazioni API.

**Come ci si iscrive al livello Gratuito di Azure Machine Learning?**

È sufficiente un account Microsoft. Andare alla [home page di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) e quindi fare clic su **Per iniziare**. Accedere con l'account Microsoft. Verrà automaticamente creata un'area di lavoro nel livello Gratuito. È possibile iniziare immediatamente a esplorare e creare esperimenti di Machine Learning.

**Come ci si iscrive al livello Standard di Azure Machine Learning?**

Per creare un'area di lavoro di Machine Learning di livello Standard è prima di tutto necessario avere accesso a una sottoscrizione di Azure. È possibile iscriversi per ottenere una sottoscrizione della versione di valutazione gratuita di Azure di 30 giorni ed eseguire in seguito l'aggiornamento a una sottoscrizione di Azure a pagamento oppure acquistare direttamente una sottoscrizione di Azure a pagamento. Dopo aver ottenuto l'accesso alla sottoscrizione, è possibile creare un'area di lavoro di Machine Learning dal portale di Microsoft Azure classico. Vedere le [istruzioni dettagliate](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

In alternativa, si può essere invitati dal proprietario di un'area di lavoro di Machine Learning di livello Standard ad accedere alla sua area di lavoro.

**È possibile specificare un account di archiviazione BLOB di Azure personale da usare con il livello Gratuito?**

No. Il livello Standard equivale alla versione del servizio Machine Learning disponibile prima dell'introduzione dei livelli.

**È possibile distribuire i propri modelli di Machine Learning come API nel livello Gratuito?**

Sì. Nell'ambito del livello Gratuito è possibile rendere operativi i modelli di Machine Learning nei servizi API di staging. Per inserire il servizio API di staging in produzione e ottenere un endpoint di produzione per il servizio reso operativo, è necessario usare il livello Standard.

**Qual è la differenza tra la versione di valutazione gratuita di Azure e il livello Gratuito di Azure Machine Learning?**

La [versione di valutazione gratuita di Microsoft Azure](https://azure.microsoft.com/free/) offre crediti che possono essere applicati a qualsiasi servizio di Azure per un mese. Il livello Gratuito di Azure Machine Learning offre un accesso continuo specifico ad Azure Machine Learning per carichi di lavoro non di produzione.

**Come si trasferisce un esperimento dal livello Gratuito al livello Standard?**

Per copiare gli esperimenti dal livello gratuito al livello Standard:

1. Accedere ad Azure Machine Learning Studio e verificare che nel selettore delle aree di lavoro nella barra di spostamento in alto siano visualizzate sia l'area di lavoro del livello Gratuito sia quella del livello Standard.
2. Se ci si trova nell'area di lavoro del livello Standard, passare a quella del livello Gratuito.
3. Nella visualizzazione elenco degli esperimenti selezionare un esperimento da copiare e quindi fare clic sul pulsante di comando **Copy** (Copia).
4. Selezionare l'area di lavoro del livello Standard nella finestra di dialogo visualizzata e quindi fare clic sul pulsante **Copy** (Copia).
   Tutti i set di dati, il modello con training e così via verranno copiati insieme all'esperimento nell'area di lavoro del livello Standard.
5. Sarà necessario eseguire di nuovo l'esperimento e ripubblicare il servizio Web nell'area di lavoro del livello Standard.

### <a name="studio-workspace"></a>Area di lavoro di Studio
**Saranno emesse diverse fatture per le diverse aree di lavoro?**

Gli addebiti delle aree di lavoro vengono suddivisi separatamente per ogni misurazione applicabile in una singola fattura.

**In quale tipologia specifica di risorse di calcolo verranno eseguiti gli esperimenti?**

Machine Learning è un servizio multi-tenant. Le risorse di calcolo effettive usate nel back-end variano e sono ottimizzate ai fini delle prestazioni e della prevedibilità.

### <a name="guest-access"></a>Accesso guest
**Che cos'è l'accesso guest ad Azure Machine Learning Studio?**

L'accesso guest è un'esperienza di valutazione limitata. È possibile creare ed eseguire esperimenti in Azure Machine Learning Studio gratuitamente e senza autenticazione. Le sessioni guest sono non persistenti (non possono essere salvate) e limitate a otto ore. Le altre restrizioni includono la mancanza di supporto per R e Python, l'assenza di API di staging e dimensioni dei set di dati e capacità di archiviazione limitate. Gli utenti che scelgono di accedere con un account Microsoft, invece, hanno accesso completo al livello Gratuito di Machine Learning Studio descritto in precedenza, che include un'area di lavoro persistente e funzionalità più complete. Per scegliere un'esperienza gratuita di Machine Learning, fare clic sul pulsante **Get started** (Per iniziare) in [https://studio.azureml.net](https://studio.azureml.net) e quindi selezionare **Guest Access** (Accesso guest) oppure accedere con un account Microsoft.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx

