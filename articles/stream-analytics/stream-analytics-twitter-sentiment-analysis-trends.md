---
title: Analisi del sentiment di Twitter in tempo reale con Analisi di flusso di Azure | Microsoft Docs
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
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: d4fae06cb240c1687b059ae991b8d09f94e2ffc3
ms.lasthandoff: 05/01/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analisi del sentiment su Twitter in tempo reale in Analisi di flusso di Azure

Informazioni su come creare una soluzione di analisi dei sentimenti per analisi dei social media portando gli eventi di Twitter in tempo reale negli hub eventi di Azure. In questo scenario si scrive una query di Analisi di flusso di Azure per analizzare i dati. Sarà quindi possibile archiviare i risultati per usarli in seguito o usare un dashboard e [Power BI](https://powerbi.com/) per visualizzare informazioni in tempo reale.

Gli strumenti di analisi dei social media permettono alle organizzazioni di determinare gli argomenti di tendenza, vale a dire gli argomenti e gli atteggiamenti che registrano un numero elevato di post nei social media. L'analisi dei sentimenti, detta anche *opinion mining*, usa gli strumenti di analisi dei social media per determinare le attitudini rispetto a un prodotto, un'idea e così via.

L'analisi delle tendenze Twitter in tempo reale è un ottimo esempio di strumento di analisi, perché il modello di sottoscrizione hashtag consente di ascoltare parole chiave specifiche e sviluppare l'analisi del sentiment nel feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: analisi del sentiment su social media in tempo reale

Una società con un sito Web di notizie è interessata a superare la concorrenza offrendo contenuti del sito immediatamente fruibili per i lettori. La società usa l'analisi dei social media su argomenti rilevanti per i lettori eseguendo l'analisi del sentiment in tempo reale sui dati di Twitter.

In particolare, per identificare gli argomenti di tendenza in tempo reale su Twitter, la società richiede un'analisi in tempo reale sui volumi dei tweet e sui sentimenti relativi agli argomenti più importanti. In altre parole, ciò che serve è un motore di analisi del sentiment basato su questo feed di social media.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure.
* Un account Twitter e [token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens).
* Il file [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) da GitHub.
* Facoltativo: codice sorgente per client Twitter da [GitHub](https://aka.ms/azure-stream-analytics-twitterclient).

## <a name="create-an-event-hub-input"></a>Creare un input dell'hub eventi

L'applicazione di esempio genera eventi e ne effettua il push a un'istanza di Hub eventi, vale a dire a un hub eventi. Gli hub eventi del bus di servizio di Azure sono il metodo preferito per l'inserimento di eventi per Analisi di flusso. Per altre informazioni, vedere la sezione relativa a Hub eventi nella [Documentazione sul bus di servizio](/azure/service-bus/).

### <a name="create-an-event-hub"></a>Creare un hub eventi

Per creare un hub eventi, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **NUOVO**, digitare **Hub eventi** e quindi selezionare **Hub eventi** nei risultati della ricerca. Selezionare quindi **Crea**.

2. Specificare un nome per l'hub eventi e creare un gruppo di risorse. Nell'esempio vengono specificati rispettivamente `socialtwitter-eh` e `socialtwitter-rg`. Selezionare l'opzione di aggiunta dell'account al dashboard e quindi fare clic sul pulsante **Crea**.

3. Al termine della distribuzione selezionare l'hub eventi. Quindi, in **Entità**selezionare **Hub eventi**.

4. Fare clic sul pulsante **+ Hub eventi** per creare l'hub eventi. Specificare nuovamente il nome, nell'esempio `socialtwitter-eh`, quindi scegliere **Crea**.

5. Per concedere l'accesso all'hub eventi, è necessario creare un criterio di accesso condiviso. Selezionare l'hub eventi e quindi scegliere **Criteri di accesso condiviso** in **Impostazioni**.

6. In **Criteri di accesso condiviso** creare criteri con autorizzazioni **GESTISCI** facendo clic su **+ Aggiungi**. Assegnare un nome ai criteri, selezionare **GESTISCI** e quindi scegliere **Crea**.

7. Selezionare i nuovi criteri appena creati e quindi fare clic sul pulsante di copia per l'entità **STRINGA DI CONNESSIONE - CHIAVE PRIMARIA**. Servirà più avanti nell'esercitazione. Tornare al dashboard.

![Endpoint dei criteri di accesso condiviso](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Configurare e avviare l'applicazione client Twitter

È stata messa creata un'applicazione client che si connette ai dati di Twitter usando le [API di streaming di Twitter](https://dev.twitter.com/streaming/overview) per raccogliere gli eventi tweet relativi a un set di argomenti con parametri. Lo strumento open source [Sentiment140](http://help.sentiment140.com/) permette di assegnare un valore di sentiment a ogni tweet, come illustrato di seguito:

* 0 = negativo
* 2 = neutrale
* 4 = positivo

Quindi, viene effettuato il push degli eventi tweet all'hub eventi.  

### <a name="set-up-the-application"></a>Configurare l'applicazione
 Attenersi alla seguente procedura per configurare l'applicazione:

1. [Scaricare il file TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) e decomprimerlo.

2. Eseguire l'applicazione `TwitterWPFClient.exe` e immettere i dati per il segreto e la chiave API di Twitter, il segreto e il token di accesso di Twitter e le informazioni sull'hub eventi. Definire infine le parole chiave per cui determinare il sentiment.

### <a name="generate-an-oauth-access-token"></a>Generare un token di accesso OAuth
Per altre informazioni, vedere [Passaggi per generare un token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens).  

 Per generare un token è necessario eseguire un'applicazione vuota.  

1. Sostituire i valori EventHubConnectionString ed EventHubName in TwitterWpfClient.exe.config con la stringa di connessione e il nome dell'hub eventi. La stringa di connessione copiata in precedenza contiene sia la stringa di connessione che il nome dell'hub eventi. Assicurarsi di separarli e di inserire ogni elemento nel campo corretto. Si consideri ad esempio la stringa di connessione seguente:

 ```
    Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub
 ```  

   Il file TwitterWpfClient.exe.config deve contenere le impostazioni mostrate nell'esempio seguente:

 ```
      add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
      add key="EventHubName" value="yourhub"
   ```
   > [!NOTE]
   > Il testo "EntityPath=" **non** viene visualizzato nel valore EventHubName.

    È anche possibile immettere i valori per le informazioni di connessione a Twitter e Azure direttamente nel client. Lo stesso vale se "EntityPath=" non viene usato.

   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

2. **Facoltativo:** modificare le parole chiave da cercare. Per impostazione predefinita, questa applicazione cerca alcune parole chiave di gioco.  È possibile modificare i valori di **twitter_keywords** in TwitterWpfClient.exe.config, se necessario.

3. Eseguire TwitterWpfClient.exe. Quindi, selezionare il pulsante di avvio verde per raccogliere il sentiment sul social media. Gli eventi tweet con i valori **CreatedAt**, **Topic** e **SentimentScore** vengono inviati all'hub eventi.

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

Ora che il flusso di eventi Tweet viene trasmesso in tempo reale da Twitter è possibile impostare un processo di Analisi di flusso per analizzare questi eventi in tempo reale.

### <a name="provision-a-stream-analytics-job"></a>Eseguire il provisioning di un processo di Analisi dei flussi

Per effettuare il provisioning di un processo di Analisi di flusso, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/) selezionare **NUOVO**, digitare **ANALISI DI FLUSSO** e quindi selezionare il riquadro Analisi di flusso visualizzato.

2. Specificare i valori seguenti e quindi scegliere **CREA**.

   * **NOME PROCESSO**: immettere un nome del processo.

   * **Gruppo di risorse**: selezionare il gruppo di risorse creato in precedenza in questa esercitazione usando l'opzione "Use existing" (Usa esistente).

   * **ACCOUNT DI ARCHIVIAZIONE**: scegliere l'account di archiviazione di Azure da usare per archiviare i dati di monitoraggio per tutti i processi di Analisi di flusso in esecuzione all'interno dell'area. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.   

![Nuovo processo](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

Il processo appena creato viene aperto nel portale di Azure.

## <a name="specify-the-job-input"></a>Specificare l'input del processo
Per specificare l'input del processo, seguire questa procedura:

1. Nel processo di Analisi di flusso selezionare **INPUT** in **Topologia processo** al centro del riquadro del processo, quindi fare clic su **AGGIUNGI**.

    Successivamente, il portale richiede alcune delle informazioni seguenti. Nella maggior parte dei casi si possono usare i valori predefiniti, indicati di seguito:

   * **ALIAS DI INPUT**: immettere un nome descrittivo per l'input del processo, ad esempio `TwitterStream`. Usare questo nome nella query in un secondo momento.  

   * **NOME HUB EVENTI**: selezionare il nome dell'hub eventi.

   * **NOME CRITERIO HUB EVENTI**: selezionare il criterio dell'hub eventi creato precedentemente in questa esercitazione.

2. Selezionare il pulsante **Crea**.

## <a name="specify-the-job-query"></a>Specificare la query del processo

Analisi di flusso supporta un semplice modello di query dichiarativa per descrivere le trasformazioni. Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Questa esercitazione permette di creare e testare diverse query su dati di Twitter.

Per confrontare il numero di riferimenti tra gli argomenti, viene usata una [finestra a cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) per ottenere il conteggio dei riferimenti per argomento ogni cinque secondi.

Modificare come indicato di seguito la query nell'editor di codice e quindi scegliere **Salva**:

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

In questa query viene usata la parola chiave **TIMESTAMP BY** per specificare un campo di timestamp nel payload da usare nel calcolo temporale. Se il campo non è stato specificato, l'operazione di windowing viene eseguita usando l'ora in cui ogni evento è arrivato all'hub eventi. Altre informazioni sono disponibili nella sezione relativa a tempo di arrivo e tempo di applicazione nelle [informazioni di riferimento sulle query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Questa query accede anche a un timestamp per la fine di ogni finestra usando la proprietà **System.Timestamp**.

![Riquadro della query](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-an-output-sink"></a>Creare un sink di output

Ora che è stato definito un flusso di eventi, un input dell'hub eventi per acquisire gli eventi e una query per eseguire una trasformazione nel flusso, l'ultimo passaggio consiste nel definire un sink di output per il processo.  

Gli eventi tweet aggregati provenienti dalla query del processo vengono scritti nell'archivio BLOB di Azure.  È anche possibile effettuare il push dei risultati al database SQL di Azure, all'archivio tabelle di Azure oppure a Hub eventi, in base alle esigenze specifiche dell'applicazione.

## <a name="specify-the-job-output"></a>Specificare l'output del processo
Per specificare l'output del processo, seguire questa procedura:

1. Nel processo di Analisi di flusso selezionare **OUTPUT** in **Topologia processo** e quindi scegliere **AGGIUNGI**.

2. Digitare o selezionare i valori seguenti nel riquadro:

   * **ALIAS DI OUTPUT**: immettere un nome descrittivo per l'output del processo. In questa dimostrazione viene usato `Output`.

   * **Sink**: selezionare **Archivio BLOB**.

   * **ACCOUNT DI ARCHIVIAZIONE**: selezionare **Crea un nuovo account di archiviazione**.

    * **ACCOUNT DI ARCHIVIAZIONE**: assegnare un nome al nuovo account di archiviazione, in questo esempio `socialtwitter`.

    * **CONTENITORE**: assegnare un nome al nuovo contenitore, in questo esempio `socialtwitter`.

3. Per le altre voci, lasciare i valori predefiniti. Infine, selezionare **Crea**.

## <a name="start-the-job"></a>Avviare il processo

Poiché sono stati specificati l'input, la query e l'output del processo, a questo punto è possibile avviare il processo di Analisi di flusso.

Per avviare il processo, seguire questa procedura:

1. Nel riquadro della panoramica del processo nella parte superiore della pagina fare clic su **AVVIA**.

2. Nella finestra di dialogo visualizzata selezionare **ORA DI INIZIO DEL PROCESSO**, quindi fare clic sul pulsante con il **segno di spunta** nella parte inferiore della finestra di dialogo. Lo stato del processo passa prima ad **Avvio in corso** e quindi a **In esecuzione**.

![Processo in esecuzione](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Visualizzare l'output per l'analisi dei sentimenti

Quando il processo è in esecuzione ed elabora il flusso di Twitter in tempo reale, è possibile scegliere come visualizzare l'output per l'analisi del sentiment.

Usare uno strumento come [Esplora archivi di Microsoft Azure](https://http://storageexplorer.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) per visualizzare l'output del processo in tempo reale. A questo punto è possibile usare [Power BI](https://powerbi.com/) per estendere l'applicazione in modo da includere un dashboard personalizzato come quello riportato nello screenshot seguente.

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="create-another-query-to-identify-trending-topics"></a>Creare un'altra query per identificare gli argomenti di tendenza

Un'altra query di esempio creata per questo scenario si basa su una [finestra temporale scorrevole](https://msdn.microsoft.com/library/azure/dn835051.aspx). Per identificare gli argomenti di tendenza si cercano gli argomenti che superano un valore di soglia per i riferimenti entro un determinato periodo di tempo.

Ai fini di questa esercitazione, verranno selezionati gli argomenti menzionati più di 20 volte negli ultimi cinque secondi.

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="use-field-headers"></a>Usare le intestazioni dei campi

La tabella seguente contiene un elenco delle etichette dei campi che è possibile usare in questa esercitazione. È possibile sperimentare nell'editor di query.

Proprietà JSON | Definizione
--- | ---
CreatedAt | Orario di creazione del tweet
Argomento | Argomento che corrisponde alla parola chiave specificata
SentimentScore | Punteggio del sentiment da Sentiment140
Autore | Handle di Twitter che ha inviato il tweet
Text | Corpo completo del tweet


## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

