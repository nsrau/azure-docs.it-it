<properties
	pageTitle="Domande frequenti su Azure Machine Learning | Microsoft Azure"
	description="Introduzione ad Azure Machine Learning; domande frequenti su fatturazione, funzionalità e limitazioni di un servizio cloud per la modellazione predittiva semplificata."
	keywords="introduzione al machine learning, modellazione predittiva, cos’è il machine learning"
	services="machine-learning"
	documentationCenter=""
	authors="pablissima"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/25/2016"
	ms.author="paulettm"/>

# Domande frequenti su Azure Machine Learning: Fatturazione, funzionalità, limitazioni e supporto

Questa sezione contiene le domande e le risposte su Azure Machine Learning, un servizio cloud per la modellazione predittiva e l'operatività delle soluzioni con i servizi Web. In questa sezione sono contenute le domande sull'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto.

## Domande generali

**Cos'è Azure Machine Learning?**

Azure Machine Learning è un servizio completamente gestito che è possibile usare per creare, testare, usare e gestire le soluzioni per l'analisi predittiva nel cloud. Con un semplice browser è possibile accedere, caricare i dati e avviare immediatamente le sperimentazioni di Machine Learning. La modellazione predittiva a trascinamento, un'ampia gamma di moduli e una libreria di modelli di avvio rendono le attività comuni di Machine Learning facili e veloci da eseguire. Per altre informazioni, vedere la [panoramica del servizio di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Per un'introduzione al Machine Learning che tratta i concetti e la terminologia di base, vedere [Introduzione ad Azure Machine Learning](machine-learning-what-is-machine-learning.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Cos'è Machine Learning Studio?**

Machine Learning Studio è un ambiente workbench accessibile tramite un Web browser. Machine Learning Studio ospita diversi moduli con un'interfaccia di composizione visiva che consente di compilare un flusso di lavoro di data science end-to-end sotto forma di esperimento.

Per altre informazioni su Machine Learning Studio, vedere [Cos'è Machine Learning Studio](machine-learning-what-is-ml-studio.md)

**Cos'è il servizio API di Machine Learning?**

Il servizio API di Machine Learning consente di distribuire i modelli predittivi compilati in Machine Learning Studio sotto forma di servizi Web scalabili e a tolleranza di errore. I servizi Web creati dal servizio API di Machine Learning sono API REST che forniscono un'interfaccia per la comunicazione tra le applicazioni eterne e i modelli di analisi predittiva.

Vedere [Connessione a un servizio Web di Azure Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md) per altre informazioni.


## Domande sulla fatturazione

**Come funziona la fatturazione di Machine Learning?**

Per informazioni sulla fatturazione e i prezzi, vedere [Machine Learning Prezzi](https://azure.microsoft.com/pricing/details/machine-learning/).

**Machine Learning offre una versione di valutazione gratuita?**

 Quando ci si iscrive a una versione di valutazione gratuita di Azure, è possibile provare tutti i servizi Azure per un mese. Per altre informazioni sulla versione di valutazione gratuita di Azure, visitare [Domande frequenti sulla versione di valutazione gratuita di Azure](/pricing/free-trial-faq/).

## Domande su Machine Learning Studio

### Creazione di un esperimento
**Esiste il controllo della versione o l'integrazione Git per i grafici dell'esperimento?**

No. Tuttavia, quando si esegue un esperimento, la versione del grafico viene mantenuta e non può essere modificata dagli altri utenti.

### Importazione ed esportazione dei dati per Machine Learning
**Quali origini dati sono supportate da Machine Learning?**

I dati possono essere caricati in Machine Learning Studio in uno dei due modi seguenti: caricando i file locali come set di dati oppure usando un modulo Reader per importare i dati. I file locali possono essere caricati aggiungendo nuovi set di dati in Machine Learning Studio. Vedere [Importare dati di training in Azure Machine Learning Studio](machine-learning-data-science-import-data.md) per altre informazioni sui formati di file supportati.


#### <a id="ModuleLimit"></a>Qual è la grandezza massima dei set di dati per i moduli?

I moduli in Machine Learning Studio supportano set di dati fino a 10 GB di dati numerici ad alta densità per i casi di utilizzo comuni. Se un modulo richiede più input, 10 GB rappresenta il totale di tutte le dimensioni degli input. È anche possibile campionare set di dati di dimensioni maggiori tramite le query di Hive o del database SQL di Azure oppure tramite la pre-elaborazione Learning by Counts prima dell'inserimento.

I seguenti tipi di dati possono espandersi in set di dati di dimensioni maggiori durante la normalizzazione della funzionalità e sono limitati a meno di 10 GB:

- Sparse
- Categorical
- Stringhe
- Dati binari

I seguenti moduli sono limitati a set di dati inferiori a 10 GB:

- Moduli di raccomandazione
- Modulo SMOTE
- Moduli di script: R, Python, SQL
- Moduli in cui la dimensione dei dati di output può essere maggiore della dimensione dei dati in input, ad esempio Join o Feature Hashing.
- Convalida incrociata, parametri di organizzazione, regressione ordinale e multiclasse uno-tutti, quando il numero di iterazioni è molto elevato.

Per i set di dati con dimensioni maggiori di alcuni GB, è necessario caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare HDInsight, anziché caricarli direttamente dal file locale.


####<a id="UploadLimit"></a>Quali sono i limiti per il caricamento di dati?
Per i set di dati con dimensioni maggiori di un paio di GB, caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare HDInsight, anziché caricarli direttamente dal file locale.

**È possibile leggere i dati da Amazon S3?**

Se la quantità di dati è limitata e si vuole esporli tramite un URL HTTP, è possibile usare il modulo [Reader][reader]. Per quantità di dati maggiori, trasferirli prima in Archiviazione di Azure e quindi usare il modulo [Reader][reader] per passarli all'esperimento.
<!--
<SEE CLOUD DS PROCESS>
-->

**Esiste una capacità di input dell'immagine predefinita?**

Per informazioni sulla capacità di input dell'immagine, vedere [Image Reader][image-reader].

### Moduli

**L'algoritmo, l'origine dati, il formato dei dati o l'operazione di trasformazione dei dati cercati non sono presenti in Azure ML Studio. Quali sono le opzioni disponibili?**

È possibile visitare il [forum con i commenti e suggerimenti degli utenti](http://go.microsoft.com/fwlink/?LinkId=404231) per visualizzare le richieste di funzionalità a cui stiamo dando seguito. Se la funzionalità cercata è già stata richiesta, aggiungere il proprio voto alla richiesta. Se la funzionalità cercata non esiste, creare una nuova richiesta. Sempre in questo forum, è possibile visualizzare lo stato della richiesta. Questo elenco viene controllato regolarmente e lo stato di disponibilità delle funzionalità viene aggiornato di frequente. Oltre al supporto predefinito per R e Python, è possibile creare trasformazioni personalizzate in base alle proprie esigenze.


**È possibile trasferire un proprio codice esistente in ML Studio?**

Sì, è possibile trasferire un proprio codice R esistente in ML Studio ed eseguirlo nello stesso esperimento con gli strumenti di apprendimento forniti da Azure Machine Learning, quindi distribuirlo come servizio Web tramite Azure Machine Learning. Vedere [Estendere l'esperimento con R](machine-learning-extend-your-experiment-with-r.md).

**È possibile usare un codice tipo [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) per definire un modello?**

No, non è supportato. Tuttavia, è possibile usare il codice personalizzato R e Python per definire un modulo.


### Elaborazione dei dati

**Esiste la possibilità di visualizzare i dati (oltre alle visualizzazioni R) in modo interattivo all'interno dell'esperimento?**

Facendo clic sull'output di un modulo è possibile visualizzare i dati e ottenere le statistiche.

**Quando si esegue l'anteprima dei risultati o dei dati nel browser, il numero di righe e colonne è limitato. Perché?**

Poiché la quantità di dati trasmessi al browser può essere elevata, la dimensione dei dati viene limitata per evitare il rallentamento di ML Studio. È preferibile scaricare i dati/risultati e usare Excel o un altro strumento per visualizzarli per intero.

### Algoritmi

**Quali algoritmi esistenti sono supportati in Machine Learning Studio?**

Machine Learning Studio fornisce algoritmi all'avanguardia, ad esempio gli alberi delle decisioni con boosting scalabili, i sistemi di raccomandazione bayesiani, le reti neurali basate su machine deep learning e le giungle delle decisioni sviluppati da Microsoft Research. Sono inclusi anche i pacchetti open source di Machine Learning scalabili come Vowpal Wabbit. Machine Learning Studio supporta gli algoritmi di Machine Learning per la classificazione multiclasse e binaria, la regressione e il clustering. Vedere l'elenco completo dei [moduli di Machine Learning][machine-learning-modules].

**L'algoritmo di Machine Learning più appropriato per i dati viene suggerito automaticamente?**

No. Tuttavia, esistono diversi modi in Machine Learning Studio per confrontare i risultati di ogni algoritmo per determinare quello più adatto per la risoluzione del problema.

**Sono disponibili delle linee guida su come scegliere un algoritmo rispetto a tutti gli algoritmi forniti?** Vedere [Come scegliere un algoritmo in Azure Machine Learning](machine-learning-algorithm-choice.md).

**Gli algoritmi forniti sono scritti in R o Python?**

No. Questi algoritmi sono scritti principalmente in linguaggi compilati per fornire prestazioni più elevate.

**Sono disponibili informazioni dettagliate sugli algoritmi forniti?**

La documentazione fornisce alcune informazioni sugli algoritmi. Inoltre, sono descritti i parametri forniti per l'ottimizzazione dell'algoritmo che è possibile usare.

**È previsto il supporto per l'apprendimento online?**

No. Attualmente è supportata solo la ripetizione del training a livello di codice.

**È possibile visualizzare i livelli di un modello Neural Net usando il modulo predefinito?**

No.

**È possibile creare dei moduli personalizzati in C# o in altri linguaggi?**

Attualmente, i nuovi moduli personalizzati possono essere creati solo in R.

### Modulo R

**Quali pacchetti R sono disponibili in Machine Learning Studio?**

Attualmente, Machine Learning Studio supporta oltre 400 pacchetti CRAN R e l'elenco è in costante aumento. Vedere [Estendere l'esperimento con R](machine-learning-extend-your-experiment-with-r.md) per informazioni su come ottenere un elenco di pacchetti R supportati. Se il pacchetto desiderato non compare nell'elenco, specificare il nome del pacchetto nel [forum dei commenti e suggerimenti degli utenti](http://go.microsoft.com/fwlink/?LinkId=404231).

**È possibile compilare un modulo R personalizzato?**

Sì. Vedere [Creazione di moduli R personalizzati in Azure Machine Learning](machine-learning-custom-r-modules.md) per altre informazioni.

**Esiste un ambiente REPL per R?**

No, non esistono ambienti REPL per R in Studio.

### Modulo Python

**È possibile compilare un modulo Python personalizzato?**

Non al momento, ma con il modulo Python standard o un set di moduli è possibile ottenere lo stesso risultato.

**Esiste un ambiente REPL per Python?**

È possibile utilizzare i notebook Jupyter in Studio Machine Learning. Per ulteriori informazioni, vedere [Introdurre i notebook Jupyter in Azure ML Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)

## Servizio Web

###Ripetizione del training dei modelli a livello di codice

**Come si ripete il training dei modelli AzureML a livello di codice?** Usare le API per la ripetizione del training. Il codice di esempio è disponibile [qui](https://azuremlretrain.codeplex.com/).

### Creazione

**È possibile distribuire il modello in locale o in un'applicazione senza una connessione Internet?** No.


**Esiste una latenza di base prevista per tutti i servizi Web?**

Vedere [Limiti relativi alle sottoscrizioni](../azure-subscription-service-limits.md)

### Uso

**Quando si esegue il modello predittivo come servizio di esecuzione batch invece che come servizio richiesta-risposta?**

Il servizio richiesta-risposta (RRS) è un servizio Web a bassa latenza e a scalabilità elevata usato per fornire un'interfaccia ai modelli senza stato creati e distribuiti dall'ambiente dell'esperimento. Il servizio di esecuzione batch (BES) è un servizio per il punteggio asincrono di un batch di record di dati. L'input per BES è simile all'input di dati usato in RRS. La differenza principale è che BES legge un blocco di record da diverse origini, ad esempio il servizio BLOB e il servizio tabelle in Azure, il database SQL di Azure, HDInsight (query hive) e origini HTTP. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato](machine-learning-consume-web-services.md).

**Come si aggiorna il modello per il servizio Web distribuito?**

L'aggiornamento di un modello predittivo per un servizio già distribuito è semplice. È sufficiente modificare l'esperimento usato per creare e salvare il modello di apprendimento ed eseguirlo di nuovo. Quando è disponibile la nuova versione del modello di apprendimento, ML Studio chiederà all'utente se vuole aggiornare il servizio Web nell'ambiente di gestione temporanea. Dopo aver applicato l'aggiornamento al servizio Web di gestione temporanea, lo stesso aggiornamento diventa disponibile anche per il servizio Web di produzione. Vedere [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md) per dettagli su come aggiornare un servizio Web distribuito.

Inoltre, è possibile utilizzare le API Retraining. Il codice di esempio è disponibile [qui](https://azuremlretrain.codeplex.com/).

**Come si monitora il servizio Web distribuito in produzione?**

Una volta inserito in produzione un modello predittivo, è possibile monitorarlo dal portale di Azure classico. Ogni servizio distribuito ha un proprio dashboard, in cui è possibile visualizzare le informazioni di monitoraggio per il servizio.

**Esiste una posizione in cui è possibile visualizzare l'output di RRS/BES?**

Per RRS, la risposta del servizio web si trova in genere dove si visualizza il risultato. È inoltre possibile scrivere un BLOB. Per BES, l'output viene scritto in un BLOB per impostazione predefinita. È inoltre possibile scrivere l'output in un database o in una tabella utilizzando il modulo di scrittura.

 ****È possibile creare servizi web solo da modelli creati in Studio? No. È inoltre possibile creare servizi web direttamente dai notebook Jupyter e RStudio.

 ****Dove è possibile trovare informazioni sui codici di errore? I codici di errore vengono descritti [qui](https://msdn.microsoft.com/library/azure/dn905910.aspx).

## Scalabilità

**Che cos'è la scalabilità del servizio Web?**

Attualmente, il provisioning dell'endpoint predefinito viene eseguito con 20 richieste RRS simultanee per ogni endpoint. È possibile scalare le richieste simultanee a 200 richieste per ogni endpoint ed è possibile scalare ogni servizio Web a 10.000 endpoint per servizio Web come descritto nell’articolo [Scalabilità degli endpoint dell'API](machine-learning-scaling-endpoints.md). Per BES, ogni endpoint consente l'elaborazione di 40 richieste alla volta e richieste aggiuntive oltre 40 richieste vengono messe in coda. Queste richieste in coda verranno eseguite automaticamente allo svuotamento della coda.


**I processi R sono distribuiti in più nodi?**

No.


**Su quanti dati è possibile eseguire il training?**

I moduli in Machine Learning Studio supportano set di dati fino a 10 GB di dati numerici ad alta densità per i casi di utilizzo comuni. Se un modulo richiede più input, 10 GB rappresenta il totale di tutte le dimensioni degli input. È anche possibile campionare set di dati di dimensioni maggiori tramite le query di Hive o del database SQL di Azure oppure tramite la pre-elaborazione Learning by Counts prima dell'inserimento.

I seguenti tipi di dati possono espandersi in set di dati di dimensioni maggiori durante la normalizzazione della funzionalità e sono limitati a meno di 10 GB:

- Sparse
- Categorical
- Stringhe
- Dati binari

I seguenti moduli sono limitati a set di dati inferiori a 10 GB:

- Moduli di raccomandazione
- Modulo SMOTE
- Moduli di script: R, Python, SQL
- Moduli in cui la dimensione dei dati di output può essere maggiore della dimensione dei dati in input, ad esempio Join o Feature Hashing.
- Convalida incrociata, parametri di organizzazione, regressione ordinale e multiclasse uno-tutti, quando il numero di iterazioni è molto elevato.

Per i set di dati con dimensioni maggiori di alcuni GB, è necessario caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare HDInsight, anziché caricarli direttamente dal file locale.


**Esistono limitazioni per la dimensione dei vettori?**

Le righe e le colonne sono limitate singolarmente in base alla limitazione .NET di intervallo massimo: 2.147.483.647.

**La dimensione della macchina virtuale su cui è in esecuzione il processo può essere modificata?**

No.

## Sicurezza e disponibilità

**Chi ha accesso all'endpoint HTTP per il servizio Web distribuito in produzione per impostazione predefinita? Come è possibile limitare l'accesso all'endpoint?**

Dopo aver distribuito un servizio web, è necessario creare un endpoint predefinito per tale servizio. Tale endpoint predefinito viene distribuito nell'ambiente di produzione e può essere chiamato utilizzando la relativa chiave API. È possibile aggiungere altri endpoint con le rispettive chiavi dal portale di Azure classico o a livello di programmazione utilizzando le API di gestione del servizio Web. Le chiavi di accesso sono necessarie per effettuare le chiamate al servizio Web sia nell'ambiente di produzione che nell'ambiente di gestione temporanea. Per altre informazioni, vedere [Connessione a un servizio Web di Azure Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).


**Cosa succede se non viene trovato l'account di archiviazione?**

Machine Learning Studio si basa su un account di archiviazione di Azure fornito dall'utente per salvare i dati intermedi quando si esegue il flusso di lavoro. Questo account di archiviazione viene fornito a Machine Learning Studio al momento della creazione di uno spazio di lavoro. Dopo aver creato lo spazio di lavoro, se l'account di archiviazione viene eliminato e non è più disponibile, lo spazio di lavoro non funzionerà più e tutti gli esperimenti nello spazio di lavoro non riusciranno.

Se si elimina accidentalmente l'account di archiviazione, l'unico modo per ripristinarlo consiste nel ricreare l'account di archiviazione con lo stesso nome e nella stessa area dell'account eliminato. Successivamente, risincronizzare la chiave di accesso.


**Cosa succede se la chiave di accesso dell'account di archiviazione non è sincronizzata?** Machine Learning Studio si basa su un account di archiviazione di Azure fornito dall'utente per salvare i dati intermedi quando si esegue il flusso di lavoro. Questo account di archiviazione viene fornito a Machine Learning Studio al momento della creazione di uno spazio di lavoro e le chiavi di accesso sono associate allo spazio di lavoro. Dopo aver creato lo spazio di lavoro, se le chiavi di accesso vengono modificate, lo spazio di lavoro non riesce più ad accedere all'account di archiviazione, quindi non funzionerà più e tutti gli esperimenti nello spazio di lavoro non riusciranno.

Se sono state modificate le chiavi di accesso dell'account di archiviazione, assicurarsi di sincronizzare di nuovo le chiavi di accesso nell'impostazione dello spazio di lavoro nel portale di Azure classico


## Azure Marketplace

Vedere [Domande frequenti sulla pubblicazione e sull'utilizzo delle app di Machine Learning in Azure Marketplace](machine-learning-marketplace-faq.md)

## Supporto e training

**Dove si trovano i training per Azure ML?**

Il [centro di documentazione di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) ospita le esercitazioni video e le guide per le procedure. Queste guide dettagliate forniscono un'introduzione ai servizi e indicazioni sul ciclo di vita data-science per l'importazione dei dati, la pulizia dei dati, la compilazione di modelli predittivi e la loro distribuzione in produzione con Azure ML.

Microsoft aggiungerà regolarmente nuovo materiale in Machine Learning Center. Per ottenere altro materiale di apprendimento, inviare una richiesta su Machine Learning Center nel [forum dei commenti degli utenti](https://windowsazure.uservoice.com/forums/257792-machine-learning).

I training sono disponibili anche in [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning)

**Come si ottiene il supporto per Azure Machine Learning?**

Per ottenere supporto tecnico per Azure Machine Learning, andare in [Opzioni di supporto per Azure](/support/options/) e selezionare **Machine Learning**.

È inoltre disponibile un forum della community di Azure Machine Learning su MSDN, in cui è possibile porre domande relative ad Azure ML. Il forum è monitorato dal team di Azure ML. Visitare il [forum di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0309_2016-->