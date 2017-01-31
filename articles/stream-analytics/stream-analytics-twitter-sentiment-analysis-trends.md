---
title: Analisi del sentiment di Twitter in tempo reale con Analisi di flusso | Documentazione Microsoft
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
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3c97604b17636f011ddb2acda40fbc77afeab590
ms.openlocfilehash: 9f7e9008f29b2b1a3a0422133e15871c4ce7cca8


---
# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analisi dei social media: analisi dei sentimenti di Twitter in tempo reale in Analisi di flusso
Informazioni su come creare una soluzione di analisi dei sentimenti per analisi dei social media portando gli eventi di Twitter in tempo reale negli hub eventi di Azure. È necessario scrivere una query di Analisi di flusso di Azure per analizzare i dati. Sarà quindi possibile archiviare i risultati per analisi successive o usare un dashboard e [Power BI](https://powerbi.com/) per visualizzare informazioni in tempo reale.

Gli strumenti di analisi di social media consentono alle organizzazioni di comprendere gli argomenti di tendenza, i soggetti significativi e le attitudini con un elevato numero di post nei social media. L'analisi dei sentimenti, detta anche *opinion mining*, usa gli strumenti di analisi dei social media per determinare le attitudini rispetto a un prodotto, un'idea e così via. L'analisi delle tendenze Twitter in tempo reale è un ottimo esempio, perché il modello di sottoscrizione hashtag consente di ascoltare parole chiave specifiche e sviluppare l'analisi dei sentimenti nel feed.

## <a name="scenario-sentiment-analysis-in-real-time"></a>Scenario: analisi dei sentimenti in tempo reale
Una società con sito Web di notizie è interessata a superare la concorrenza offrendo contenuti del sito immediatamente fruibili per i lettori. La società usa l'analisi dei social media su argomenti rilevanti per i lettori eseguendo l'analisi dei sentimenti in tempo reale sui dati di Twitter. In particolare, per identificare gli argomenti di tendenza in tempo reale su Twitter, la società richiede un'analisi in tempo reale sui volumi dei tweet e sui sentimenti relativi agli argomenti più importanti. Ciò che serve, in sostanza, è un motore di analisi dei sentimenti basato su questo feed di social media.

## <a name="prerequisites"></a>Prerequisiti
* Account Twitter e [token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) dall'Area download Microsoft
* Facoltativo: codice sorgente per client twitter da [Github](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Creare un input dell'hub eventi e un gruppo di consumer
L'applicazione di esempio genererà gli eventi e li invierà a un'istanza di Hub eventi, vale a dire a un hub eventi. Gli hub eventi del bus di servizio sono il metodo preferito per l'inserimento di eventi per Analisi di flusso. Vedere la documentazione relativa agli hub eventi nella [Documentazione sul bus di servizio](/azure/service-bus/).

Per creare un nuovo hub eventi, seguire questa procedura.

1. Nel portale di Azure fare clic su **NUOVO** > **SERVIZI APP** > **BUS DI SERVIZIO** > **HUB EVENTI** > **CREAZIONE RAPIDA** e inserire un nome, un'area e uno spazio dei nomi nuovo o esistente.  
2. Come procedura consigliata, ogni processo di Analisi di flusso deve essere letto da un singolo gruppo di consumer di Hub eventi. Il processo di creazione di un gruppo di consumer verrà illustrato in un secondo momento. Altre informazioni sui gruppi di consumer sono disponibili in [Panoramica di Hub eventi di Azure](https://msdn.microsoft.com/library/azure/dn836025.aspx). Per creare un gruppo di consumer, passare all'hub eventi appena creato, scegliere la scheda **GRUPPI DI CONSUMER**, fare clic su **CREA** nella parte inferiore della pagina, quindi fornire un nome per il gruppo di consumer.
3. Per concedere l'accesso all'hub eventi, è necessario creare un criterio di accesso condiviso. Fare clic sulla scheda **CONFIGURA** dell'hub eventi.
4. In **CRITERI DI ACCESSO CONDIVISI** creare un nuovo criterio con autorizzazioni di **gestione**.

   ![Criteri di accesso condivisi in cui è possibile creare un nuovo criterio con autorizzazioni di gestione.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)
5. Fare clic su **SAVE** nella parte inferiore della pagina.
6. Passare al **DASHBOARD**, fare clic su **INFORMAZIONI DI CONNESSIONE** nella parte inferiore della pagina e quindi copiare e salvare le informazioni di connessione. Utilizzare l'icona di copia visualizzata sotto l'icona di ricerca.

## <a name="configure-and-start-the-twitter-client-application"></a>Configurazione e avvio dell'applicazione client Twitter
È stata messa a disposizione un'applicazione client che si connette ai dati di Twitter usando le [API di streaming di Twitter](https://dev.twitter.com/streaming/overview) per raccogliere gli eventi Tweet relativi a un insieme di argomenti con parametri. Lo strumento open source [Sentiment140](http://help.sentiment140.com/) viene usato per assegnare un valore di sentimento a ogni tweet.

* 0 = negativo
* 2 = neutrale
* 4 = positivo

Quindi, gli eventi Tweet vengono inviati all'hub eventi.  

Attenersi alla seguente procedura per configurare l'applicazione:

1. [Scaricare la soluzione TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2. Aprire TwitterClient.exe.config e sostituire oauth_consumer_key, oauth_consumer_secret, oauth_token e oauth_token_secret con i token di Twitter con i propri valori.  

   [Passaggi per generare un token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   Si noti che è necessario eseguire un'applicazione vuota per generare un token.  
3. Sostituire i valori EventHubConnectionString ed EventHubName in TwitterClient.exe.config con la stringa di connessione e il nome dell'hub eventi. La stringa di connessione copiata in precedenza contiene sia la stringa di connessione che il nome dell'hub eventi, quindi assicurarsi di separarli e di inserire ogni elemento nel campo corretto. Si consideri ad esempio la stringa di connessione seguente:

     Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

   Il file TwitterClient.exe.config deve contenere le impostazioni come nell'esempio seguente:

     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"   add key="EventHubName" value="yourhub"

   È importante notare che il testo "EntityPath=" **non** viene visualizzato nel valore EventHubName.
4. *Facoltativo:* modificare le parole chiave da cercare.  Per impostazione predefinita, questa applicazione cerca le parole chiave "Azure, Skype, XBox, Microsoft, Seattle".  È possibile modificare i valori di **twitter_keywords** in TwitterClient.exe.config, se necessario.
5. Eseguire TwitterClient.exe per avviare l'applicazione. Gli eventi Tweet con i valori **CreatedAt**, **Topic** e **SentimentScore** vengono inviati all'hub eventi.

   ![Analisi dei sentimenti: valori SentimentScore inviati a un hub eventi.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.
Ora che il flusso di eventi Tweet viene trasmesso in tempo reale da Twitter è possibile impostare un processo di Analisi di flusso per analizzare questi eventi in tempo reale.

### <a name="provision-a-stream-analytics-job"></a>Eseguire il provisioning di un processo di Analisi dei flussi
1. Nel [portale di Azure](https://manage.windowsazure.com/) fare clic su **NUOVO** > **SERVIZI DATI** > **ANALISI DI FLUSSO** > **CREAZIONE RAPIDA**.
2. Specificare i valori seguenti, quindi fare clic su **CREA PROCESSO DI ANALISI DEI FLUSSI**:

   * **NOME PROCESSO**: immettere un nome del processo.
   * **AREA**: scegliere l'area in cui si desidera eseguire il processo. È consigliabile inserire il processo e l'hub eventi nella stessa area per garantire prestazioni migliori ed evitare addebiti connessi al trasferimento di dati tra aree diverse.
   * **ACCOUNT DI ARCHIVIAZIONE**: scegliere l'account di archiviazione di Azure da usare per archiviare i dati di monitoraggio per tutti i processi di Analisi di flusso in esecuzione all'interno dell'area. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.
3. Fare clic su **ANALISI DEI FLUSSI** nel riquadro sinistro per visualizzare un elenco dei processi di Analisi dei flussi.  
   ![Icona di servizio Analisi di flusso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

   Il nuovo processo verrà visualizzato nell'elenco con uno stato **CREATO**. Si noti che il pulsante **AVVIA** nella parte inferiore della pagina è disabilitato. Prima di poter avviare il processo, è necessario configurare l'input, l'output, la query per l'esecuzione.

### <a name="specify-job-input"></a>Specificare l'input del processo
1. Nel processo di Analisi di flusso fare clic su **INPUT** nella parte superiore della pagina, quindi scegliere **AGGIUNGI INPUT**. La finestra di dialogo visualizzata presenterà una serie di passaggi per l'impostazione dell'input.
2. Fare clic su **FLUSSO DATI** e quindi sul pulsante a destra.
3. Fare clic su **HUB EVENTI** e quindi sul pulsante a destra.
4. Digitare o selezionare i valori seguenti nella terza pagina:

   * **ALIAS DI INPUT**: immettere un nome descrittivo per il processo di input, ad esempio *TwitterStream*. Si noti che il nome verrà usato nella query in un secondo momento.
     **HUB EVENTI**: se l'hub eventi creato si trova nella stessa sottoscrizione del processo di Analisi di flusso, selezionare lo spazio dei nomi in cui si trova l'hub eventi.

     Se l'hub eventi si trova in un'altra sottoscrizione, scegliere **Usa hub eventi da un'altra sottoscrizione**, quindi immettere manualmente i valori per **SPAZIO DEI NOMI DEL BUS DI SERVIZIO**, **NOME HUB EVENTI**, **NOME CRITERIO HUB EVENTI**, **CHIAVE CRITERIO HUB EVENTI** e **CONTEGGIO PARTIZIONI HUB EVENTI**.
   * **NOME HUB EVENTI**: selezionare il nome dell'hub eventi.
   * **NOME CRITERIO HUB EVENTI**: selezionare il criterio dell'hub eventi creato precedentemente in questa esercitazione.
   * **GRUPPO DI CONSUMER DELL'HUB EVENTI**: digitare il nome del gruppo di consumer creato in precedenza in questa esercitazione.
5. Fare clic sul pulsante a destra.
6. Specificare i valori seguenti:

   * **FORMATO SERIALIZZATORE EVENTI**: JSON
   * **CODIFICA**: UTF8
7. Fare clic sul pulsante con il **segno di spunta** per aggiungere l'origine e per verificare che Analisi dei flussi sia in grado di connettersi all'hub eventi.

### <a name="specify-job-query"></a>Specificare una query del processo
Analisi di flusso supporta un semplice modello di query dichiarativa per descrivere le trasformazioni. Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Questa esercitazione consente di creare e testare diverse query su dati di Twitter.

#### <a name="sample-data-input"></a>Input dati di esempio
Per convalidare la query in base al tipo di dati effettivo del processo, è possibile usare la funzionalità **DATI DI ESEMPIO** per estrarre gli eventi dal flusso di e creare un file JSON degli eventi per il test.

1. Selezionare l'input dell'hub eventi e fare clic su **DATI DI ESEMPIO** nella parte inferiore della pagina.
2. Nella finestra di dialogo visualizzata specificare un valore per **ORA DI INIZIO** per iniziare a raccogliere i dati e un valore per **DURATA** per indicare quanti dati aggiuntivi verranno usati.
3. Fare clic sul pulsante **DETTAGLI**, quindi sul collegamento**Fare clic qui** per scaricare e salvare il file JSON generato.

#### <a name="pass-through-query"></a>Query pass-through
Per iniziare, si farà una semplice query pass-through che proietti tutti i campi in un evento.

1. Fare clic su **QUERY** nella parte superiore della pagina del processo di Analisi di flusso.
2. Nell'editor di codice sostituire il modello di query iniziale con le operazioni seguenti:

     SELECT * FROM TwitterStream

   Verificare che il nome dell'origine di input corrisponda al nome dell'input specificato in precedenza.
3. Fare clic su **Test** nell'editor di query.
4. Passare al file JSON di esempio.
5. Fare clic sul pulsante con il **segno di spunta** e verificare i risultati sotto la definizione della query.

   ![Risultati visualizzati sotto la definizione della query](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>Numero di tweet per argomento: finestra a cascata con aggregazione
Per confrontare il numero di riferimenti tra gli argomenti, verrà usata una [finestra a cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) per ottenere il conteggio dei riferimenti per argomento ogni cinque secondi.

1. Modificare la query nell'editor di codice nel modo seguente:

     SELECT System.Timestamp as Time, Topic, COUNT(*)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic

   In questa query viene usata la parola chiave **TIMESTAMP BY** per specificare un campo di timestamp nel payload da usare nel calcolo temporale. Se il campo non è stato specificato, l'operazione di windowing verrà eseguita usando l'ora in cui ogni evento è arrivato all'hub eventi.  Altre informazioni sono disponibili nella sezione relativa a tempo di arrivo e tempo di applicazione nelle [informazioni di riferimento sulle query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx).

   Questa query accede anche a un timestamp per la fine di ogni finestra usando la proprietà **System.Timestamp**.
2. Fare clic su **Riesegui** nell'editor di query per visualizzare i risultati della query.

#### <a name="identify-trending-topics-sliding-window"></a>Identificare gli argomenti di tendenza: finestra scorrevole
Per identificare gli argomenti di tendenza si cercheranno gli argomenti che superano un valore di soglia per i riferimenti entro un determinato periodo di tempo. Ai fini di questa esercitazione, verranno selezionati gli argomenti menzionati più di 20 volte negli ultimi cinque secondi usando una [finestra scorrevole](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1. Nell'editor del codice modificare la query come segue: SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY SLIDINGWINDOW(s, 5), topic   HAVING COUNT(*) > 20
2. Fare clic su **Riesegui** nell'editor di query per visualizzare i risultati della query.

   ![Output della query della finestra scorrevole](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>Conteggio dei riferimenti e sentimenti: finestra a cascata con aggregazione
La query finale che verrà testata usa una **finestra a cascata** per ottenere il numero di riferimenti, media, minimo, massimo e deviazione standard del punteggio dei sentimenti per ogni argomento ogni cinque secondi.

1. Modificare la query nell'editor di codice nel modo seguente:

     SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),   Max(SentimentScore), STDEV(SentimentScore)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic
2. Fare clic su **Riesegui** nell'editor di query per visualizzare i risultati della query.
3. Questa è la query che verrà usata per il nostro dashboard.  Fare clic su **SAVE** nella parte inferiore della pagina.

## <a name="create-output-sink"></a>Creare un sink di output
Ora che è stato definito un flusso di eventi, un input dell'hub eventi per acquisire gli eventi e una query per eseguire una trasformazione nel flusso, l'ultimo passaggio consiste nel definire un sink di output per il processo.  Gli eventi Tweet aggregati provenienti dalla query del processo verranno scritti nell'archivio BLOB di Azure.  È anche possibile effettuare il push dei risultati al database SQL, all'archivio tabelle di Azure o all'hub eventi, in base alle esigenze specifiche dell'applicazione.

Se necessario, attenersi alla procedura seguente per creare un contenitore per l'archivio BLOB:

1. Usare un account di archiviazione esistente o crearne uno nuovo facendo clic su **NUOVO** > **SERVIZI DATI** > **ARCHIVIAZIONE** > **CREAZIONE RAPIDA** e seguire le istruzioni sullo schermo.
2. Selezionare l'account di archiviazione, fare clic su **CONTENITORI** nella parte superiore della pagina e quindi fare clic su **AGGIUNGI**.
3. Specificare un **NOME** per il contenitore e impostare il relativo **ACCESSO** su **BLOB pubblico**.

## <a name="specify-job-output"></a>Specificare l'output del processo
1. Nel processo di Analisi di flusso fare clic su **OUTPUT** nella parte superiore della pagina, quindi scegliere **AGGIUNGI OUTPUT**. La finestra di dialogo visualizzata presenterà alcuni passaggi per l'impostazione dell'output.
2. Fare clic su **ARCHIVIAZIONE BLOB**, quindi sul pulsante a destra.
3. Digitare o selezionare i valori seguenti nella terza pagina:

   * **ALIAS DI OUTPUT**: immettere un nome descrittivo per l'output del processo.
   * **SOTTOSCRIZIONE**: se l'archivio BLOB creato si trova nella stessa sottoscrizione del processo di Analisi di flusso, scegliere **Usare l'account di archiviazione dalla sottoscrizione corrente**. Se il dispositivo di archiviazione è in una sottoscrizione diversa, fare clic su **Usare l'account di archiviazione dalla sottoscrizione corrente** e immettere manualmente le informazioni relative ad **ACCOUNT DI ARCHIVIAZIONE**, **CHIAVE ACCOUNT DI ARCHIVIAZIONE** e **CONTENITORE**.
   * **ACCOUNT DI ARCHIVIAZIONE**: selezionare il nome dell’account di archiviazione.
   * **CONTENITORE**: selezionare il nome del contenitore.
   * **PREFISSO NOME FILE**: digitare un prefisso di file da usare per la scrittura dell'output del BLOB.
4. Fare clic sul pulsante a destra.
5. Specificare i valori seguenti:
   * **FORMATO SERIALIZZATORE EVENTI**: JSON
   * **CODIFICA**: UTF8
6. Fare clic sul pulsante con il **segno di spunta** per aggiungere l'origine e per verificare che Analisi di flusso sia in grado di connettersi all'account di archiviazione.

## <a name="start-job"></a>Avviare il processo
Poiché sono stati specificati l'input, la query e l'output del processo, a questo punto è possibile avviare il processo di Analisi di flusso.

1. Dal **DASHBOARD** del processo fare clic su **AVVIA** nella parte inferiore della pagina.
2. Nella finestra di dialogo visualizzata selezionare **ORA DI INIZIO PROCESSO**, quindi fare clic sul pulsante con il **segno di spunta** nella parte inferiore della finestra di dialogo. Lo stato del processo diventerà **Avvio in corso** e poco dopo **In esecuzione**.

## <a name="view-output-for-sentiment-analysis"></a>Visualizzare l'output per l'analisi dei sentimenti
Quando il processo è in esecuzione ed elabora il flusso di Twitter in tempo reale, scegliere come deve essere visualizzato l'output per l'analisi dei sentimenti. Usare uno strumento come [Esplora archivi di Microsoft Azure](https://azurestorageexplorer.codeplex.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) per visualizzare l'output del processo in tempo reale. A questo punto è possibile usare [Power BI](https://powerbi.com/) per estendere l'applicazione in modo da includere un dashboard personalizzato come quello riportato nella schermata che segue.

![Analisi di social media: output dell’analisi dei sentimenti (opinion mining) di Analisi di flusso in un dashboard di Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


