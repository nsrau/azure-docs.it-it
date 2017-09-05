---
title: Analisi del sentiment su Twitter in tempo reale con Analisi di flusso di Azure | Microsoft Docs
description: Informazioni sull'uso di Analisi di flusso per l'analisi del sentiment su Twitter in tempo reale. Istruzioni dettagliate, dalla generazione degli eventi fino ai dati in un dashboard in tempo reale.
keywords: analisi delle tendenze twitter in tempo reale, analisi del sentiment, analisi dei social media, esempio di analisi delle tendenze
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 98230a8b61d1776a9ab23fd416af306efc700959
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analisi del sentiment su Twitter in tempo reale in Analisi di flusso di Azure

Si imparerà a creare una soluzione di analisi del sentiment per i social media portando gli eventi di Twitter in tempo reale negli hub eventi di Azure. Si potrà quindi scrivere una query di Analisi di flusso di Azure per analizzare i dati e archiviare i risultati per analisi successive o usare un dashboard e [Power BI](https://powerbi.com/) per rendere disponibili informazioni rilevanti in tempo reale.

Gli strumenti di analisi dei social media permettono alle organizzazioni di determinare gli argomenti di tendenza, vale a dire gli argomenti e gli atteggiamenti che registrano un numero elevato di post nei social media. L'analisi del sentiment, detta anche *opinion mining*, usa gli strumenti di analisi dei social media per determinare le attitudini rispetto a un prodotto, un'idea e così via. 

L'analisi delle tendenze Twitter in tempo reale offre un ottimo esempio di strumento di analisi, perché il modello di sottoscrizione hashtag consente di ascoltare parole chiave specifiche (hashtag) e sviluppare l'analisi del sentiment del feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: analisi del sentiment su social media in tempo reale

Una società con un sito Web di notizie è interessata a superare la concorrenza offrendo contenuti del sito immediatamente fruibili per i lettori. La società usa l'analisi dei social media su argomenti rilevanti per i lettori eseguendo l'analisi del sentiment in tempo reale sui dati di Twitter.

Per identificare in tempo reale gli argomenti di tendenza su Twitter, la società deve eseguire un'analisi in tempo reale sul volume dei tweet e sul sentiment relativo agli argomenti più importanti. In altre parole, ciò che serve è un motore di analisi del sentiment basato su questo feed di social media.

## <a name="prerequisites"></a>Prerequisiti
In questa esercitazione si usa un'applicazione client che si connette a Twitter e cerca i tweet con determinati hashtag, che è possibile impostare. Per eseguire l'applicazione e analizzare i tweet tramite Analisi di flusso di Azure, è necessario quanto segue:

* Una sottoscrizione di Azure
* Un account Twitter 
* Un'applicazione Twitter e il [token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) per tale applicazione. Più avanti sono riportate istruzioni dettagliate per creare un'applicazione Twitter.
* L'applicazione TwitterWPFClient, che legge il feed di Twitter. Per ottenere questa applicazione, scaricare il file [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) da GitHub e decomprimere il pacchetto in una cartella nel computer. Per visualizzare il codice sorgente ed eseguire l'applicazione in un debugger, è possibile ottenere il codice sorgente da [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Creare un hub eventi per l'input di Analisi di flusso

L'applicazione di esempio genera eventi e ne esegue il push a un hub eventi di Azure. Gli hub eventi di Azure sono la soluzione preferita per l'inserimento di eventi per Analisi di flusso. Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Creare uno spazio dei nomi dell'hub eventi e un hub eventi
In questa procedura si creerà uno spazio dei nomi dell'hub eventi e quindi si aggiungerà un hub eventi a tale spazio. Gli spazi dei nomi degli hub eventi consentono di raggruppare in modo logico le istanze dei bus di eventi correlate. 

1. Accedere al portale di Azure e fare clic su **Nuovo** > **Internet delle cose** > **Hub eventi**. 

2. Nel pannello **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi, ad esempio `<yourname>-socialtwitter-eh-ns`. È possibile usare qualsiasi nome per lo spazio dei nomi, a condizione che sia valido per un URL e univoco in Azure. 
    
3. Selezionare una sottoscrizione, creare o scegliere un gruppo di risorse e quindi fare clic su **Crea**. 

    ![Creare uno spazio dei nomi dell'hub eventi](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Al termine della distribuzione, lo spazio dei nomi dell'hub eventi è disponibile nell'elenco delle risorse di Azure. 

5. Scegliere il nuovo spazio dei nomi e, nel relativo pannello, fare clic su  **+&nbsp;Hub eventi**. 

    ![Pulsante Aggiungi hub eventi per creare un nuovo hub eventi ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Assegnare il nome `socialtwitter-eh` al nuovo hub eventi. È possibile usare un nome diverso. In questo caso, tenerne traccia, poiché sarà necessario in un secondo momento. Non sono richieste altre impostazioni per l'hub eventi.

    ![Pannello per creare un nuovo hub eventi](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Fare clic su **Crea**.


### <a name="grant-access-to-the-event-hub"></a>Concedere l'accesso all'hub eventi

Prima che un processo possa inviare dati a un hub eventi, è necessario che per l'hub siano configurati criteri che consentano l'accesso appropriato. I criteri di accesso generano una stringa di connessione che include informazioni di autorizzazione.

1.  Nel pannello dello spazio dei nomi dell'evento fare clic su **Hub eventi** e quindi sul nome del nuovo hub eventi.

2.  Nel pannello dell'hub eventi fare clic su **Criteri di accesso condiviso** e quindi su **+&nbsp;Aggiungi**.

    >[!NOTE]
    >Verificare di usare l'hub eventi e non lo spazio dei nomi.

3.  Aggiungere criteri denominati `socialtwitter-access` e per **Attestazione** selezionare **Gestisci**.

    ![Pannello per creare nuovi criteri di accesso all'hub eventi](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Fare clic su **Crea**.

5.  Dopo aver distribuito i criteri, fare clic nell'elenco dei criteri di accesso condiviso.

6.  Individuare la casella con l'etichetta **CONNECTION STRING-PRIMARY KEY** e fare clic sul pulsante Copia accanto alla stringa di connessione. 
    
    ![Copia della chiave della stringa di connessione primaria dai criteri di accesso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Incollare la stringa di connessione in un editor di testo. Sarà necessario usare questa stringa nella sezione successiva, dopo avervi apportato alcune piccole modifiche.

    La stringa di connessione ha un aspetto simile al seguente:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Si noti che la stringa di connessione contiene più coppie chiave-valore, separate da punti e virgola: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` e `EntityPath`.  

    > [!NOTE]
    > Per sicurezza, le parti della stringa di connessione nell'esempio sono state rimosse.

8.  Nell'editor di testo rimuovere la coppia `EntityPath` dalla stringa di connessione. Non dimenticare di rimuovere il punto e virgola che la precede. Al termine dell'operazione, la stringa di connessione ha un aspetto simile al seguente:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Configurare e avviare l'applicazione client Twitter
L'applicazione client ottiene gli eventi tweet direttamente da Twitter. A questo scopo, è necessaria l'autorizzazione per chiamare le API di streaming di Twitter. Per configurare questa autorizzazione, si crea un'applicazione in Twitter in modo da generare credenziali univoche, ad esempio un token OAuth. Si può quindi configurare l'applicazione client in modo da usare queste credenziali quando esegue chiamate API. 

### <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter
Se si ha già un'applicazione Twitter utilizzabile per questa esercitazione, è possibile crearne una. È necessario avere già un account Twitter.

> [!NOTE]
> La procedura esatta da seguire in Twitter per creare un'applicazione e ottenere le chiavi, i segreti e il token può cambiare. Se queste istruzioni non corrispondono alle informazioni visualizzate sul sito di Twitter, consultare la documentazione per sviluppatori di Twitter.

1. Passare alla [pagina di gestione delle applicazioni Twitter](https://apps.twitter.com/). 

2. Creare una nuova applicazione. 

    * Per l'URL del sito Web, specificare un URL valido. Non è necessario che corrisponda a un sito live, ma non è possibile specificare semplicemente `localhost`.
    * Lasciare vuoto il campo relativo al callback. L'applicazione client usata per questa esercitazione non richiede callback.

    ![Creazione di un'applicazione in Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Facoltativamente, modificare le autorizzazioni dell'applicazione impostandole in sola lettura.

4. Una volta creata l'applicazione, accedere alla pagina **Keys and Access Tokens** (Chiavi e token di accesso).

5. Fare clic sul pulsante per generare un token di accesso e un segreto del token.

Tenere queste informazioni a portata di mano perché saranno necessarie nella procedura successiva.

>[!NOTE]
>Le chiavi e i segreti dell'applicazione Twitter consentono l'accesso all'account Twitter personale. Trattarle come dati sensibili, allo stesso modo della password di Twitter. Evitare, ad esempio, di incorporare queste informazioni in un'applicazione che si prevede di distribuire ad altri utenti. 


### <a name="configure-the-client-application"></a>Configurare l'applicazione client
È stata creata un'applicazione client che si connette ai dati di Twitter usando le [API di streaming di Twitter](https://dev.twitter.com/streaming/overview) per raccogliere gli eventi tweet relativi a un set specifico di argomenti. L'applicazione usa lo strumento open source [Sentiment140](http://help.sentiment140.com/) che assegna il valore di sentiment seguente a ogni tweet:

* 0 = negativo
* 2 = neutrale
* 4 = positivo

Dopo l'assegnazione di un valore di sentiment, gli eventi tweet vengono inviati tramite push all'hub eventi creato in precedenza.

Prima di essere eseguita, l'applicazione richiede all'utente determinate informazioni, ad esempio le chiavi di Twitter e la stringa di connessione all'hub eventi. È possibile specificare le informazioni di configurazione nei modi seguenti:

* Eseguire l'applicazione e quindi usare l'interfaccia utente dell'applicazione per immettere le chiavi, i segreti e stringa di connessione. In questo caso, le informazioni di configurazione vengono usate per la sessione corrente, ma non vengono salvate.
* Modificare il file config dell'applicazione e impostare i valori in tale file. Questo approccio consente di salvare in modo permanente le informazioni di configurazione, ma questo significa anche che informazioni potenzialmente riservate vengono archiviate in testo normale nel computer.

La procedura seguente illustra entrambi gli approcci. 

1. Verificare di aver scaricato e decompresso il file dell'applicazione [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip), come indicato nei prerequisiti.

2. Per impostare i valori in fase di esecuzione (e solo per la sessione corrente), eseguire l'applicazione `TwitterWPFClient.exe`. Quando richiesto dall'applicazione, immettere i valori seguenti:

    * La chiave utente di Twitter (chiave API).
    * Il segreto utente di Twitter (segreto API).
    * Il token di accesso di Twitter.
    * Il segreto del token di accesso di Twitter.
    * Le informazioni della stringa di connessione salvate in precedenza. Verificare di usare la stringa di connessione da cui è stata rimossa la coppia chiave-valore `EntityPath`.
    * Le parole chiave di Twitter per cui si vuole determinare il sentiment.

   ![Applicazione TwitterWpfClient in esecuzione, con le impostazioni oscurate](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Per impostare i valori in modo permanente, usare un editor di testo per aprire il file TwitterWpfClient.exe.config. Nell'elemento `<appSettings>` definire le impostazioni seguenti:

    * Per `oauth_consumer_key` impostare la chiave utente di Twitter (chiave API). 
    * Per `oauth_consumer_secret` impostare il segreto utente di Twitter (segreto API).
    * Per `oauth_token` impostare il token di accesso di Twitter.
    * Per `oauth_token_secret` impostare il segreto del token di accesso di Twitter.

    Più avanti, nell'elemento `<appSettings>`, apportare queste modifiche:

    * Per `EventHubName` impostare il nome dell'hub eventi, ovvero il valore del percorso dell'entità.
    * Per `EventHubNameConnectionString` impostare la stringa di connessione. Verificare di usare la stringa di connessione da cui è stata rimossa la coppia chiave-valore `EntityPath`.

    La sezione `<appSettings>` ha un aspetto simile all'esempio seguente. Per motivi di chiarezza e sicurezza, è stato applicato il ritorno a capo automatico ad alcune righe e sono stati rimossi alcuni caratteri.

    ![File di configurazione dell'applicazione TwitterWpfClient in un editor di testo che mostra i segreti e le chiavi di Twitter e le informazioni della stringa di connessione all'hub eventi](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Se non si è già avviata l'applicazione, eseguire ora TwitterWpfClient.exe. 

5. Fare clic sul pulsante di avvio verde per raccogliere i dati del sentiment sul social media. Gli eventi tweet con i valori **CreatedAt**, **Topic** e **SentimentScore** vengono inviati all'hub eventi.

    ![Applicazione TwitterWpfClient in esecuzione, con l'elenco di tweet visualizzato](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Se vengono restituiti errori e nella parte inferiore della finestra non viene visualizzato un flusso di tweet, controllare le chiavi e i segreti. Verificare inoltre che la stringa di connessione non includa la chiave `EntityPath` e il relativo valore.


## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

Ora che gli eventi tweet vengono trasmessi in flusso in tempo reale da Twitter, è possibile impostare un processo di Analisi di flusso per analizzare questi eventi in tempo reale.

1. Nel portale di Azure fare clic su **Nuovo** > **Internet delle cose** > **Processo di Analisi di flusso**.

2. Assegnare il nome `socialtwitter-sa-job` al processo, specificare una sottoscrizione, un gruppo di risorse e un percorso.

    È consigliabile posizionare il processo e l'hub eventi nella stessa area per ottenere prestazioni ottimali ed evitare di pagare il trasferimento dei dati tra aree.

    ![Creazione di un nuovo processo di Analisi di flusso](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Fare clic su **Crea**.

    Verrà creato il processo e il portale visualizzerà i relativi dettagli.


## <a name="specify-the-job-input"></a>Specificare l'input del processo

1. Nel processo di Analisi di flusso, in **Topologia processo** nella parte centrale del pannello, fare clic su **Input**. 

2. Nel pannello **Input** fare clic su **+&nbsp;Aggiungi** e quindi compilare il pannello con questi valori:

    * **Alias di input**: usare il nome `TwitterStream`. Se si usa un nome diverso, tenerne traccia, poiché sarà necessario in un secondo momento.
    * **Tipo di origine**: selezionare **Flusso dati**.
    * **Origine**: selezionare **Hub eventi**.
    * **Opzioni di importazione**: selezionare **Usa l'hub eventi della sottoscrizione corrente**. 
    * **Spazio dei nomi del bus di servizio**: selezionare lo spazio dei nomi dell'hub eventi creato in precedenza (`<yourname>-socialtwitter-eh-ns`).
    * **Hub eventi**: selezionare l'hub eventi creato in precedenza (`socialtwitter-eh`).
    * **Nome criteri hub eventi**: selezionare i criteri di accesso creati in precedenza (`socialtwitter-access`).

    ![Creare un nuovo input per il processo di Analisi di flusso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Fare clic su **Crea**.


## <a name="specify-the-job-query"></a>Specificare la query del processo

Analisi di flusso supporta un semplice modello di query dichiarativa per descrivere le trasformazioni. Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Questa esercitazione permette di creare e testare diverse query su dati di Twitter.

Per confrontare il numero di menzioni tra gli argomenti, è possibile usare una [finestra a cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) per ottenere il conteggio delle menzioni per argomento ogni cinque secondi.

1. Chiudere il pannello **Input** se non si è già fatto.

2. Nel pannello del processo fare clic sulla casella **Query**. Azure elenca gli input e gli output configurati per il processo e consente di creare una query per trasformare il flusso di input nel momento in cui viene inviato all'output.

3. Verificare che l'applicazione TwitterWpfClient sia in esecuzione. 

3. Nel pannello **Query** fare clic sui puntini di sospensione accanto all'input `TwitterStream` e quindi selezionare **Campiona dati da input**.

    ![Voci di menu per usare dati di esempio per la voce del processo di Analisi di flusso, con l'opzione "Campiona dati da input" selezionata](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Verrà aperto un pannello che consente di specificare la quantità di dati di esempio da ottenere, definita in termini di durata della lettura del flusso di input.

4. Impostare **Minuti** su 3 e quindi fare clic su **OK**. 
    
    ![Opzioni per il campionamento del flusso di input, con l'opzione "3 minuti" selezionata.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure campiona i dati dal flusso di input per una durata di tre minuti e invia una notifica quando i dati di esempio sono pronti. Questa operazione richiede un breve tempo. 

    I dati di esempio vengono archiviati temporaneamente e sono disponibili finché rimane aperta la finestra di query. Se si chiude la finestra di query, i dati di esempio verranno rimossi e sarà necessario creare un nuovo set di dati. 

5. Modificare la query nell'editor di codice nel modo seguente:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Se non si è usato `TwitterStream` come alias per l'input, sostituire l'alias per `TwitterStream` nella query.  

    In questa query viene usata la parola chiave **TIMESTAMP BY** per specificare un campo di timestamp nel payload da usare nel calcolo temporale. Se il campo non è stato specificato, l'operazione di windowing viene eseguita usando l'ora in cui ogni evento è arrivato all'hub eventi. Altre informazioni sono disponibili nella sezione relativa a tempo di arrivo e tempo di applicazione nelle [informazioni di riferimento sulle query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Questa query accede anche a un timestamp per la fine di ogni finestra usando la proprietà **System.Timestamp**.

5. Fare clic su **Test**. La query viene eseguita sui dati che sono stati campionati.
    
6. Fare clic su **Salva**. In questo modo la query viene salvata nell'ambito del processo di Analisi di flusso. I dati di esempio non vengono salvati.


## <a name="experiment-using-different-fields-from-the-stream"></a>Provare a usare diversi campi dal flusso 

La tabella seguente elenca i campi che fanno parte dei dati di flusso JSON. È possibile sperimentare nell'editor di query.

|Proprietà JSON | Definizione|
|--- | ---|
|CreatedAt | Orario di creazione del tweet|
|Argomento | Argomento che corrisponde alla parola chiave specificata|
|SentimentScore | Punteggio del sentiment da Sentiment140|
|Autore | Handle di Twitter che ha inviato il tweet|
|Text | Corpo completo del tweet|


## <a name="create-an-output-sink"></a>Creare un sink di output

A questo punto sono stati definiti un flusso di eventi, un input dell'hub eventi per inserire gli eventi e una query per eseguire una trasformazione nel flusso. L'ultimo passaggio consiste nel definire un sink di output per il processo.  

In questa esercitazione gli eventi tweet aggregati ottenuti dalla query del processo vengono scritti nell'archiviazione BLOB di Azure.  È anche possibile eseguire il push dei risultati al database SQL di Azure, ad Archiviazione tabelle di Azure, a Hub eventi o a Power BI, in base alle esigenze specifiche dell'applicazione.

## <a name="specify-the-job-output"></a>Specificare l'output del processo

1. Nella sezione **Topologia processo** fare clic sulla casella **Output**. 

2. Nel pannello **Output** fare clic su **+&nbsp;Aggiungi** e quindi compilare il pannello con questi valori:

    * **Alias di output**: usare il nome `TwitterStream-Output`. 
    * **Sink**: selezionare **Archivio BLOB**.
    * **Opzioni di importazione**: selezionare **Usa l'archiviazione BLOB della sottoscrizione corrente**.
    * **Account di archiviazione**. Selezionare **Crea un nuovo account di archiviazione**.
    * **Account di archiviazione** (seconda casella). Immettere `YOURNAMEsa`, dove `YOURNAME` rappresenta il nome o un'altra stringa univoca. Il nome può contenere solo lettere minuscole e numeri e deve essere univoco in Azure. 
    * **Contenitore**. Immettere `socialtwitter`.
    Il nome dell'account di archiviazione e il nome del contenitore vengono usati insieme per fornire un URI per l'archiviazione BLOB, in modo simile al seguente: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![Pannello "Nuovo output" per il processo di Analisi di flusso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Fare clic su **Crea**. 

    Azure crea l'account di archiviazione e genera automaticamente una chiave. 

5. Chiudere il pannello **Output**. 


## <a name="start-the-job"></a>Avviare il processo

Sono stati specificati l'input del processo, la query e l'output. A questo punto si è pronti ad avviare il processo di Analisi di flusso.

1. Verificare che l'applicazione TwitterWpfClient sia in esecuzione. 

2. Nel pannello del processo fare clic su **Avvia**.

    ![Avviare il processo di Analisi di flusso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Nel pannello **Avvia processo**, per **Ora di inizio dell'output del processo**, selezionare **Ora** e quindi fare clic su **Avvia**. 

    ![Pannello "Avvia processo" per il processo di Analisi di flusso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure informa l'utente quando il processo viene avviato e, nel pannello del processo, lo stato viene visualizzato come **In esecuzione**.

    ![Processo in esecuzione](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Visualizzare l'output per l'analisi del sentiment

Quando il processo è in esecuzione ed elabora il flusso di Twitter in tempo reale, è possibile visualizzare l'output per l'analisi del sentiment.

Per visualizzare l'output del processo in tempo reale, usare uno strumento come [Azure Storage Explorer](https://http://storageexplorer.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). A questo punto è possibile usare [Power BI](https://powerbi.com/) per estendere l'applicazione in modo da includere un dashboard personalizzato come quello riportato nello screenshot seguente:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Creare un'altra query per identificare gli argomenti di tendenza

Un'altra query che è possibile usare per comprendere il sentiment su Twitter si basa su un [finestra temporale scorrevole](https://msdn.microsoft.com/library/azure/dn835051.aspx). Per identificare gli argomenti di tendenza si cercano gli argomenti in cui le menzioni superano un valore soglia entro un determinato periodo di tempo.

Ai fini di questa esercitazione, si cercano gli argomenti menzionati più di 20 volte negli ultimi cinque secondi.

1. Nel pannello del processo fare clic su **Arresta** per arrestare il processo. 

2. Nella sezione **Topologia processo** fare clic sulla casella **Query**. 

3. Modificare la query nel modo seguente:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Fare clic su **Salva**.

5. Verificare che l'applicazione TwitterWpfClient sia in esecuzione. 

6. Fare clic su **Avvia** per riavviare il processo usando la nuova query.


## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

