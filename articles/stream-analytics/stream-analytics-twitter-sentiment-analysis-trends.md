---
title: Analisi del sentiment su Twitter in tempo reale con Analisi di flusso di Azure
description: Questo articolo descrive come usare Analisi di flusso per l'analisi del sentiment su Twitter in tempo reale. Istruzioni dettagliate, dalla generazione degli eventi fino ai dati in un dashboard in tempo reale.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 7023651b09abc8c3124c7bf71608018d5cb72e25
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162005"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analisi del sentiment su Twitter in tempo reale in Analisi di flusso di Azure

Questo articolo illustra come creare una soluzione di analisi dei sentimenti per i social media portando gli eventi di Twitter in tempo reale negli hub eventi di Azure. È possibile scrivere una query di analisi di flusso di Azure per analizzare i dati e archiviare i risultati per un uso successivo oppure creare un dashboard [Power bi](https://powerbi.com/) per fornire informazioni dettagliate in tempo reale.

Gli strumenti di analisi dei social media permettono alle organizzazioni di determinare gli argomenti di tendenza, Gli argomenti di tendenza sono soggetti e atteggiamenti che hanno un volume elevato di post sui social media. L'analisi dei sentimenti, definita anche *Opinion Mining*, USA gli strumenti di analisi dei social media per determinare gli atteggiamenti verso un prodotto o un'idea. 

L'analisi delle tendenze Twitter in tempo reale è un ottimo esempio di strumento di analisi, perché il modello di sottoscrizione hashtag consente di ascoltare parole chiave specifiche (hashtag) e sviluppare l'analisi dei sentimenti del feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: analisi del sentiment su social media in tempo reale

Una società con un sito Web di notizie è interessata a superare la concorrenza offrendo contenuti del sito immediatamente fruibili per i lettori. La società usa l'analisi dei social media su argomenti rilevanti per i lettori eseguendo l'analisi del sentiment in tempo reale sui dati di Twitter.

Per identificare in tempo reale gli argomenti di tendenza su Twitter, la società deve eseguire un'analisi in tempo reale sul volume dei tweet e sul sentiment relativo agli argomenti più importanti.

## <a name="prerequisites"></a>Prerequisites

Questa guida illustra come usare un'applicazione client che si connette a Twitter e cerca Tweet con determinati hashtag, che è possibile impostare. Per eseguire l'applicazione e analizzare i tweet usando analisi di flusso di Azure, è necessario disporre di quanto segue:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).

* Un account [Twitter](https://twitter.com) .

* Applicazione TwitterClientCore, che legge il feed di Twitter. Per ottenere l'applicazione, scaricare [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Installare il [interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).

## <a name="create-an-event-hub-for-streaming-input"></a>Creare un hub eventi per l'input di streaming

L'applicazione di esempio genera eventi e ne esegue il push a un hub eventi di Azure. Hub eventi di Azure è il metodo preferito per l'inserimento di eventi per analisi di flusso. Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Creare uno spazio dei nomi dell'hub eventi e un hub eventi
In questa sezione si crea uno spazio dei nomi dell'hub eventi e si aggiunge un hub eventi a tale spazio dei nomi. Gli spazi dei nomi degli hub eventi consentono di raggruppare in modo logico le istanze dei bus di eventi correlate. 

1. Accedere al portale di Azure e selezionare **Crea una risorsa**. Quindi. cercare **Hub eventi** e selezionare **Crea**.

2. Nella pagina **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. È possibile usare qualsiasi nome per lo spazio dei nomi, ma il nome deve essere valido per un URL e deve essere univoco in Azure. 
    
3. Selezionare un piano tariffario e una sottoscrizione e creare o scegliere un gruppo di risorse. Scegliere quindi un percorso e selezionare **Crea**. 
 
4. Al termine della distribuzione dello spazio dei nomi, passare al gruppo di risorse e trovare lo spazio dei nomi dell'hub eventi nell'elenco delle risorse di Azure. 

5. Dal nuovo spazio dei nomi selezionare **+&nbsp;Hub eventi**. 

6. Denominare il nuovo hub eventi *socialtwitter-eh*. È possibile usare un nome diverso. In questo caso, tenerne traccia, poiché sarà necessario in un secondo momento. Non sono richieste altre impostazioni per l'hub eventi.
 
7. Selezionare **Create** (Crea).

### <a name="grant-access-to-the-event-hub"></a>Concedere l'accesso all'hub eventi

Prima che un processo possa inviare dati a un hub eventi, l'hub eventi richiede un criterio che consenta l'accesso. I criteri di accesso generano una stringa di connessione che include informazioni di autorizzazione.

1.  Nella barra di spostamento sul lato sinistro dello spazio dei nomi di hub eventi selezionare **Hub eventi**, disponibile nella sezione **entità** . Selezionare quindi l'hub eventi appena creato.

2.  Nella barra di spostamento sul lato sinistro selezionare criteri di **accesso condiviso** in **Impostazioni**.

    >[!NOTE]
    >Per lo spazio dei nomi dell'hub eventi e per l'hub eventi è disponibile un'opzione Criteri di accesso condiviso. Assicurarsi di lavorare nel contesto dell'hub eventi, non nello spazio dei nomi globale dell'hub eventi.

3.  Nella pagina Criteri di accesso selezionare **+ Aggiungi**. Immettere quindi *socialtwitter-Access* per il **nome del criterio** e selezionare la casella di controllo **Gestisci** .
 
4.  Selezionare **Create** (Crea).

5.  Dopo aver distribuito i criteri, selezionare i criteri nell'elenco dei criteri di accesso condiviso.

6.  Individuare la casella stringa di **connessione con etichetta chiave primaria** e selezionare il pulsante copia accanto alla stringa di connessione.
 
7.  Incollare la stringa di connessione in un editor di testo. Questa stringa di connessione è necessaria per la sezione successiva dopo avere apportato alcune piccole modifiche.

   La stringa di connessione ha un aspetto simile al seguente:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Si noti che la stringa di connessione contiene più coppie chiave-valore, separate da punti e virgola: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` e `EntityPath`.  

   > [!NOTE]
   > Per sicurezza, le parti della stringa di connessione nell'esempio sono state rimosse.

8.  Nell'editor di testo rimuovere la coppia `EntityPath` dalla stringa di connessione. Non dimenticare di rimuovere il punto e virgola che la precede. Al termine dell'operazione, la stringa di connessione ha un aspetto simile al seguente:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=
   ```

## <a name="configure-and-start-the-twitter-client-application"></a>Configurare e avviare l'applicazione client Twitter

L'applicazione client ottiene gli eventi tweet direttamente da Twitter. A questo scopo, è necessaria l'autorizzazione per chiamare le API di streaming di Twitter. Per configurare questa autorizzazione, si crea un'applicazione in Twitter in modo da generare credenziali univoche, ad esempio un token OAuth. Si può quindi configurare l'applicazione client in modo da usare queste credenziali quando esegue chiamate API. 

### <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter
Se non si ha già un'applicazione Twitter che è possibile usare per questa guida, è possibile crearne una. È necessario avere già un account Twitter.

> [!NOTE]
> La procedura esatta da seguire in Twitter per creare un'applicazione e ottenere le chiavi, i segreti e il token può cambiare. Se queste istruzioni non corrispondono alle informazioni visualizzate sul sito di Twitter, consultare la documentazione per sviluppatori di Twitter.

1. In un Web browser passare a [Twitter per sviluppatori](https://developer.twitter.com/en/apps) e selezionare **Create an app** (Crea un'app). Potrebbe essere visualizzato un messaggio che indica che è necessario richiedere un account per sviluppatori Twitter. A tale scopo, dopo che l'applicazione è stata approvata, verrà visualizzato un messaggio di posta elettronica di conferma. L'approvazione di un account di sviluppatore potrebbe richiedere diversi giorni.

   ![Dettagli dell'applicazione Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Dettagli dell'applicazione Twitter")

2. Nella pagina **Create an application** (Crea applicazione) specificare i dettagli per la nuova app e quindi selezionare **Create your Twitter application** (Crea applicazione Twitter).

   ![Dettagli dell'applicazione Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Dettagli dell'applicazione Twitter")

3. Nella pagina dell'applicazione selezionare **Keys and Tokens** (Chiavi e token) e copiare i valori di **Consumer API Key** (Chiave API consumer) e **Consumer API Secret Key** (Chiave privata API consumer). Selezionare inoltre **Create** (Crea) in **Access Token and Access Token Secret** (Token di accesso e Segreto del token di accesso) per generare i token di accesso. Copiare i valori di **Access Token** (Token di accesso) e **Access Token Secret** (Segreto del token di accesso).

   Salvare i valori recuperati per l'applicazione Twitter. I valori sono necessari in un secondo momento.

> [!NOTE]
> Le chiavi e i segreti dell'applicazione Twitter consentono l'accesso all'account Twitter personale. Trattarle come dati sensibili, allo stesso modo della password di Twitter. Evitare, ad esempio, di incorporare queste informazioni in un'applicazione che si prevede di distribuire ad altri utenti. 

### <a name="configure-the-client-application"></a>Configurare l'applicazione client

È stata creata un'applicazione client che si connette ai dati di Twitter usando le [API di streaming di Twitter](https://dev.twitter.com/streaming/overview) per raccogliere gli eventi tweet relativi a un set specifico di argomenti.

Prima di essere eseguita, l'applicazione richiede all'utente determinate informazioni, ad esempio le chiavi di Twitter e la stringa di connessione all'hub eventi.

1. Assicurarsi di aver scaricato l'applicazione [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) , come elencato nei prerequisiti.

2. Usare un editor di testo per aprire il file *app. config* . Apportare le modifiche seguenti all'elemento `<appSettings>`:

   * Impostare `oauth_consumer_key` sulla chiave utente di Twitter (chiave API). 
   * Impostare `oauth_consumer_secret` sul segreto del consumer di Twitter (chiave privata API).
   * Impostare `oauth_token` per il token di accesso di Twitter.
   * Impostare `oauth_token_secret` sul segreto del token di accesso di Twitter.
   * Per `EventHubNameConnectionString` impostare la stringa di connessione. Verificare di usare la stringa di connessione da cui è stata rimossa la coppia chiave-valore `EntityPath`.
   * Impostare `EventHubName` sul nome dell'hub eventi (ovvero il valore del percorso dell'entità).

3. Aprire la riga di comando e passare alla directory in cui si trova l'app TwitterClientCore. Usare il comando `dotnet build` per compilare il progetto. Usare quindi il comando `dotnet run` per eseguire l'app. L'app invia i tweet all'hub eventi.

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

Ora che gli eventi tweet vengono trasmessi in flusso in tempo reale da Twitter, è possibile impostare un processo di Analisi di flusso per analizzare questi eventi in tempo reale.

1. Nella portale di Azure passare al gruppo di risorse e selezionare **+ Aggiungi**. Quindi cercare il **processo di analisi di flusso** e selezionare **Crea**.

2. Assegnare il nome `socialtwitter-sa-job` al processo, specificare una sottoscrizione, un gruppo di risorse e un percorso.

    È consigliabile posizionare il processo e l'hub eventi nella stessa area per ottenere prestazioni ottimali ed evitare di pagare il trasferimento dei dati tra aree.

3. Selezionare **Create** (Crea). Quindi passare al processo al termine della distribuzione.

## <a name="specify-the-job-input"></a>Specificare l'input del processo

1. Nel processo di analisi di flusso selezionare **input** dal menu a sinistra in **topologia processo**.

2. Selezionare **+&nbsp;aggiungere input di flusso** > **Hub eventi**. Compilare il nuovo modulo di **input** con le seguenti informazioni:

   |**Impostazione**  |**Valore consigliato**  |**Descrizione**  |
   |---------|---------|---------|
   |Alias di input| *TwitterStream* | Immettere un alias per l'input. |
   |Subscription  | \<Sottoscrizione in uso\> |  Selezionare la sottoscrizione di Azure da usare. |
   |Spazio dei nomi dell'hub eventi | *ASA-Twitter-eventhub* |
   |Nome dell'hub eventi | *socialtwitter-eh* | Scegliere *Usa esistente*. Quindi selezionare l'hub eventi creato.|
   |Tipo di compressione eventi| GZip | Tipo di compressione dei dati.|

   Lasciare i valori predefiniti rimanenti e selezionare **Salva**.

## <a name="specify-the-job-query"></a>Specificare la query del processo

Analisi di flusso supporta un semplice modello di query dichiarativa per descrivere le trasformazioni. Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di analisi dei flussi di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Questa guida dettagliata consente di creare e testare diverse query su dati di Twitter.

Per confrontare il numero di menzioni tra gli argomenti, è possibile usare una [finestra a cascata](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) per ottenere il conteggio delle menzioni per argomento ogni cinque secondi.

1. Nella **Panoramica**del processo selezionare **modifica query** in alto a destra nella casella query. Azure elenca gli input e gli output configurati per il processo e consente di creare una query per trasformare il flusso di input così come viene inviato all'output.

2. Modificare la query nell'editor di query come segue:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. I dati degli eventi dei messaggi devono essere visualizzati nella finestra di **Anteprima di input** al di sotto della query. Verificare che la **vista** sia impostata su **JSON**. Se non vengono visualizzati dati, assicurarsi che il generatore di dati invii gli eventi all'hub eventi e che sia stato selezionato **gzip** come tipo di compressione per l'input.

4. Selezionare **query di test** e notare i risultati nella finestra **risultati test** sotto la query.

5. Modificare la query nell'editor di codice nel modo seguente e selezionare **query di test**:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Questa query restituisce tutti i tweet che includono la parola chiave *Azure*.

## <a name="create-an-output-sink"></a>Creare un sink di output

A questo punto sono stati definiti un flusso di eventi, un input dell'hub eventi per inserire gli eventi e una query per eseguire una trasformazione nel flusso. L'ultimo passaggio consiste nel definire un sink di output per il processo.  

In questa guida è possibile scrivere gli eventi Tweet aggregati dalla query del processo all'archivio BLOB di Azure.  È anche possibile eseguire il push dei risultati al database SQL di Azure, ad Archiviazione tabelle di Azure, a Hub eventi o a Power BI, in base alle esigenze specifiche dell'applicazione.

## <a name="specify-the-job-output"></a>Specificare l'output del processo

1. Nella sezione **topologia processo** nel menu di spostamento a sinistra selezionare **output**. 

2. Nella pagina **output** fare clic su **+&nbsp;Aggiungi** e **archiviazione BLOB/data Lake storage Gen2**:

   * **Alias di output**: usare il nome `TwitterStream-Output`. 
   * **Opzioni di importazione**: selezionare **Seleziona archiviazione dalle sottoscrizioni**.
   * **Account di archiviazione**. Selezionare l'account di archiviazione.
   * **Contenitore**. Selezionare **Crea nuovo** e immettere `socialtwitter`.
   
4. Selezionare **Salva**.   

## <a name="start-the-job"></a>Avviare il processo

Sono stati specificati l'input del processo, la query e l'output. A questo punto si è pronti ad avviare il processo di Analisi di flusso.

1. Verificare che l'applicazione TwitterClientCore sia in esecuzione. 

2. Nella panoramica del processo selezionare **Avvia**.

3. Nella pagina **Avvia processo** , per **ora di inizio dell'output del processo**, selezionare **ora** e quindi fare clic su **Avvia**.

## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)