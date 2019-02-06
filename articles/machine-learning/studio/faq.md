---
title: Domande frequenti su Azure Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: 'Azure Machine Learning Studio: domande frequenti su fatturazione, funzionalità e limitazioni di un servizio cloud per la modellazione predittiva semplificata.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462280"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Domande frequenti su Azure Machine Learning Studio: funzionalità e limitazioni
Di seguito sono riportate alcune domande frequenti e le corrispondenti risposte su Azure Machine Learning, un servizio cloud che consente di sviluppare modelli predittivi e rendere operative le soluzioni tramite servizi Web. 

## <a name="general-questions"></a>Domande generali
**Cos'è Machine Learning Studio?**

Machine Learning Studio è un ambiente accessibile tramite Web browser che offre un'area di disegno con funzionalità di trascinamento della selezione. Machine Learning Studio ospita diversi moduli in un'interfaccia di composizione visiva che consente di compilare un flusso di lavoro di analisi scientifica dei dati end-to-end sotto forma di esperimento.

Per altre informazioni su Machine Learning Studio, vedere [Cos'è Machine Learning Studio?](what-is-ml-studio.md)

**Cos'è il servizio API di Machine Learning?**

Il servizio API di Machine Learning consente di distribuire modelli predittivi, ad esempio quelli compilati in Machine Learning Studio, sotto forma di servizi Web scalabili e a tolleranza di errore. I servizi Web creati dal servizio API di Machine Learning sono API REST che offrono un'interfaccia per la comunicazione tra le applicazioni esterne e i modelli di analisi predittiva.

Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](consume-web-services.md).

**Dove sono elencati i servizi Web classici? Dove sono elencati i nuovi servizi Web basati su Azure Resource Manager?**

I servizi Web creati con il modello di distribuzione classica e quelli creati con il nuovo modello di distribuzione Azure Resource Manager sono elencati nel portale [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) (Servizi Web di Microsoft Azure Machine Learning).

I servizi Web classici sono elencati anche nella scheda **Servizi Web** di [Machine Learning Studio](http://studio.azureml.net).

## <a name="azure-machine-learning-questions"></a>Domande su Azure Machine Learning
**Che cosa sono i servizi Web di Azure Machine Learning?**

I servizi Web di Machine Learning offrono un'interfaccia tra un'applicazione e un modello di assegnazione dei punteggi del flusso di lavoro di Machine Learning. Un'applicazione esterna può usare Azure Machine Learning per comunicare con un modello di assegnazione dei punteggi del flusso di lavoro di Machine Learning in tempo reale. Una chiamata al servizio Web di Machine Learning restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata a un servizio Web, passare una chiave API creata al momento della distribuzione del servizio Web. Un servizio Web di Machine Learning è basato su REST, un'opzione di architettura diffusa per progetti di programmazione Web.

Azure Machine Learning offre due tipi di servizi Web.

* Servizio di richiesta-risposta (RRS): un servizio a bassa latenza e scalabilità elevata che offre un'interfaccia per i modelli senza stato creati e distribuiti con Machine Learning Studio.
* Servizio Esecuzione batch (BES): un servizio asincrono per l'assegnazione di punteggi a batch di record di dati.

È possibile utilizzare l'API REST e accedere al servizio Web in diversi modi. È ad esempio possibile scrivere un'applicazione in C#, R o Python usando il codice di esempio generato automaticamente quando è stato distribuito il servizio Web.

Il codice di esempio è disponibile nelle posizioni seguenti:
- Nella pagina di utilizzo del servizio Web nel portale dei servizi Web di Microsoft Azure Machine Learning
- Nella pagina della Guida dell'API nel dashboard del servizio Web in Machine Learning Studio

È anche possibile usare la cartella di lavoro di Microsoft Excel di esempio creata automaticamente e disponibile nel dashboard del servizio Web in Machine Learning Studio.

**Quali sono i principali aggiornamenti di Azure Machine Learning?**

Per informazioni sugli aggiornamenti più recenti, vedere [Novità in Azure Machine Learning](../../active-directory/fundamentals/whats-new.md).

## <a name="import-and-export-data-for-machine-learning"></a>Importare ed esportare dati per Machine Learning
**Quali origini dati sono supportate da Machine Learning?**

Per scaricare dati in un esperimento di Machine Learning Studio sono disponibili tre modi:

- Caricare un file locale come set di dati
- Usare un modulo per importare dati da servizi dati cloud
- Importare un set di dati salvato da un altro esperimento

Per altre informazioni sui formati di file supportati, vedere [Importare dati di training in Azure Machine Learning Studio](import-data.md).

### <a id="ModuleLimit"></a>Qual è la grandezza massima dei set di dati per i moduli?
I moduli in Machine Learning Studio supportano set di dati fino a 10 GB di dati numerici ad alta densità per i casi di utilizzo comuni. Se un modulo richiede più input, il valore di 10 GB rappresenta il totale delle dimensioni di tutti gli input. È anche possibile campionare set di dati di dimensioni maggiori con query di Hive o del database SQL di Azure oppure usare la pre-elaborazione di Learning by Counts (Apprendimento in base a conteggi) prima dell'inserimento.  

I tipi di dati seguenti possono espandersi in set di dati di dimensioni maggiori durante la normalizzazione della funzionalità e sono limitati a meno di 10 GB:

* Sparse
* Categorical
* Stringhe
* Dati binari

I moduli seguenti sono limitati a set di dati inferiori a 10 GB:

* Moduli di raccomandazione
* Modulo SMOTE (Synthetic Minority Oversampling Technique)
* Moduli di scripting: R, Python, SQL
* Moduli in cui la dimensione dei dati di output può essere maggiore della dimensione dei dati di input, come Join (Unione) o Feature Hashing (Hash delle funzionalità)
* Convalida incrociata, ottimizzazione degli iperparametri del modello, regressione ordinale e multiclasse uno-tutti, quando il numero di iterazioni è molto elevato

### <a id="UploadLimit"></a>Quali sono i limiti per il caricamento di dati?
Per i set di dati di dimensioni maggiori di alcuni GB, caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare Azure HDInsight, anziché eseguire il caricamento diretto da un file locale.

**È possibile leggere i dati da Amazon S3?**

Se la quantità di dati è limitata e si vuole esporli con un URL HTTP, è possibile usare il modulo [Import Data][import-data] (Importa dati). Per quantità di dati maggiori, trasferirli prima di tutto in Archiviazione di Azure e quindi usare il modulo [Import Data][import-data] (Importa dati) per passarli all'esperimento.
<!--

<SEE CLOUD DS PROCESS>
-->

**Esiste una capacità di input dell'immagine predefinita?**

Per informazioni sulla capacità di input dell'immagine, vedere [Import Images][image-reader] (Importare immagini).

## <a name="modules"></a>Moduli
**L'algoritmo, l'origine dati, il formato dei dati o l'operazione di trasformazione dei dati che si sta cercando non è presente in Azure Machine Learning Studio. Quali sono le opzioni disponibili?**

È possibile accedere al [forum dei commenti e suggerimenti degli utenti](https://go.microsoft.com/fwlink/?LinkId=404231) per visualizzare le richieste di funzionalità sotto esame. Se una funzionalità cercata è già stata richiesta, aggiungere il proprio voto alla richiesta. Se la funzionalità cercata non esiste, creare una nuova richiesta. In questo forum è anche possibile visualizzare lo stato della richiesta. Questo elenco viene controllato regolarmente e lo stato di disponibilità delle funzionalità viene aggiornato di frequente. È anche possibile usare il supporto predefinito per R e Python per creare trasformazioni personalizzate in base alle proprie esigenze.

**È possibile trasferire un proprio codice esistente in Machine Learning Studio?**

Sì, è possibile trasferire un proprio codice R o Python esistente in Machine Learning Studio, eseguirlo nello stesso esperimento con gli strumenti di apprendimento di Azure Machine Learning, quindi distribuire la soluzione come servizio Web tramite Azure Machine Learning. Per altre informazioni, vedere [Estendere l'esperimento con R](extend-your-experiment-with-r.md) ed [Eseguire gli script di apprendimento automatico di Python in Azure Machine Learning Studio](execute-python-scripts.md).

**È possibile usare un codice tipo [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) per definire un modello?**

No. Il linguaggio PMML (Predictive Model Markup Language) non è supportato. Per definire un modulo è possibile usare codice R e Python personalizzato.

**Quanti moduli è possibile eseguire in parallelo nell'esperimento?**  

È possibile eseguire fino a quattro moduli in parallelo in un esperimento.

## <a name="data-processing"></a>Elaborazione dei dati
**Esiste la possibilità di visualizzare i dati (oltre alle visualizzazioni R) in modo interattivo all'interno dell'esperimento?**

Fare clic sull'output di un modulo per visualizzare i dati e ottenere le statistiche.

**Quando si visualizza l'anteprima dei risultati o dei dati in un browser, il numero di righe e colonne è limitato. Perché?**

Dato che al browser potrebbero essere inviate grandi quantità di dati, la dimensione dei dati viene limitata per evitare il rallentamento di Machine Learning Studio. Per visualizzare tutti i dati/risultati, è preferibile scaricare i dati e usare Excel o un altro strumento.

## <a name="algorithms"></a>Algoritmi
**Quali algoritmi esistenti sono supportati in Machine Learning Studio?**

Machine Learning Studio offre algoritmi all'avanguardia, ad esempio gli alberi delle decisioni con boosting scalabili, i sistemi di raccomandazione bayesiani, le reti neurali basate su Machine Deep Learning e le giungle delle decisioni sviluppati da Microsoft Research. Sono inclusi anche pacchetti open source scalabili di apprendimento automatico come Vowpal Wabbit. Machine Learning Studio supporta gli algoritmi di Machine Learning per la classificazione multiclasse e binaria, la regressione e il clustering. Vedere l'elenco completo dei [moduli di Machine Learning][machine-learning-modules].

**L'algoritmo di Machine Learning più appropriato per i dati viene suggerito automaticamente?**

No. In Machine Learning Studio, tuttavia, esistono diversi modi per confrontare i risultati di ogni algoritmo in modo da determinare quello più adatto per la risoluzione del problema.

**Sono disponibili delle linee guida su come scegliere un algoritmo rispetto a tutti gli algoritmi forniti?**

Vedere [Come scegliere un algoritmo](algorithm-choice.md).

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

## <a name="r-module"></a>Modulo R
**Quali pacchetti R sono disponibili in Machine Learning Studio?**

Machine Learning Studio attualmente supporta oltre 400 pacchetti CRAN-R. Vedere l'[elenco aggiornato](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) di tutti i pacchetti inclusi. Vedere anche [Estendere l'esperimento con R](extend-your-experiment-with-r.md) per informazioni su come recuperare personalmente tale elenco. Se il pacchetto desiderato non compare nell'elenco, specificare il nome del pacchetto nel [forum dei commenti e suggerimenti degli utenti](https://go.microsoft.com/fwlink/?LinkId=404231).

**È possibile compilare un modulo R personalizzato?**

Sì. Per altre informazioni, vedere [Creare moduli R personalizzati in Azure Machine Learning](custom-r-modules.md).

**Esiste un ambiente REPL per R?**

No, non esistono ambienti REPL (Read-Eval-Print-Loop) per R in Studio.

## <a name="python-module"></a>Modulo Python
**È possibile compilare un modulo Python personalizzato?**

Anche se non attualmente, è possibile usare uno o più moduli [Execute Python Script][python] (Esegui script Python) per ottenere lo stesso risultato.

**Esiste un ambiente REPL per Python?**

È possibile usare Jupyter Notebook in Machine Learning Studio. Per altre informazioni, vedere [Introducing Jupyter Notebooks in Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)(Introduzione di Jupyter Notebook in Azure Machine Learning Studio).

## <a name="web-service"></a>Servizio Web

**Come è possibile ripetere il training dei modelli di Azure Machine Learning a livello di codice?**

Usare le API per la ripetizione del training. Per altre informazioni, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](retrain-models-programmatically.md). È disponibile codice di esempio anche nella [demo sulla ripetizione del training di Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**È possibile distribuire il modello in locale o in un'applicazione senza una connessione Internet?**

 No.

**Esiste una latenza di base prevista per tutti i servizi Web?**

Vedere l'articolo relativo ai [limiti delle sottoscrizioni di Azure](../../azure-subscription-service-limits.md).

**Quando si esegue il modello predittivo come servizio di esecuzione batch invece che come servizio richiesta-risposta?**

Il servizio richiesta-risposta (RRS) è un servizio Web a bassa latenza e a scalabilità elevata usato per fornire un'interfaccia ai modelli senza stato creati e distribuiti dall'ambiente dell'esperimento. Il servizio esecuzione batch (BES) è un servizio per l'assegnazione asincrona di punteggi a un batch di record di dati. L'input per BES è simile all'input di dati usato da RRS. La differenza principale è che BES legge un blocco di record da diverse origini, ad esempio un archivio BLOB di Azure, un archivio tabelle di Azure, un database SQL di Azure, HDInsight (query Hive) e origini HTTP. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](consume-web-services.md).

**Come si aggiorna il modello per il servizio Web distribuito?**

Per aggiornare un modello predittivo per un servizio già distribuito, modificare e rieseguire l'esperimento usato per creare e salvare il modello con training. Quando è disponibile una nuova versione del modello con training, Machine Learning Studio chiede all'utente se vuole aggiornare il servizio Web. Per informazioni dettagliate su come aggiornare un servizio Web distribuito, vedere [Distribuire un servizio Web di Azure Machine Learning](publish-a-machine-learning-web-service.md).

Inoltre, è possibile utilizzare le API Retraining.
Per altre informazioni, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](retrain-models-programmatically.md). È disponibile codice di esempio anche nella [demo sulla ripetizione del training di Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Come monitorare il servizio Web distribuito in produzione?**

Dopo aver distribuito un modello predittivo, è possibile monitorarlo dal portale dei servizi Web di Azure Machine Learning. Ogni servizio distribuito ha un proprio dashboard, in cui è possibile visualizzare le informazioni di monitoraggio per il servizio. Per altre informazioni sulla gestione dei servizi Web distribuiti, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md) e [Gestire un'area di lavoro di Azure Machine Learning](manage-workspace.md).

**Esiste una posizione in cui è possibile visualizzare l'output di RRS/BES?**

Per RRS, la risposta del servizio web si trova in genere dove si visualizza il risultato. Può anche essere scritta nell'archivio BLOB di Azure. Per BES, l'output viene scritto in un BLOB per impostazione predefinita. È anche possibile scrivere l'output in un database o una tabella usando il modulo [Export Data][export-data] (Esporta dati).

**È possibile creare servizi Web solo da modelli creati in Machine Learning Studio?**

No. È anche possibile creare servizi Web direttamente con Jupyter Notebook e RStudio.

**Dove è possibile trovare informazioni sui codici di errore?**

Per un elenco dei codici di errore e delle relative descrizioni, vedere [Codici di errore dei moduli di Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) .

**Che cos'è la scalabilità del servizio Web?**

Attualmente, il provisioning dell'endpoint predefinito viene effettuato con 20 richieste RRS simultanee per ogni endpoint. È possibile ridimensionare le richieste simultanee a 200 per endpoint e ridimensionare ogni servizio Web a 10.000 endpoint per servizio Web come descritto in [Ridimensionamento di un servizio Web](scaling-webservice.md). Per BES, ogni endpoint può elaborare 40 richieste alla volta e le richieste aggiuntive oltre 40 vengono messe in coda. Queste richieste in coda vengono eseguite automaticamente allo svuotamento della coda.

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
* Moduli di scripting: R, Python, SQL
* Moduli in cui la dimensione dei dati di output può essere maggiore della dimensione dei dati di input, come Join (Unione) o Feature Hashing (Hash delle funzionalità)
* Convalida incrociata, ottimizzazione degli iperparametri del modello, regressione ordinale e multiclasse uno-tutti, quando il numero di iterazioni è molto elevato

Per i set di dati di dimensioni maggiori di alcuni GB, caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare HDInsight, anziché eseguire il caricamento diretto da un file locale.

**Esistono limitazioni per la dimensione dei vettori?**

Le righe e le colonne sono limitate singolarmente in base alla limitazione .NET di intervallo massimo: 2.147.483.647.

**È possibile modificare le dimensioni della macchina virtuale che esegue il servizio Web?**

 No.  

## <a name="security-and-availability"></a>Sicurezza e disponibilità
**Chi può accedere per impostazione predefinita all'endpoint HTTP per il servizio Web? Come limitare l'accesso all'endpoint?**

Dopo avere distribuito un servizio Web, viene creato un endpoint predefinito per tale servizio. L'endpoint predefinito può essere chiamato usando la chiave API. È possibile aggiungere altri endpoint con le rispettive chiavi dal portale dei servizi Web o a livello di codice usando le API di gestione dei servizi Web. Le chiavi di accesso sono necessarie per effettuare le chiamate al servizio Web. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](consume-web-services.md).

**Cosa succede se non viene trovato l'account di archiviazione di Azure?**

Machine Learning Studio si basa su un account di archiviazione di Azure specificato dall'utente per salvare i dati intermedi quando esegue il flusso di lavoro. Questo account di archiviazione viene fornito a Machine Learning Studio quando si crea un'area di lavoro. Dopo la creazione dell'area di lavoro, se l'account di archiviazione viene eliminato e non è più disponibile, l'area di lavoro non funzionerà più e tutti gli esperimenti al suo interno avranno esito negativo.

Se si elimina accidentalmente l'account di archiviazione, ricrearlo con lo stesso nome e nella stessa area dell'account di archiviazione eliminato. Risincronizzare quindi la chiave di accesso.

**Cosa succede se la chiave di accesso dell'account di archiviazione non è sincronizzata?**

Machine Learning Studio si basa su un account di archiviazione di Azure specificato dall'utente per archiviare i dati intermedi quando esegue il flusso di lavoro. Questo account di archiviazione viene fornito a Machine Learning Studio quando si crea un'area di lavoro e le chiavi di accesso sono associate all'area di lavoro. Se le chiavi di accesso vengono modificate dopo la creazione dell'area di lavoro, l'area di lavoro non riuscirà più ad accedere all'account di archiviazione. L'area di lavoro non funzionerà più e tutti gli esperimenti al suo interno avranno esito negativo.

Se sono state modificate le chiavi di accesso dell'account di archiviazione, sincronizzarle di nuovo nell'area di lavoro usando il portale di Azure.  


## <a name="billing-questions"></a>Domande sulla fatturazione

Per informazioni sulla fatturazione e sui prezzi, vedere [Prezzi di Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).


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
