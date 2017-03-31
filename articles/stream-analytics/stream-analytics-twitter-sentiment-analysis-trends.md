---
title: Analisi del sentiment di Twitter in tempo reale con Analisi di flusso | Microsoft Docs
description: Imparare a utilizzare Analisi di flusso per l&quot;analisi dei sentimenti di Twitter in tempo reale. Istruzioni dettagliate, dalla generazione degli eventi fino ai dati in un dashboard in tempo reale.
keywords: analisi delle tendenze twitter in tempo reale, analisi dei sentimenti, analisi dei social media, esempio di analisi di tendenza
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/09/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 09860b34bf4b1664e8d82af0e049cfd1a2d8defa
ms.lasthandoff: 03/10/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analisi del sentiment su Twitter in tempo reale in Analisi di flusso di Azure

Informazioni su come creare una soluzione di analisi dei sentimenti per analisi dei social media portando gli eventi di Twitter in tempo reale negli hub eventi di Azure. È necessario scrivere una query di Analisi di flusso di Azure per analizzare i dati. Sarà quindi possibile archiviare i risultati per analisi successive o usare un dashboard e [Power BI](https://powerbi.com/) per visualizzare informazioni in tempo reale.

Gli strumenti di analisi di social media consentono alle organizzazioni di comprendere gli argomenti di tendenza, i soggetti significativi e le attitudini con un elevato numero di post nei social media. L'analisi dei sentimenti, detta anche *opinion mining*, usa gli strumenti di analisi dei social media per determinare le attitudini rispetto a un prodotto, un'idea e così via. L'analisi delle tendenze Twitter in tempo reale è un ottimo esempio, perché il modello di sottoscrizione hashtag consente di ascoltare parole chiave specifiche e sviluppare l'analisi del sentiment nel feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: analisi del sentiment su social media in tempo reale

Una società con sito Web di notizie è interessata a superare la concorrenza offrendo contenuti del sito immediatamente fruibili per i lettori. La società usa l'analisi dei social media su argomenti rilevanti per i lettori eseguendo l'analisi dei sentimenti in tempo reale sui dati di Twitter. In particolare, per identificare gli argomenti di tendenza in tempo reale su Twitter, la società richiede un'analisi in tempo reale sui volumi dei tweet e sulle valutazioni relative agli argomenti più importanti. Ciò che serve, in sostanza, è un motore di analisi dei sentimenti basato su questo feed di social media.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure.
* Account Twitter e [token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* Il file [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) da GitHub.
* Facoltativo: codice sorgente per client twitter da [Github](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input"></a>Creare un input dell'hub eventi

L'applicazione di esempio genera gli eventi e li invia a un'istanza dell'hub eventi, vale a dire a un hub eventi. Gli hub eventi del bus di servizio sono il metodo preferito per l'inserimento di eventi per Analisi di flusso. Per altre informazioni, fare riferimento alla sezione relativa a Hub eventi nella [Documentazione sul bus di servizio](/azure/service-bus/).

### <a name="use-the-following-steps-to-create-an-event-hub"></a>Per creare un nuovo hub eventi, seguire questa procedura.

1. Nel [Portale di Azure](https://portal.azure.com), fare clic su **NUOVO** >, digitare *Hub eventi* e quindi selezionare **Hub eventi** nei risultati di ricerca. Fare quindi clic su **Crea**.
2. Specificare un nome per l'hub eventi e creare un gruppo di risorse. Nell'esempio, vengono specificati rispettivamente `socialtwitter-eh` e `socialtwitter-rg`. Selezionare la casella per aggiungere l'account al dashboard, quindi fare clic sul pulsante **Crea**.
3. Dopo la distribuzione, fare clic sull'hub eventi e quindi su **Hub eventi** in **Entità**.
4. Fare clic sul pulsante **+ Hub eventi** per creare l'hub eventi. Indicare nuovamente il nome (nell'esempio: `socialtwitter-eh`) e fare clic su **Crea**.
5. Per concedere l'accesso all'hub eventi, è necessario creare un criterio di accesso condiviso. Fare clic sull'hub eventi e quindi su **Criteri di accesso condiviso** in **Impostazioni**.
6. In **CRITERI DI ACCESSO CONDIVISO** creare un nuovo criterio con autorizzazioni **GESTISCI** facendo clic su **+ Aggiungi**. Assegnare un nome al criterio e selezionare **GESTISCI** e quindi fare clic su **Crea**.
7. Fare clic sul nuovo criterio dopo averlo creata e quindi sul pulsante di copia per l'entità **STRINGA DI CONNESSIONE - CHIAVE PRIMARIA**. Il criterio sarà necessario più avanti nell'esercitazione. Tornare al dashboard.

![Endpoint dei criteri di accesso condiviso](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Configurazione e avvio dell'applicazione client Twitter

È stata messa a disposizione un'applicazione client che si connette ai dati di Twitter usando le [API di streaming di Twitter](https://dev.twitter.com/streaming/overview) per raccogliere gli eventi Tweet relativi a un insieme di argomenti con parametri. Lo strumento open source [Sentiment140](http://help.sentiment140.com/) viene usato per assegnare un valore di sentimento a ogni tweet.

* 0 = negativo
* 2 = neutrale
* 4 = positivo

Quindi, gli eventi Tweet vengono inviati all'hub eventi.  

### <a name="follow-these-steps-to-set-up-the-application"></a>Attenersi alla seguente procedura per configurare l'applicazione:

1. [Scaricare il file TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) e decomprimerlo.
2. Eseguire l'applicazione `TwitterWPFClient.exe` e immettere i dati per la chiave e il segreto dell'API Twitter, il token di accesso e il segreto di Twitter e le informazioni sull'hub eventi di Azure. Definire infine le parole chiave per cui determinare le valutazioni. Si noti che se si specifica più di una parola (usando le virgole per definire più valori) e si desidera conoscere le valutazioni su una QUALSIASI di queste parole, è necessario selezionare "Match ANY" (Tutte le corrispondenze).

   [Passaggi per generare un token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   È necessario eseguire un'applicazione vuota per generare un token.  

3. Sostituire i valori EventHubConnectionString ed EventHubName in TwitterWpfClient.exe.config con la stringa di connessione e il nome dell'hub eventi. La stringa di connessione copiata in precedenza contiene sia la stringa di connessione che il nome dell'hub eventi, quindi assicurarsi di separarli e di inserire ogni elemento nel campo corretto. Si consideri ad esempio la stringa di connessione seguente:  
   
   `Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub`
   
   Il file TwitterWpfClient.exe.config deve contenere le impostazioni come nell'esempio seguente:
   
   ```
     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
     add key="EventHubName" value="yourhub"
   ```
   
   È importante notare che il testo "EntityPath=" **non** viene visualizzato nel valore EventHubName.
   
   È anche possibile immettere i valori per le informazioni di connessione a Twitter e Azure direttamente nel client. Lo stesso vale se "EntityPath=" non viene usato.
   
   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

4. *Facoltativo:* modificare le parole chiave da cercare.  Per impostazione predefinita, questa applicazione cerca alcune parole chiave di giochi.  È possibile modificare i valori di **twitter_keywords** in TwitterWpfClient.exe.config, se necessario.
5. Eseguire TwitterWpfClient.exe e fare clic sul pulsante di avvio verde per raccogliere le valutazioni su social media. Gli eventi Tweet con i valori **CreatedAt**, **Topic** e **SentimentScore** vengono inviati all'hub eventi.

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

Ora che il flusso di eventi Tweet viene trasmesso in tempo reale da Twitter è possibile impostare un processo di Analisi di flusso per analizzare questi eventi in tempo reale.

### <a name="provision-a-stream-analytics-job"></a>Eseguire il provisioning di un processo di Analisi dei flussi

Nel [Portale di Azure](https://portal.azure.com/) fare clic su **NUOVO** > digitare **ANALISI DI FLUSSO** > e fare clic sul riquadro Analisi di flusso che viene visualizzato. Specificare i valori seguenti, quindi fare clic su **CREA**.

   * **NOME PROCESSO**: immettere un nome del processo.
   * **Gruppo di risorse**: selezionare il gruppo di risorse creato in precedenza in questa esercitazione nell'opzione "Use existing" (Usa gruppo esistente).
   * **ACCOUNT DI ARCHIVIAZIONE**: scegliere l'account di archiviazione di Azure da usare per archiviare i dati di monitoraggio per tutti i processi di Analisi di flusso in esecuzione all'interno dell'area. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.   

![Nuovo processo](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

Una volta creato, il processo verrà aperto nel Portale di Azure.

## <a name="specify-the-job-input"></a>Specificare l'input del processo

Nel processo di Analisi di flusso, fare clic su **INPUT** in mezzo al riquadro del processo in Topologia processo, quindi fare clic su **AGGIUNGI**. Il portale richiederà quindi alcune informazioni elencate di seguito. Generalmente saranno validi i valori predefiniti, ma vengono comunque definiti di seguito per riferimento.
  
   * **ALIAS DI INPUT**: immettere un nome descrittivo per l'input del processo, ad esempio `TwitterStream`. Il nome verrà usato in seguito nella query.
   * **NOME HUB EVENTI**: selezionare il nome dell'hub eventi.
   * **NOME CRITERIO HUB EVENTI**: selezionare il criterio dell'hub eventi creato precedentemente in questa esercitazione.

Selezionare il pulsante **Create** .

## <a name="specify-job-query"></a>Specificare una query del processo

Analisi di flusso supporta un semplice modello di query dichiarativa per descrivere le trasformazioni. Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Questa esercitazione consente di creare e testare diverse query su dati di Twitter.

Per confrontare il numero di riferimenti tra gli argomenti, verrà usata una [finestra a cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) per ottenere il conteggio dei riferimenti per argomento ogni cinque secondi.

Modificare la query nell'editor di codice immettendo il codice seguente e quindi fare clic su **Salva**:

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

In questa query viene usata la parola chiave **TIMESTAMP BY** per specificare un campo di timestamp nel payload da usare nel calcolo temporale. Se il campo non è stato specificato, l'operazione di windowing verrà eseguita usando l'ora in cui ogni evento è arrivato all'hub eventi.  Altre informazioni sono disponibili nella sezione relativa a tempo di arrivo e tempo di applicazione nelle [informazioni di riferimento sulle query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Questa query accede anche a un timestamp per la fine di ogni finestra usando la proprietà **System.Timestamp**.

![Riquadro della query](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-output-sink"></a>Creare un sink di output

Ora che è stato definito un flusso di eventi, un input dell'hub eventi per acquisire gli eventi e una query per eseguire una trasformazione nel flusso, l'ultimo passaggio consiste nel definire un sink di output per il processo.  Gli eventi Tweet aggregati provenienti dalla query del processo verranno scritti nell'archivio BLOB di Azure.  È anche possibile effettuare il push dei risultati al database SQL, all'archivio tabelle di Azure o all'hub eventi, in base alle esigenze specifiche dell'applicazione.

## <a name="specify-job-output"></a>Specificare l'output del processo

Nel processo di Analisi di flusso fare clic su **OUTPUT** in **Topologia processo** e quindi fare clic su **AGGIUNGI**. Digitare o selezionare i valori seguenti nel riquadro:
   
   * **ALIAS DI OUTPUT**: immettere un nome descrittivo per l'output del processo. In questa dimostrazione verrà usato `Output`.
   * **Sink**: selezionare Archivio BLOB.
   * **ACCOUNT DI ARCHIVIAZIONE**: selezionare "Crea un nuovo account di archiviazione".
    * **Account di ARCHIVIAZIONE**: assegnare un nome al nuovo account di archiviazione (`socialtwitter` in questo esempio)
    * **CONTENITORE**: assegnare un nome al nuovo contenitore (`socialtwitter` in questo esempio)

Per le altre voci, lasciare i valori predefiniti. Infine, fare clic su **Crea**.

## <a name="start-the-job"></a>Avviare il processo

Poiché sono stati specificati l'input, la query e l'output del processo, a questo punto è possibile avviare il processo di Analisi di flusso.

Nel riquadro della panoramica processo fare clic su **AVVIA** nella parte superiore della pagina.

Nella finestra di dialogo visualizzata selezionare **ORA DI INIZIO PROCESSO**, quindi fare clic sul pulsante con il **segno di spunta** nella parte inferiore della finestra di dialogo. Lo stato del processo diventerà **Avvio in corso** e poco dopo **In esecuzione**.

![Processo in esecuzione](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Visualizzare l'output per l'analisi dei sentimenti

Quando il processo è in esecuzione ed elabora il flusso di Twitter in tempo reale, scegliere come deve essere visualizzato l'output per l'analisi dei sentimenti. Usare uno strumento come [Esplora archivi di Microsoft Azure](https://http://storageexplorer.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) per visualizzare l'output del processo in tempo reale. A questo punto è possibile usare [Power BI](https://powerbi.com/) per estendere l'applicazione in modo da includere un dashboard personalizzato come quello riportato nella schermata che segue.

![powerbi](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="another-query-of-interest-in-this-scenario"></a>Un'altra query di interesse in questo scenario

Un'altra query di esempio creata per questo scenario e basata su una [finestra temporale scorrevole](https://msdn.microsoft.com/library/azure/dn835051.aspx). Per identificare gli argomenti di tendenza si cercheranno gli argomenti che superano un valore di soglia per i riferimenti entro un determinato periodo di tempo. Ai fini di questa esercitazione, verranno selezionati gli argomenti menzionati più di 20 volte negli ultimi cinque secondi.

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="table-of-the-field-headers"></a>Tabella delle intestazioni di campo

Le etichette dei campi che è possibile usare in questo esercizio sono elencate in questa tabella. È possibile sperimentare nell'editor di query.

Proprietà JSON | Definizione
--- | ---
CreatedAt | Orario di creazione del tweet
Argomento | Argomento che corrisponde alla parola chiave specificata
SentimentScore | Punteggio del sentiment da Sentiment140
Autore | Handle di Twitter che ha inviato il tweet
Testo | Corpo completo del tweet


## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


