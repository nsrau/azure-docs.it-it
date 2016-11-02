<properties
    pageTitle="Domande frequenti su Azure Machine Learning | Microsoft Azure"
    description="Introduzione ad Azure Machine Learning; domande frequenti su fatturazione, funzionalità e limitazioni di un servizio cloud per la modellazione predittiva semplificata."
    keywords="introduzione al machine learning, modellazione predittiva, cos’è il machine learning"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="garye"/>


# <a name="azure-machine-learning-frequently-asked-questions-(faq):-billing,-capabilities,-limitations,-and-support"></a>Domande frequenti su Azure Machine Learning: Fatturazione, funzionalità, limitazioni e supporto

Questa sezione contiene le domande e le risposte su Azure Machine Learning, un servizio cloud per lo sviluppo di modelli predittivi e l'operatività delle soluzioni con i servizi Web. In questa sezione sono contenute le domande sull'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto.

## <a name="general-questions"></a>Domande generali

**Cos'è Azure Machine Learning?**

Azure Machine Learning è un servizio completamente gestito che è possibile usare per creare, testare, usare e gestire le soluzioni per l'analisi predittiva nel cloud. Con un semplice browser è possibile accedere, caricare i dati e avviare immediatamente le sperimentazioni di Machine Learning. La modellazione predittiva a trascinamento, un'ampia gamma di moduli e una libreria di modelli di avvio rendono le attività comuni di Machine Learning facili e veloci da eseguire.  Per altre informazioni, vedere la [panoramica del servizio di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Per un'introduzione al Machine Learning che tratta i concetti e la terminologia di base, vedere [Introduzione ad Azure Machine Learning](machine-learning-what-is-machine-learning.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Cos'è Machine Learning Studio?**

Machine Learning Studio è un ambiente workbench accessibile tramite un Web browser. Machine Learning Studio ospita diversi moduli con un'interfaccia di composizione visiva che consente di compilare un flusso di lavoro di analisi scientifica dei dati end-to-end sotto forma di esperimento.

Per altre informazioni su Machine Learning Studio, vedere [Cos'è Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

**Cos'è il servizio API di Machine Learning?**

Il servizio API di Machine Learning consente di distribuire i modelli predittivi, ad esempio quelli compilati in Machine Learning Studio, sotto forma di servizi Web scalabili e a tolleranza di errore. I servizi Web creati dal servizio API di Machine Learning sono API REST che forniscono un'interfaccia per la comunicazione tra le applicazioni eterne e i modelli di analisi predittiva.

Per altre informazioni, vedere [Connessione a un servizio Web di Azure Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).

**Dove sono elencati i servizi Web classici? Dove sono elencati i nuovi servizi Web basati su Azure Resource Manager?**

I servizi Web classici sono elencati nella scheda Servizi Web di [Machine Learning Studio](http://studio.azureml.net) . I nuovi servizi Web basati su Azure Resource Manager sono elencati nel portale [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) (Servizi Web di Microsoft Azure Machine Learning). Non è disponibile un elenco incrociato.

## <a name="microsoft-azure-machine-learning-web-service-questions"></a>Domande sui servizi Web di Microsoft Azure Machine Learning

**Che cosa sono i servizi Web di Azure ML?**

Con il servizio Web di Azure Machine Learning, un'applicazione esterna comunica con un modello di valutazione del flusso di lavoro di Machine Learning in tempo reale. Una chiamata al servizio Web di Machine Learning restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata al servizio Web di Machine Learning, passare una chiave API creata al momento della distribuzione del servizio Web. Il servizio Web di Machine Learning è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Azure Machine Learning dispone di due tipi di servizi:

* Servizio richiesta-risposta (RRS). Un servizio a bassa latenza e scalabilità elevata che offre un'interfaccia per i modelli senza stato creati e distribuiti da Machine Learning Studio.
* Servizio esecuzione batch (BES). Un servizio asincrono per l'assegnazione di punteggi a un batch di record di dati.

È possibile utilizzare l'API REST e accedere al servizio Web in diversi modi. È ad esempio possibile scrivere un'applicazione in C#, R o Python usando il codice di esempio generato automaticamente quando è stato distribuito il servizio Web.

Il codice di esempio è disponibile nella pagina di utilizzo del servizio Web nel portale dei servizi Web di Microsoft Azure Machine Learning e nella pagina della Guida dell'API nel dashboard del servizio Web in Machine Learning Studio.

In alternativa, è possibile usare la cartella di lavoro di Microsoft Excel di esempio creata, disponibile anche nel dashboard del servizio Web in Studio.

**Quali sono i principali aggiornamenti dei nuovi servizi Web di Azure ML?**

Per altre informazioni sui nuovi servizi Web di Azure Machine Learning, vedere la [documentazione correlata](machine-learning-whats-new.md).

## <a name="machine-learning-studio-questions"></a>Domande su Machine Learning Studio

### <a name="creating-an-experiment"></a>Creazione di un esperimento

**Esiste il controllo della versione o l'integrazione Git per i grafici dell'esperimento?**

No, tuttavia Machine Learning Studio mantiene ogni iterazione di un esperimento che non può essere modificato da altri utenti.
Per altre informazioni, vedere [Gestire iterazioni dell'esperimento in Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md).


### <a name="deploying-an-experiment"></a>Distribuzione di un esperimento

**È possibile distribuire come nuovo servizio Web basato su Azure Resource Manager un esperimento predittivo già distribuito come servizio Web classico?**

No, non è possibile distribuire un esperimento già distribuito come servizio Web classico. È invece necessario creare e distribuire un nuovo esperimento predittivo.


### <a name="importing-and-exporting-data-for-machine-learning"></a>Importazione ed esportazione dei dati per Machine Learning

**Quali origini dati sono supportate da Machine Learning?**

È possibile caricare dati in un esperimento di Machine Learning Studio in tre modi: caricando un file locale come set di dati, usando un modulo per importare i dati dai servizi dati cloud o importando un set di dati da un altro esperimento. Per altre informazioni sui formati di file supportati, vedere [Importare dati di training in Azure Machine Learning Studio](machine-learning-data-science-import-data.md).


#### <a name="<a-id="modulelimit"></a>how-large-can-the-data-set-be-for-my-modules?"></a><a id="ModuleLimit"></a>Qual è la grandezza massima dei set di dati per i moduli?

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
- Convalida incrociata, ottimizzazione degli iperparametri del modello, regressione ordinale e multiclasse uno-tutti, quando il numero di iterazioni è molto elevato.

Per i set di dati con dimensioni maggiori di alcuni GB, è necessario caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare HDInsight, anziché caricarli direttamente dal file locale.


####<a name="<a-id="uploadlimit"></a>what-are-the-limits-for-data-upload?"></a><a id="UploadLimit"></a>Quali sono i limiti per il caricamento di dati?
Per i set di dati con dimensioni maggiori di un paio di GB, caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare HDInsight, anziché caricarli direttamente dal file locale.

**È possibile leggere i dati da Amazon S3?**

Se la quantità di dati è limitata e si vuole esporli con un URL HTTP, è possibile usare il modulo [Import Data][import-data] (Importa dati). Per quantità di dati maggiori, trasferire prima di tutto i dati in Archiviazione di Azure e quindi usare il modulo [Import Data][import-data] (Importa dati) per passarli all'esperimento.
<!--
<SEE CLOUD DS PROCESS>
-->

**Esiste una capacità di input dell'immagine predefinita?**

Per informazioni sulla capacità di input dell'immagine, vedere [Import Images][image-reader] (Importare immagini).

### <a name="modules"></a>Moduli

**L'algoritmo, l'origine dati, il formato dei dati o l'operazione di trasformazione dei dati che si sta cercando non è presente in Azure Machine Learning Studio. Quali sono le opzioni disponibili?**

È possibile visitare il [forum dei commenti e suggerimenti degli utenti](http://go.microsoft.com/fwlink/?LinkId=404231) per visualizzare le richieste di funzionalità sotto esame. Se una funzionalità cercata è già stata richiesta, aggiungere il proprio voto alla richiesta. Se la funzionalità cercata non esiste, creare una nuova richiesta. Sempre in questo forum, è possibile visualizzare lo stato della richiesta. Questo elenco viene controllato regolarmente e lo stato di disponibilità delle funzionalità viene aggiornato di frequente. Oltre al supporto predefinito per R e Python, è possibile creare trasformazioni personalizzate in base alle proprie esigenze.


**È possibile trasferire un proprio codice esistente in Machine Learning Studio?**

Sì, è possibile trasferire un proprio codice R o Python esistente in Machine Learning Studio, eseguirlo nello stesso esperimento con gli strumenti di apprendimento di Azure Machine Learning, quindi distribuire la soluzione come servizio Web tramite Azure Machine Learning. Per altre informazioni, vedere [Estendere l'esperimento con R](machine-learning-extend-your-experiment-with-r.md) ed [Eseguire gli script di apprendimento automatico di Python in Azure Machine Learning Studio](machine-learning-execute-python-scripts.md).

**È possibile usare un codice tipo [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) per definire un modello?**

No, non è supportato. Tuttavia, è possibile usare il codice personalizzato R e Python per definire un modulo.

**Quanti moduli è possibile eseguire in parallelo nell'esperimento?**  

È possibile eseguire fino a quattro moduli in parallelo in un esperimento.


### <a name="data-processing"></a>Elaborazione dei dati

**Esiste la possibilità di visualizzare i dati (oltre alle visualizzazioni R) in modo interattivo all'interno dell'esperimento?**

Facendo clic sull'output di un modulo è possibile visualizzare i dati e ottenere le statistiche.

**Quando si esegue l'anteprima dei risultati o dei dati nel browser, il numero di righe e colonne è limitato. Perché?**

Poiché la quantità di dati trasmessi al browser può essere elevata, la dimensione dei dati viene limitata per evitare il rallentamento di Machine Learning Studio. Per visualizzare tutti i dati/risultati, è preferibile scaricare i dati e usare Excel o un altro strumento.

### <a name="algorithms"></a>Algoritmi

**Quali algoritmi esistenti sono supportati in Machine Learning Studio?**

Machine Learning Studio offre algoritmi all'avanguardia, ad esempio gli alberi delle decisioni con boosting scalabili, i sistemi di raccomandazione bayesiani, le reti neurali basate su Machine Deep Learning e le giungle delle decisioni sviluppati da Microsoft Research. Sono inclusi anche i pacchetti open source di Machine Learning scalabili come Vowpal Wabbit. Machine Learning Studio supporta gli algoritmi di Machine Learning per la classificazione multiclasse e binaria, la regressione e il clustering. Vedere l'elenco completo dei [Machine Learning Modules][machine-learning-modules] (Moduli di Machine Learning).

**L'algoritmo di Machine Learning più appropriato per i dati viene suggerito automaticamente?**

No. Tuttavia, esistono diversi modi in Machine Learning Studio per confrontare i risultati di ogni algoritmo per determinare quello più adatto per la risoluzione del problema.

**Sono disponibili delle linee guida su come scegliere un algoritmo rispetto a tutti gli algoritmi forniti?**
Vedere [Come scegliere un algoritmo](machine-learning-algorithm-choice.md).

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

### <a name="r-module"></a>Modulo R

**Quali pacchetti R sono disponibili in Machine Learning Studio?**

Machine Learning Studio attualmente supporta oltre 400 pacchetti CRAN-R. Vedere l'[elenco aggiornato](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) di tutti i pacchetti inclusi. Vedere anche [Estendere l'esperimento con R](machine-learning-extend-your-experiment-with-r.md) per informazioni su come recuperare personalmente tale elenco. Se il pacchetto necessario non compare nell'elenco, specificare il nome del pacchetto nel [forum dei commenti e suggerimenti degli utenti](http://go.microsoft.com/fwlink/?LinkId=404231).

**È possibile compilare un modulo R personalizzato?**

Sì. Per altre informazioni, vedere [Creare moduli R personalizzati in Azure Machine Learning](machine-learning-custom-r-modules.md).

**Esiste un ambiente REPL per R?**

No, non esistono ambienti REPL per R in Studio.

### <a name="python-module"></a>Modulo Python

**È possibile compilare un modulo Python personalizzato?**

Anche se non attualmente, è possibile usare uno o più moduli [Execute Python Script][python] (Esegui script Python) per ottenere lo stesso risultato.

**Esiste un ambiente REPL per Python?**

È possibile usare Jupyter Notebook in Machine Learning Studio. Per altre informazioni, vedere [Introducing Jupyter Notebooks in Azure Machine Learning Studio (Introduzione a Jupyter Notebooks in Azure Machine Learning Studio)] (http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Servizio Web

###<a name="retraining-models-programmatically"></a>Ripetizione del training dei modelli a livello di codice

**Come ripetere il training dei modelli di Azure Machine Learning a livello di codice?**

Usare le API per la ripetizione del training. for more information, see [Ripetere il training dei modelli di Machine Learning a livello di codice](machine-learning-retrain-models-programmatically.md). È disponibile codice di esempio anche nella [demo sulla ripetizione del training di Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

### <a name="create"></a>Create

**È possibile distribuire il modello in locale o in un'applicazione senza una connessione Internet?**

No.


**Esiste una latenza di base prevista per tutti i servizi Web?**

Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

### <a name="use"></a>Uso

**Quando si esegue il modello predittivo come servizio di esecuzione batch invece che come servizio richiesta-risposta?**

Il servizio richiesta-risposta (RRS) è un servizio Web a bassa latenza e a scalabilità elevata usato per fornire un'interfaccia ai modelli senza stato creati e distribuiti dall'ambiente dell'esperimento. Il servizio di esecuzione batch (BES) è un servizio per il punteggio asincrono di un batch di record di dati. L'input per BES è simile all'input di dati usato in RRS. La differenza principale è che BES legge un blocco di record da diverse origini, ad esempio il servizio BLOB e il servizio tabelle in Azure, il database SQL di Azure, HDInsight (query hive) e origini HTTP. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato](machine-learning-consume-web-services.md).

**Come si aggiorna il modello per il servizio Web distribuito?**

L'aggiornamento di un modello predittivo per un servizio già distribuito è semplice. È sufficiente modificare l'esperimento usato per creare e salvare il modello di apprendimento ed eseguirlo di nuovo. Quando è disponibile una nuova versione del modello di apprendimento, Machine Learning Studio chiede all'utente se vuole aggiornare il servizio Web. Per dettagli su come aggiornare un servizio Web distribuito, vedere [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

Inoltre, è possibile utilizzare le API Retraining.
Per altre informazioni, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](machine-learning-retrain-models-programmatically.md). È disponibile codice di esempio anche nella [demo sulla ripetizione del training di Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Come monitorare il servizio Web distribuito in produzione?**

Una volta distribuito un modello predittivo, è possibile monitorarlo dal portale di Azure classico. Ogni servizio distribuito ha un proprio dashboard, in cui è possibile visualizzare le informazioni di monitoraggio per il servizio. Per altre informazioni sulla gestione dei servizi Web distribuiti, vedere [Gestire un'area di lavoro di Azure Machine Learning](machine-learning-manage-workspace.md).

**Esiste una posizione in cui è possibile visualizzare l'output di RRS/BES?**

Per RRS, la risposta del servizio web si trova in genere dove si visualizza il risultato. È anche possibile scriverla nell'archivio BLOB di Azure. Per BES, l'output viene scritto in un BLOB per impostazione predefinita. È anche possibile scrivere l'output in un database o una tabella usando il modulo [Export Data][export-data] (Esporta dati).

**È possibile creare servizi Web solo da modelli creati in Machine Learning Studio?**

No, è anche possibile creare servizi Web direttamente da Jupyter Notebook e RStudio.

**Dove è possibile trovare informazioni sui codici di errore?**

Per un elenco dei codici di errore e delle relative descrizioni, vedere [Codici di errore dei moduli di Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) .

## <a name="scalability"></a>Scalabilità

**Che cos'è la scalabilità del servizio Web?**

Attualmente, il provisioning dell'endpoint predefinito viene effettuato con 20 richieste RRS simultanee per ogni endpoint. È possibile ridimensionare le richieste simultanee a 200 per endpoint e ridimensionare ogni servizio Web a 10.000 endpoint per servizio Web come descritto in [Ridimensionamento di un servizio Web](machine-learning-scaling-webservice.md). Per BES, ogni endpoint consente l'elaborazione di 40 richieste alla volta e richieste aggiuntive oltre 40 richieste vengono messe in coda. Queste richieste in coda vengono eseguite automaticamente allo svuotamento della coda.


**I processi R sono distribuiti in più nodi?**

No.  


**Quanti dati è possibile usare per il training?**

I moduli in Machine Learning Studio supportano set di dati fino a 10 GB di dati numerici ad alta densità per i casi di utilizzo comuni. Se un modulo richiede più input, la dimensione totale per la somma di tutti gli input è pari a 10 GB. È anche possibile campionare set di dati di dimensioni maggiori tramite query di Hive o del database SQL di Azure oppure tramite la pre-elaborazione con moduli [Learning with Counts][counts] (Apprendimento in base a conteggi) prima dell'inserimento.  

I seguenti tipi di dati possono espandersi in set di dati di dimensioni maggiori durante la normalizzazione della funzionalità e sono limitati a meno di 10 GB:

- sparse
- categorical
- stringhe
- dati binari

I moduli seguenti sono limitati a set di dati inferiori a 10 GB:

- Moduli di raccomandazione
- Modulo SMOTE
- Moduli di script: R, Python, SQL
- Moduli in cui la dimensione dei dati di output può essere maggiore della dimensione dei dati in input, ad esempio Join o Feature Hashing.
- Convalida incrociata, ottimizzazione degli iperparametri del modello, regressione ordinale e multiclasse uno-tutti, quando il numero di iterazioni è molto elevato.

Per i set di dati con dimensioni maggiori di alcuni GB, è necessario caricare i dati in Archiviazione di Azure o nel database SQL di Azure oppure usare HDInsight, invece di caricarli direttamente da un file locale.


**Esistono limitazioni per la dimensione dei vettori?**

Le righe e le colonne sono limitate singolarmente in base alla limitazione .NET di intervallo massimo: 2.147.483.647.

**È possibile modificare le dimensioni della macchina virtuale usata per eseguire il servizio Web?**

No.  

## <a name="security-and-availability"></a>Sicurezza e disponibilità

**Chi ha accesso per impostazione predefinita all'endpoint HTTP per il servizio Web? Come limitare l'accesso all'endpoint?**

Dopo avere distribuito un servizio Web, viene creato un endpoint predefinito per tale servizio. L'endpoint predefinito può essere chiamato usando la chiave API. È possibile aggiungere altri endpoint con le rispettive chiavi dal portale di Azure classico o a livello di programmazione usando le API Gestione dei servizi Web. Le chiavi di accesso sono necessarie per effettuare le chiamate al servizio Web. Per altre informazioni, vedere [Connessione a un servizio Web di Azure Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).


**Cosa succede se non viene trovato l'account di archiviazione di Azure?**

Machine Learning Studio si basa su un account di archiviazione di Azure fornito dall'utente per salvare i dati intermedi quando si esegue il flusso di lavoro. Questo account di archiviazione viene fornito a Machine Learning Studio al momento della creazione di un'area di lavoro. Dopo aver creato l'area di lavoro, se l'account di archiviazione viene eliminato e non è più disponibile, l'area di lavoro non funzionerà più e tutti gli esperimenti nell'area di lavoro non riusciranno.

Se si elimina accidentalmente l'account di archiviazione, ricrearlo con lo stesso nome e nella stessa area dell'account di archiviazione eliminato. Risincronizzare quindi la chiave di accesso.


**Cosa succede se la chiave di accesso dell'account di archiviazione non è sincronizzata?**

Machine Learning Studio si basa su un account di archiviazione di Azure fornito dall'utente per archiviare i dati intermedi quando si esegue il flusso di lavoro. Questo account di archiviazione viene fornito a Machine Learning Studio al momento della creazione di un'area di lavoro e le chiavi di accesso sono associate all'area di lavoro. Dopo aver creato l'area di lavoro, se le chiavi di accesso vengono modificate, l'area di lavoro non riuscirà più ad accedere all'account di archiviazione. L'area di lavoro non funzionerà più e tutti gli esperimenti al suo interno avranno esito negativo.

Se sono state modificate le chiavi di accesso dell'account di archiviazione, sincronizzare di nuovo le chiavi di accesso nell'area di lavoro usando il portale di Azure classico.  


## <a name="azure-marketplace"></a>Azure Marketplace

Vedere [Domande frequenti sulla pubblicazione e sull'uso delle app di Machine Learning in Azure Marketplace](machine-learning-marketplace-faq.md).

## <a name="support-and-training"></a>Supporto e training

**Dove si trovano i training per Azure Machine Learning?**

[centro di documentazione di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) ospita le esercitazioni video e le guide per le procedure. Queste guide dettagliate forniscono un'introduzione ai servizi e indicazioni sul ciclo di vita dell'analisi scientifica dei dati per l'importazione dei dati, la pulizia dei dati, la compilazione di modelli predittivi e la loro distribuzione in produzione con Azure Machine Learning.

Microsoft aggiunge regolarmente nuovo materiale in Machine Learning Center. Per ottenere altro materiale di apprendimento in Machine Learning Center, inviare una richiesta nel [forum dei commenti e suggerimenti degli utenti](https://windowsazure.uservoice.com/forums/257792-machine-learning).

È disponibile formazione anche in [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Come si ottiene il supporto per Azure Machine Learning?**

Per ottenere supporto tecnico per Azure Machine Learning, accedere a [Supporto tecnico di Azure](/support/options/) e selezionare **Machine Learning**.

È anche disponibile un forum della community di Azure Machine Learning su MSDN, in cui è possibile porre domande relative ad Azure Machine Learning. Il forum è monitorato dal team di Azure Machine Learning. Visitare il [forum di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Domande sulla fatturazione

**Come funziona la fatturazione di Machine Learning?**

Il servizio Azure Machine Learning include due componenti: Machine Learning Studio e i servizi Web di Machine Learning.

Per la valutazione di Machine Learning Studio si può usare il livello di fatturazione gratuito.  Il livello gratuito consente anche di distribuire un servizio Web classico con capacità limita.

Dopo aver stabilito che Azure Machine Learning soddisfa le proprie esigenze, è possibile iscriversi al livello standard. Per effettuare l'iscrizione, è necessario avere una sottoscrizione di Microsoft Azure.

Nel piano standard viene fatturata una tariffa mensile per ogni area di lavoro definita in Machine Learning Studio. Quando si esegue un esperimento in Studio, vengono fatturate le risorse di calcolo durante l'esecuzione dell'esperimento. Quando si distribuisce un servizio Web classico, le transazioni e le ore di calcolo vengono fatturate con pagamento in base al consumo.

I nuovi servizi Web di Machine Learning introducono piani di fatturazione che consentono una maggiore prevedibilità dei costi. I prezzi a livelli offrono tariffe scontate ai clienti che necessitano di capacità elevata.

Quando si crea un piano, si assume l'impegno di un costo fisso che copre una quantità inclusa di ore di calcolo API e transazioni API. Se sono necessarie più quantità incluse, è possibile aggiungere altre istanze al piano. Se sono necessarie molte quantità incluse aggiuntive, è possibile scegliere un livello superiore che offre un numero di quantità incluse notevolmente maggiore e un tariffa scontata migliore.

All'esaurimento delle quantità incluse delle istanze esistenti, per l'uso aggiuntivo viene addebitata una tariffa di eccedenza associata al livello del piano di fatturazione.

Nota: le quantità incluse vengono riallocate ogni 30 giorni e quelle inutilizzate non vengono trasferite al periodo successivo.

Per altre informazioni sulla fatturazione e sui prezzi, vedere [Prezzi di Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**Machine Learning offre una versione di valutazione gratuita?**

 Azure Machine Learning offre un'opzione di sottoscrizione gratuita (vedere [Prezzi di Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/) per informazioni dettagliate) e Machine Learning Studio offre una versione di valutazione rapida di 8 ore (per questa versione, accedere a [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2)).

 Quando ci si iscrive a una versione di valutazione gratuita di Azure, è inoltre possibile provare tutti i servizi Azure per un mese. Per altre informazioni sulla versione di valutazione gratuita, vedere [Domande frequenti sulla versione di valutazione gratuita di Azure](/pricing/free-trial-faq/).

**Che cos'è una transazione?**

Una transazione rappresenta una chiamata API a cui Azure Machine Learning risponde. Le transazioni di chiamate al servizio richiesta risposta (RRS) e al servizio esecuzione batch (BES) vengono aggregate e addebitate nel piano di fatturazione.

**Le quantità incluse di transazioni di un piano possono essere usate per transazioni sia RRS che BES?**

Sì. Le transazioni dei servizi RRS e BES vengono aggregate e addebitate nel piano di fatturazione.

**Che cos'è un'ora di calcolo API?**

Un'ora di calcolo API è l'unità di fatturazione per il tempo impiegato per l'esecuzione di chiamate API con le risorse di calcolo di ML. Ai fini della fatturazione, tutte le chiamate vengono aggregate.

**Quanto tempo è necessario in genere per una tipica chiamata API di produzione?**

I tempi delle chiamate API di produzione possano variare in modo significativo, in genere da centinaia di millisecondi a pochi secondi, ma a seconda della complessità dei modelli di elaborazione dei dati e di apprendimento automatico possono essere necessari anche alcuni minuti. Il modo migliore per stimare i tempi delle chiamate API di produzione consiste nell'usare come benchmark un modello nel servizio Machine Learning.

**Che cos'è un'ora di calcolo di Studio?**

Un'ora di calcolo di Studio è l'unità di fatturazione per il tempo aggregato in cui gli esperimenti usano risorse di calcolo in Studio.

**Nei nuovi servizi Web, qual è lo scopo del livello sviluppo/test?**

I nuovi servizi Web di Azure ML offrono più livelli per il provisioning del piano di fatturazione. Il livello sviluppo/test offre quantità incluse limitate che consentono di testare l'esperimento come nuovo servizio Web senza addebito di costi. Viene offerta l'opportunità di provare e osservare il funzionamento.

**Sono previsti addebiti distinti per l'archiviazione?**

Il livello Gratuito di Machine Learning non richiede né consente l'archiviazione separata. Per il livello Standard di Machine Learning, gli utenti devono possedere un account di archiviazione di Azure. L'archiviazione di Azure [viene fatturata separatamente](https://azure.microsoft.com/pricing/details/storage/).

**In che modo Machine Learning supporta attività a disponibilità elevata?**

I tempi delle chiamate API di produzione possano variare in modo significativo, in genere da centinaia di millisecondi a pochi secondi, ma a seconda della complessità dei modelli di elaborazione dei dati e di apprendimento automatico possono essere necessari anche alcuni minuti. Il modo migliore per stimare i tempi delle chiamate API di produzione consiste nell'usare come benchmark un modello nel servizio Machine Learning.

**In quale tipologia specifica di risorse di calcolo verranno eseguite le chiamate API di produzione?**

Machine Learning è un servizio multi-tenant e le risorse di calcolo effettive usate nel back-end variano e sono ottimizzate ai fini delle prestazioni e della prevedibilità.

### <a name="management-of-new-web-services"></a>Gestione dei nuovi servizi Web

**Cosa succede se si elimina il proprio piano?**

Il piano viene rimosso dalla sottoscrizione e viene fatturato un utilizzo ripartito.

Nota: un piano usato da un servizio Web non può essere eliminato. Per eliminare il piano, è necessario assegnare un nuovo piano al servizio Web oppure eliminare il servizio Web.

**Che cos'è un'istanza del piano?**

Un'istanza del piano è un'unità di quantità incluse che è possibile aggiungere al piano di fatturazione. Quando si seleziona un livello per il piano di fatturazione, questo include un'istanza. Se sono necessarie più quantità incluse, è possibile aggiungere istanze del livello di fatturazione selezionato al piano.

**Quante istanze del piano possono essere aggiunte?**

Una sottoscrizione può includere una sola istanza del livello sviluppo/test.

Per i livelli S1, S2 e S3 è possibile aggiungere tutte le istanze necessarie.

Nota: a seconda dell'utilizzo previsto, può essere più conveniente eseguire l'aggiornamento a un livello di quantità incluse superiore anziché aggiungere istanze al livello corrente.

**Cosa succede quando si cambiano i livelli del piano (per aggiornamento o downgrade)?**

Il piano precedente viene eliminato e la fatturazione dell'utilizzo corrente viene ripartita. Per il resto del periodo viene creato un nuovo piano con le intere quantità incluse del livello di aggiornamento o downgrade.

Nota: le quantità incluse vengono allocate per periodo e le quantità inutilizzate non vengono rinnovate.

**Cosa succede quando si aumentano le istanze in un piano?**

Le quantità incluse vengono ripartite e potrebbero trascorrere fino a 24 ore prima che vengano applicate.

**Cosa succede quando si elimina un'istanza di un piano?**

L'istanza viene rimossa dalla sottoscrizione e viene fatturato un utilizzo ripartito.


### <a name="signing-up-for-new-web-services-plans"></a>Iscrizione ai piani dei nuovi servizi Web

**Come ci si iscrive a un piano?**

Per creare i piani di fatturazione sono disponibili due modi.

Quando si distribuisce per la prima volta un nuovo servizio Web, si può scegliere un piano esistente o crearne uno nuovo.

I piani creati in questo modo rientrano nell'area dell'utente, in cui il servizio Web verrà distribuito.

Può essere opportuno definire i piani di fatturazione prima di distribuire il servizio se si vogliono distribuire i servizi in aree diverse da quella predefinita.

In tal caso, è possibile accedere al portale Azure Machine Learning Web Services (Servizi Web di Microsoft Azure Machine Learning) e passare alla pagina Plans (Piani). In questa pagina è possibile aggiungere ed eliminare piani, nonché modificare i piani esistenti.

**Quale piano è consigliabile scegliere come punto di partenza?**

È consigliabile partire dal livello S1 Standard e monitorare l'utilizzo del servizio. Se si riscontra che le quantità incluse vengono usate rapidamente, è possibile aggiungere istanze oppure passare a un livello superiore e usufruire di tariffe scontate migliori. Il piano di fatturazione può essere modificato in base alle esigenze nell'intero ciclo di fatturazione.

**In quali aree sono disponibili i nuovi piani?**

I nuovi piani di fatturazione sono disponibili nelle tre aree di produzione in cui sono supportati i nuovi servizi Web:

* Stati Uniti centro-meridionali
* Europa occidentale
* Asia sudorientale

**Se si hanno servizi Web in più aree, è necessario un piano per ogni area?**

Sì. I prezzi dei piani variano in base all'area. Quando si distribuisce un servizio Web in un'altra area, è necessario assegnare al servizio un piano specifico di tale regione.

### <a name="new-web-services---overages"></a>Nuovi servizi Web: eccedenze

**Come si controlla se si è verificata un'eccedenza nell'utilizzo del servizio Web?**

È possibile visualizzare l'uso in tutti i propri piani nella pagina Plans (Piani) del portale Azure Machine Learning Web Services (Servizi Web di Microsoft Azure Machine Learning). Accedere al portale e fare clic sull'opzione di menu Plans (Piani).

Nelle colonne Transactions (Transazioni) e Compute (Calcolo) della tabella vengono visualizzate le quantità incluse del piano e la percentuale usata.

**Cosa succede quando si esauriscono le quantità incluse del livello sviluppo/test?**

I servizi a cui è assegnato un livello sviluppo/test vengono arrestati fino al periodo successivo o al passaggio a uno dei livelli a pagamento.

**Per i servizi Web classici e le eccedenze dei nuovi servizi Web, come vengono calcolati i prezzi per i carichi di lavoro richiesta-risposta (RRS) e batch (BES)?**

Per un carico di lavoro RRS vengono addebitati tutte le chiamate di transazioni API effettuate e il tempo di calcolo associato a tali richieste. I costi delle transazioni dell'API di produzione RRS vengono calcolati come numero totale delle chiamate API effettuate moltiplicato per il prezzo per 1.000 transazioni (ripartito per singola transazione). I costi delle ore di calcolo dell'API di produzione RRS vengono calcolati come quantità di tempo necessaria per l'esecuzione di ogni chiamata API moltiplicata per il numero totale di transazioni API moltiplicato per il prezzo per ora di calcolo dell'API di produzione.

Per l'eccedenza in S1 Standard, ad esempio, 1.000.000 transazioni API la cui esecuzione richiede 0,72 secondi ciascuna avranno come risultato $ 500 ** (1.000.000 * $ 0,50/1.000 transazioni API) come costo delle transazioni dell'API di produzione e $ 400 (1.000.000 * 0,72 secondi * $ 2/ora) per le ore di calcolo dell'API di produzione, per un totale di $ 900.

Per un carico di lavoro BES, l'addebito avverrà allo stesso modo, ma il costo delle transazioni API rappresenta il numero di processi batch che si inviano e i costi di calcolo rappresentano il tempo di calcolo associato a tali processi batch. I costi delle transazioni dell'API di produzione BES vengono calcolati come numero totale di processi inviati moltiplicato per il prezzo per 1.000 transazioni (ripartito per singola transazione). I costi delle ore di calcolo dell'API di produzione BES vengono calcolati come quantità di tempo necessaria per l'esecuzione di ogni riga nel processo moltiplicata per il numero totale di righe nel processo moltiplicato per il numero totale di processi moltiplicato per il prezzo per ora di calcolo dell'API di produzione. Quando si usa il calcolatore di Machine Learning, la misurazione delle transazioni rappresenta il numero di processi che si intende inviare, mentre il campo del tempo per transazione rappresenta il tempo combinato necessario per l'esecuzione di tutte le righe in ogni processo.

Con l'eccedenza in S1 Standard, ad esempio, se si inviano 100 processi al giorno ognuno dei quali è costituito da 500 righe che impiegano 0,72 secondi ciascuna, i costi di eccedenza mensili equivarranno a $ 1,55 (100 processi al giorno = 3.100 processi/mese * $ 0,50/1.000 transazioni API) come costo delle transazioni dell'API di produzione e a $ 620 (500 righe * 0,72 secondi * 3.100 processi * $ 2/ora) per le ore di calcolo dell'API di produzione, per un totale di $ 621,55.

### <a name="azure-ml-classic-web-services"></a>Servizi Web classici di Azure ML

**È ancora disponibile il pagamento in base al consumo?**
Sì. In Azure Machine Learning sono ancora disponibili i servizi Web classici.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Livello Gratuito e Standard di Azure Machine Learning

**Che cosa include il livello Gratuito di Azure Machine Learning?**

Il livello Gratuito di Azure Machine Learning è concepito per offrire un'introduzione approfondita ad Azure Machine Learning Studio. Per iscriversi è sufficiente un account Microsoft. Il livello Gratuito include l'accesso gratuito a un'area di lavoro di Azure Machine Learning Studio per ogni [account Microsoft](https://www.microsoft.com/account/default.aspx), nonché la possibilità di usare fino a 10 GB di spazio di archiviazione e di rendere operativi i modelli come API di staging. I carichi di lavoro del livello Gratuito non sono coperti da un contratto di servizio e sono destinati esclusivamente allo sviluppo e all'uso personale. I carichi di lavoro del livello Gratuito non possono accedere ai dati con la connessione a un server SQL locale.

**Cosa includono il livello e i piani Standard di Azure Machine Learning?**

Il livello Standard di Azure Machine Learning è una versione di produzione a pagamento di Azure Machine Learning Studio. La tariffa mensile del servizio Azure ML Studio viene fatturata mensilmente per area di lavoro ed è ripartita per i mesi parziali. Le ore di sperimentazione in Azure ML Studio vengono fatturate per ora di calcolo per sperimentazione attiva. La fatturazione è ripartita per ore parziali.  

La fatturazione del servizio API di Azure ML varia a seconda che si tratti di un servizio Web classico o di un nuovo servizio Web.

Gli addebiti seguenti vengono aggregati per ogni area di lavoro della sottoscrizione.

* Sottoscrizione per area di lavoro di Machine Learning: tariffa mensile che consente di accedere a un'area di lavoro di ML Studio. Questa sottoscrizione è necessaria per eseguire esperimenti sia in Studio, sia usando le API di produzione.
* Ore sperimentazione in Studio. Questa misurazione aggrega tutti i costi di calcolo accumulati eseguendo esperimenti in ML Studio e chiamate API di produzione nell'ambiente di staging.
* È possibile accedere ai dati con la connessione a un server SQL locale nei modelli per il training e l'assegnazione dei punteggi.
* Per i servizi Web classici:
    * Ore di calcolo dell'API di produzione. Questa misurazione include i costi di calcolo accumulati dai servizi Web eseguiti nell'ambiente di produzione.
    * Transazioni dell'API di produzione (in migliaia). Questa misurazione include i costi accumulati per ogni chiamata al servizio Web di produzione.

Oltre agli addebiti precedenti, nel caso dei nuovi servizi Web vengono aggregati addebiti al piano selezionato:

* Piano API S1/S2/S3 Standard (unità). Questa misurazione rappresenta il tipo di istanza selezionato per i nuovi servizi Web
* Ore di calcolo API S1/S2/S3 Standard in eccedenza. Questa misurazione include i costi di calcolo accumulati dai nuovi servizi Web eseguiti nell'ambiente di produzione dopo l'esaurimento delle quantità incluse delle istanze esistenti. L'utilizzo aggiuntivo viene addebitato alla tariffa di eccedenza associata al livello dei piani S1/S2/S3.
* Transazioni API S1/S2/S3 Standard in eccedenza (in migliaia). Questa misurazione include i costi accumulati per ogni chiamata al nuovo servizio Web di produzione dopo l'esaurimento delle quantità incluse delle istanze esistenti. L'utilizzo aggiuntivo viene addebitato alla tariffa di eccedenza associata al livello dei piani S1/S2/S3.
* Quantità inclusa di ore di calcolo API. Con i nuovi servizi Web, questa misurazione rappresenta la quantità inclusa di ore di calcolo API
* Quantità inclusa di transazioni API (in migliaia). Con i nuovi servizi Web, questa misurazione rappresenta la quantità inclusa di transazioni API


**Come ci si iscrive al livello Gratuito di Azure ML?**

È sufficiente un account Microsoft. Passare alla [home page di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) e fare clic sul pulsante **Avvia**. Accedere con l'account Microsoft. Verrà automaticamente creata un'area di lavoro nel livello Gratuito. È possibile iniziare immediatamente a esplorare e creare esperimenti di Machine Learning.

**Come ci si iscrive al livello Standard di Azure ML?**

Per creare un'area di lavoro di ML di livello Standard è prima di tutto necessario avere accesso a una sottoscrizione di Azure. È possibile iscriversi per ottenere una sottoscrizione della versione di valutazione gratuita di Azure di 30 giorni ed eseguire in seguito l'aggiornamento a una sottoscrizione di Azure a pagamento oppure acquistare direttamente una sottoscrizione di Azure a pagamento. Dopo aver ottenuto l'accesso alla sottoscrizione, è possibile creare un'area di lavoro di Machine Learning dal portale di Microsoft Azure classico. Vedere le [istruzioni dettagliate](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

In alternativa, si può essere invitati dal proprietario di un'area di lavoro di livello Standard di ML ad accedere alla sua area di lavoro.

**È possibile specificare un account di archiviazione BLOB di Azure personale da usare con il livello Gratuito?**

No. Il livello Standard equivale alla versione del servizio Machine Learning disponibile prima dell'introduzione dei livelli.

**È possibile distribuire i propri modelli di Machine Learning come API nel livello Gratuito?**

Sì. Nell'ambito del livello Gratuito è possibile rendere operativi i modelli di Machine Learning nei servizi API di staging. Per inserire il servizio API di staging in produzione e ottenere un endpoint di produzione per il servizio reso operativo, è necessario usare il livello Standard.

**Qual è la differenza tra la versione di valutazione gratuita di Azure e il livello Gratuito di Azure Machine Learning?**

La [versione di valutazione gratuita di Microsoft Azure](https://azure.microsoft.com/free/) offre crediti che possono essere applicati a qualsiasi servizio di Azure per un mese. Il livello gratuito di Azure Machine Learning offre un accesso continuo specifico al servizio Azure Machine Learning per carichi di lavoro non di produzione.

**Come si trasferisce un esperimento dal livello Gratuito al livello Standard?**

Per copiare gli esperimenti dal livello gratuito al livello Standard:

1.  Accedere ad Azure Machine Learning Studio e verificare che nel selettore delle aree di lavoro nella barra di spostamento in alto siano visualizzate sia l'area di lavoro del livello Gratuito sia quella del livello Standard.
2.  Se ci si trova nell'area di lavoro del livello Standard, passare a quella del livello Gratuito.
3.  Nella visualizzazione elenco degli esperimenti selezionare un esperimento da copiare e fare clic sul pulsante di comando Copy (Copia).
4.  Selezionare l'area di lavoro del livello Standard nella finestra di dialogo popup e fare clic sul pulsante Copy (Copia).
    Tutti i set di dati, il modello con training e così via verranno copiati insieme all'esperimento nell'area di lavoro del livello Standard.
6.  Sarà necessario eseguire di nuovo l'esperimento e ripubblicare il servizio Web nell'area di lavoro del livello Standard.

### <a name="studio-workspace"></a>Area di lavoro di Studio

**Saranno emesse diverse fatture per le diverse aree di lavoro?**

Gli addebiti delle aree di lavoro vengono suddivisi separatamente per ogni misurazione applicabile in una singola fattura.

**In quale tipologia specifica di risorse di calcolo verranno eseguiti gli esperimenti?**

Machine Learning è un servizio multi-tenant e le risorse di calcolo effettive usate nel back-end variano e sono ottimizzate ai fini delle prestazioni e della prevedibilità.

### <a name="guest-access"></a>Accesso guest

**Che cos'è l'accesso guest ad Azure Machine Learning Studio?**

L'accesso guest è un'esperienza di valutazione limitata che consente di creare ed eseguire esperimenti in Azure Machine Learning Studio gratuitamente e senza autenticazione. Le sessioni guest sono non persistenti (non possono essere salvate) e limitate a 8 ore. Le altre restrizioni includono la mancanza di supporto per R e Python, l'assenza di API di staging e dimensioni dei set di dati e capacità di archiviazione limitate. Gli utenti che scelgono di accedere con un account Microsoft, invece, hanno accesso completo al livello gratuito di Machine Learning Studio descritto sopra, che include un'area di lavoro persistente e funzionalità più complete. Per scegliere un'esperienza gratuita di Machine Learning, fare clic sul pulsante **Get started** (Per iniziare) in [https://studio.azureml.net](https://studio.azureml.net)e selezionare l'accesso guest o l'accesso con un account Microsoft.

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



<!--HONumber=Oct16_HO2-->


