---
title: Analisi del sentiment su Twitter in tempo reale con Analisi di flusso di Azure
description: Questo articolo descrive come usare Analisi di flusso per l'analisi del sentiment su Twitter in tempo reale. Istruzioni dettagliate, dalla generazione degli eventi fino ai dati in un dashboard in tempo reale.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/10/2020
ms.openlocfilehash: 0941e3d5141b5b8841f5d37e3db0d0b1b1474547
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130273"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analisi del sentiment su Twitter in tempo reale in Analisi di flusso di Azure

Questo articolo illustra come creare una soluzione di analisi del sentiment sui social media portando gli eventi di Twitter in tempo reale negli Hub eventi di Azure. Si scrive una query di Analisi di flusso di Azure per analizzare i dati e archiviare i risultati per analisi successive o creare una dashboard [Power BI](https://powerbi.com/) per rendere disponibili informazioni rilevanti in tempo reale.

Gli strumenti di analisi dei social media permettono alle organizzazioni di determinare gli argomenti di tendenza, vale a dire gli argomenti e gli atteggiamenti che registrano un numero elevato di post sui social media. L'analisi del sentiment, detta anche *opinion mining* , usa gli strumenti di analisi dei social media per determinare le attitudini rispetto a un prodotto o un'idea. 

L'analisi delle tendenze Twitter in tempo reale è un ottimo esempio di strumento di analisi, poiché il modello di sottoscrizione hashtag consente di ascoltare parole chiave specifiche (hashtag) e sviluppare l'analisi del sentiment del feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: analisi del sentiment su social media in tempo reale

Una società con un sito Web di notizie è interessata a superare la concorrenza offrendo contenuti del sito immediatamente fruibili per i lettori. La società usa l'analisi dei social media su argomenti rilevanti per i lettori eseguendo l'analisi del sentiment in tempo reale sui dati di Twitter.

Per identificare in tempo reale gli argomenti di tendenza su Twitter, la società deve eseguire un'analisi in tempo reale sul volume dei tweet e sul sentiment relativo agli argomenti più importanti.

## <a name="prerequisites"></a>Prerequisiti

In questa guida pratica si usa un'applicazione client che si connette a Twitter e cerca i tweet con determinati hashtag, che è possibile impostare. Per eseguire l'applicazione e analizzare i tweet con Analisi di flusso di Azure, è necessario quanto segue:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).

* Un [account Twitter](https://twitter.com).

* L'applicazione TwitterClientCore, che legge il feed di Twitter. Per questa applicazione, scaricare [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Installare l'[interfaccia della riga di comando di .NET Core](/dotnet/core/tools/?tabs=netcore2x) versione 2.1.0.

## <a name="create-an-event-hub-for-streaming-input"></a>Creare un hub eventi per l'input di streaming

L'applicazione di esempio genera eventi e ne esegue il push a un hub eventi di Azure. Gli Hub eventi di Azure sono la soluzione preferita per l'inserimento di eventi per Analisi di flusso. Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Creare uno spazio dei nomi dell'hub eventi e un hub eventi
In questa sezione si creerà uno spazio dei nomi dell'hub eventi e si aggiungerà un hub eventi a tale spazio. Gli spazi dei nomi degli hub eventi consentono di raggruppare in modo logico le istanze dei bus di eventi correlate. 

1. Eseguire l'accesso al portale di Azure e selezionare **Crea una risorsa** . Quindi cercare **Hub eventi** e selezionare **Crea** .

2. Nella pagina **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. È possibile usare qualsiasi nome per lo spazio dei nomi, a condizione che sia valido per un URL e univoco in Azure. 
    
3. Selezionare un piano tariffario e creare o scegliere un gruppo di risorse. Quindi, scegliere un percorso e selezionare **Crea** . 
 
4. Al termine della distribuzione, passare al gruppo di risorse e cercare lo spazio dei nomi dell'hub eventi nell'elenco delle risorse di Azure. 

5. Dal nuovo spazio dei nomi selezionare **+&nbsp;Hub eventi** . 

6. Denominare il nuovo hub eventi come *socialtwitter-eh* . È possibile usare un nome diverso. In questo caso, tenerne traccia, poiché sarà necessario in un secondo momento. Non sono richieste altre impostazioni per l'hub eventi.
 
7. Selezionare **Create** (Crea).

### <a name="grant-access-to-the-event-hub"></a>Concedere l'accesso all'hub eventi

Prima che un processo possa inviare dati a un hub eventi, l'hub necessita che siano configurati criteri che consentano l'accesso. I criteri di accesso generano una stringa di connessione che include informazioni di autorizzazione.

1.  Nella barra di spostamento sul lato sinistro dello spazio dei nomi di hub eventi, selezionare **Hub eventi** , che si trova nella sezione **Entità** . Quindi, selezionare l'hub eventi appena creato.

2.  Nella barra di spostamento sul lato sinistro selezionare **Criteri di accesso condiviso** disponibili in **Impostazioni** .

    >[!NOTE]
    >Per lo spazio dei nomi dell'hub eventi e per l'hub eventi è disponibile un'opzione di Criteri di accesso condiviso. Assicurarsi di lavorare nel contesto dell'hub eventi, non nello spazio dei nomi generale dell'hub eventi.

3.  Nella pagina dei criteri di accesso selezionare **+ Aggiungi** . Immettere quindi *socialtwitter-access* per il **Nome criterio** e selezionare la casella di controllo **Gestisci** .
 
4.  Selezionare **Create** (Crea).

5.  Dopo aver distribuito i criteri, selezionare il criterio dall'elenco dei criteri di accesso condiviso.

6.  Individuare la casella con l'etichetta **Connection string primar-key** e fare clic sul pulsante Copia accanto alla stringa di connessione.
 
7.  Incollare la stringa di connessione in un editor di testo. Sarà necessario usare questa stringa nella sezione successiva, dopo aver apportato alcune piccole modifiche.

   La stringa di connessione ha un aspetto simile al seguente:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Si noti che la stringa di connessione contiene più coppie chiave-valore, separate da punti e virgola: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` e `EntityPath`.  

   > [!NOTE]
   > Per sicurezza, le parti della stringa di connessione nell'esempio sono state rimosse.

## <a name="configure-and-start-the-twitter-client-application"></a>Configurare e avviare l'applicazione client Twitter

L'applicazione client ottiene gli eventi tweet direttamente da Twitter. A questo scopo, è necessaria l'autorizzazione per chiamare le API di streaming di Twitter. Per configurare questa autorizzazione, si crea un'applicazione in Twitter in modo da generare credenziali univoche, ad esempio un token OAuth. Si può quindi configurare l'applicazione client in modo da usare queste credenziali quando esegue chiamate API. 

### <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter
Se non si ha già un'applicazione Twitter da usare in questa guida pratica, è possibile crearne una. È necessario avere già un account Twitter.

> [!NOTE]
> La procedura esatta da seguire in Twitter per creare un'applicazione e ottenere le chiavi, i segreti e il token può cambiare. Se queste istruzioni non corrispondono alle informazioni visualizzate sul sito di Twitter, consultare la documentazione per sviluppatori di Twitter.

1. In un browser Web passare a [Twitter per sviluppatori](https://developer.twitter.com/en/apps), creare un account sviluppatore e selezionare **Create an app** (Crea un'app). Potrebbe essere visualizzato un messaggio che indica che è necessario richiedere un account per sviluppatori Twitter. Eseguire questa operazione e, dopo l'approvazione della richiesta, si dovrebbe ricevere un messaggio di posta elettronica di conferma. L'approvazione di un account di sviluppatore potrebbe richiedere diversi giorni.

   ![Screenshot che mostra il pulsante Crea un'app.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Dettagli dell'applicazione Twitter")

2. Nella pagina **Create an application** (Crea applicazione) specificare i dettagli per la nuova app e quindi selezionare **Create your Twitter application** (Crea applicazione Twitter).

   ![Screenshot mostra il riquadro dei dettagli dell'app in cui è possibile immettere i valori per l'app.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Dettagli dell'applicazione Twitter")

3. Nella pagina dell'applicazione selezionare **Keys and Tokens** (Chiavi e token) e copiare i valori di **Consumer API Key** (Chiave API consumer) e **Consumer API Secret Key** (Chiave privata API consumer). Selezionare inoltre **Create** (Crea) in **Access Token and Access Token Secret** (Token di accesso e Segreto del token di accesso) per generare i token di accesso. Copiare i valori di **Access Token** (Token di accesso) e **Access Token Secret** (Segreto del token di accesso).

   Salvare i valori recuperati per l'applicazione Twitter. È necessario usare questi valori più avanti.

> [!NOTE]
> Le chiavi e i segreti dell'applicazione Twitter consentono l'accesso all'account Twitter personale. Trattarle come dati sensibili, allo stesso modo della password di Twitter. Evitare, ad esempio, di incorporare queste informazioni in un'applicazione che si prevede di distribuire ad altri utenti. 

### <a name="configure-the-client-application"></a>Configurare l'applicazione client

È stata creata un'applicazione client che si connette ai dati di Twitter usando le [API di streaming di Twitter](https://dev.twitter.com/streaming/overview) per raccogliere gli eventi tweet relativi a un set specifico di argomenti.

Prima di essere eseguita, l'applicazione richiede all'utente determinate informazioni, ad esempio le chiavi di Twitter e la stringa di connessione all'hub eventi.

1. Verificare di aver scaricato il file dell'applicazione [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore), come indicato nei prerequisiti.

2. Usare un editor di testo per aprire il file *App.config* . Apportare le modifiche seguenti all'elemento `<appSettings>`:

   * Per `oauth_consumer_key` impostare la chiave utente di Twitter (chiave API). 
   * Per `oauth_consumer_secret` impostare il segreto utente di Twitter (chiave privata API).
   * Per `oauth_token` impostare il token di accesso di Twitter.
   * Per `oauth_token_secret` impostare il segreto del token di accesso di Twitter.
   * Per `EventHubNameConnectionString` impostare la stringa di connessione.
   * Per `EventHubName` impostare il nome dell'hub eventi, ovvero il valore del percorso dell'entità.

3. Aprire la riga di comando e passare alla directory che contiene l'app TwitterClientCore. Per compilare il progetto, usare il comando `dotnet build`. Quindi usare il comando `dotnet run` per eseguire l'app. L'app invia i tweet all'Hub eventi.

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

Ora che gli eventi tweet vengono trasmessi in flusso in tempo reale da Twitter, è possibile impostare un processo di Analisi di flusso per analizzare questi eventi in tempo reale.

1. Passare al gruppo di risorse nel portale di Azure e selezionare **+ Aggiungi** . Cercare quindi **processo analisi di flusso** e selezionare **Crea** .

2. Assegnare il nome `socialtwitter-sa-job` al processo, specificare una sottoscrizione, un gruppo di risorse e un percorso.

    È consigliabile posizionare il processo e l'hub eventi nella stessa area per ottenere prestazioni ottimali ed evitare di pagare il trasferimento dei dati tra aree.

3. Selezionare **Create** (Crea). Quindi, al termine della distribuzione, passare al processo.

## <a name="specify-the-job-input"></a>Specificare l'input del processo

1. Nel processo di Analisi di flusso selezionare **Input** dal menu a sinistra sotto a **Topologia processo** .

2. Selezionare **+&nbsp;Aggiungi input del flusso** > **Hub eventi** . Compilare il modulo del **nuovo input** con le informazioni seguenti:

   |**Impostazione**  |**Valore consigliato**  |**Descrizione**  |
   |---------|---------|---------|
   |Alias di input| *TwitterStream* | Immettere un alias per l'input. |
   |Subscription  | \<Your subscription\> |  Selezionare la sottoscrizione di Azure da usare. |
   |Spazio dei nomi dell'hub eventi | *asa-twitter-eventhub* |
   |Nome dell'hub eventi | *socialtwitter-eh* | Scegliere *Usa esistente* . Quindi, selezionare l'Hub eventi creato.|
   |Tipo di compressione eventi| GZip | Il tipo di compressione dati.|

   Lasciare i valori predefiniti restanti e selezionare **Salva** .

## <a name="specify-the-job-query"></a>Specificare la query del processo

Analisi di flusso supporta un semplice modello di query dichiarativa per descrivere le trasformazioni. Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di analisi dei flussi di Azure](/stream-analytics-query/stream-analytics-query-language-reference). Questa guida pratica permette di creare e testare diverse query su dati di Twitter.

Per confrontare il numero di menzioni tra gli argomenti, è possibile usare una [finestra a cascata](/stream-analytics-query/tumbling-window-azure-stream-analytics) per ottenere il conteggio delle menzioni per argomento ogni cinque secondi.

1. Nel processo **Panoramica** selezionare **Modifica query** accanto all'angolo in alto a destra della casella Query. Azure elenca gli input e gli output configurati per il processo e consente di creare una query per trasformare il flusso di input nel momento in cui viene inviato all'output.

2. Modificare la query nell'editor di query nel modo seguente:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. I dati eventi dei messaggi devono essere visualizzati nella finestra di **Anteprima input** sotto la query. Assicurarsi che la **Vista** sia impostata su **JSON** . Se non vengono visualizzati dati, assicurarsi che il generatore di dati invii gli eventi all'hub eventi e che sia stato selezionato **GZip** come tipo di compressione per l'input.

4. Selezionare **Esegui test della query** osservare i risultati nella finestra **Risultati test** sotto la query.

5. Modificare come indicato di seguito la query nell'editor di codice e scegliere **Esegui test della query** :

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Questa query restituisce tutti i tweet che includono la parola chiave *Azure* .

## <a name="create-an-output-sink"></a>Creare un sink di output

A questo punto sono stati definiti un flusso di eventi, un input dell'hub eventi per inserire gli eventi e una query per eseguire una trasformazione nel flusso. L'ultimo passaggio consiste nel definire un sink di output per il processo.  

In questa guida pratica gli eventi tweet aggregati ottenuti dalla query del processo vengono scritti nell'archiviazione BLOB di Azure.  È anche possibile eseguire il push dei risultati al database SQL di Azure, ad Archiviazione tabelle di Azure, a Hub eventi o a Power BI, in base alle esigenze specifiche dell'applicazione.

## <a name="specify-the-job-output"></a>Specificare l'output del processo

1. Nella sezione **Topologia processo** nel menu di spostamento a sinistra selezionare **Output** . 

2. Nella pagina **Output** , fare clic su **+&nbsp;Aggiungi** e **Archiviazione BLOB/Data Lake Storage Gen2** :

   * **Alias di output** : Usare il nome `TwitterStream-Output`. 
   * **Opzioni di importazione** : Selezionare **Selezionare l'archiviazione dalle sottoscrizioni correnti** .
   * **Account di archiviazione** . Selezionare l'account di archiviazione.
   * **Contenitore** . Selezionare **Crea nuovo** e immettere `socialtwitter`.
   
4. Selezionare **Salva** .   

## <a name="start-the-job"></a>Avviare il processo

Sono stati specificati l'input del processo, la query e l'output. A questo punto si è pronti ad avviare il processo di Analisi di flusso.

1. Verificare che l'applicazione TwitterClientCore sia in esecuzione. 

2. Nella panoramica del processo selezionare **Avvia** .

3. Nella pagina **Avvia processo** , per **Ora di inizio dell'output del processo** , selezionare **Ora** e quindi selezionare **Avvia** .

## <a name="get-support"></a>Supporto
Per maggiore supporto, provare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](/rest/api/streamanalytics/)