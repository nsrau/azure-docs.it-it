<properties
	pageTitle="Analisi dei sentimenti di Twitter in tempo reale con Analisi di flusso | Microsoft Azure"
	description="Imparare a utilizzare Analisi di flusso per l'analisi dei sentimenti di Twitter in tempo reale. Istruzioni dettagliate, dalla generazione degli eventi fino ai dati in un dashboard in tempo reale."
	keywords="analisi delle tendenze twitter in tempo reale, analisi dei sentimenti, analisi dei social media, esempio di analisi di tendenza"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="09/26/2016"
	ms.author="jeffstok"/>


# Analisi dei social media: analisi dei sentimenti di Twitter in tempo reale in Analisi di flusso

Informazioni su come creare una soluzione di analisi dei sentimenti per analisi dei social media portando gli eventi di Twitter in tempo reale negli hub eventi. Si scriverà una query di Analisi di flusso per analizzare i dati e quindi archiviare i risultati per analisi successive, oppure usare un dashboard per rendere disponibili le informazioni in tempo reale usando [Power BI](https://powerbi.com/).

Gli strumenti di analisi di social media consentono alle organizzazioni di comprendere gli argomenti di tendenza, i soggetti significativi e le attitudini con un elevato numero di post nei social media. L’analisi del sentimento, denominata anche "opinion mining", utilizza gli strumenti di analisi dei social media per determinare le attitudini rispetto a un prodotto, un’idea e così via. L'analisi delle tendenze Twitter in tempo reale è un ottimo esempio, perché il modello di sottoscrizione hashtag consente di ascoltare determinate parole chiave e sviluppare l'analisi dei sentimenti nel feed.

## Scenario: analisi dei sentimenti in tempo reale

Un sito Web di notizie è interessato a superare la concorrenza offrendo contenuto del sito immediatamente fruibile dai lettori. Viene utilizzata l’analisi dei social media su argomenti rilevanti per i lettori eseguendo l’analisi del sentimento in tempo reale sui dati di Twitter. In particolare, per identificare gli argomenti che sono di tendenza in tempo reale su Twitter, si necessita di un’analisi in tempo reale sui volumi dei tweet e relativi sentimenti implicati per gli argomenti più importanti. In questo modo, è necessario un motore di analisi dei sentimenti basato su questo feed di social media.

## Prerequisiti
1.	Account Twitter e [token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
2.	[TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) dall'Area download Microsoft
3.	Facoltativo: codice sorgente per client twitter da [Github](https://aka.ms/azure-stream-analytics-twitterclient)

## Creare un input dell’hub eventi e un gruppo di consumer

L'applicazione di esempio genererà gli eventi e li invierà a un'istanza dell’hub eventi (o semplicemente hub eventi). Gli hub eventi del bus di servizio sono il metodo preferito per l’inserimento di eventi per l'analisi dei flussi. Vedere la documentazione relativa agli hub eventi nella [documentazione relativa al bus di servizio](/documentation/services/service-bus/)

Attenersi alla procedura seguente per creare un hub eventi.

1.	Nel portale di Azure fare clic su **NUOVO** > **SERVIZI APP** > **BUS DI SERVIZIO** > **HUB EVENTI** > **CREAZIONE RAPIDA** e inserire un nome, un’area e uno spazio dei nomi nuovo o esistente per creare un nuovo hub eventi.
2.	Come procedura consigliata, ogni processo di analisi dei flussi deve essere letto da un singolo gruppo di consumer di hub eventi. Verrà illustrata la procedura di creazione di un gruppo di consumer riportata di seguito e ulteriori informazioni. Per creare un gruppo di consumer, individuare l'hub eventi appena creato, quindi scegliere la scheda **GRUPPI DI CONSUMER**, fare clic su **CREA** nella parte inferiore della pagina, quindi fornire un nome per il gruppo di consumer.
3.	Per concedere l'accesso all'Hub di eventi, è necessario creare un criterio di accesso condiviso. Scegliere la scheda **CONFIGURA** dell'Hub eventi.
4.	Under **CRITERI DI ACCESSO CONDIVISI**, creare un nuovo criterio di **gestione** delle autorizzazioni.


  	![Criteri di accesso condivisi in cui è possibile creare un nuovo criterio con autorizzazioni di gestione.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.	Fare clic su **SAVE** nella parte inferiore della pagina.
6.	Per eseguire questa operazione, fare clic su **DASHBOARD**, quindi su **INFORMAZIONI DI CONNESSIONE** nella parte inferiore della pagina e infine salvare la stringa di connessione. Utilizzare l'icona di copia visualizzata sotto l'icona di ricerca.

## Configurazione e avvio dell'applicazione client Twitter

È stata messa a disposizione un'applicazione client che interagirà con i dati di Twitter tramite le [API di streaming di Twitter](https://dev.twitter.com/streaming/overview) per raccogliere gli eventi Tweet relativi a un insieme di argomenti con parametri. Lo strumento di origine [Sentiment140](http://help.sentiment140.com/) viene utilizzato per assegnare un valore sentimento a ogni tweet (0: negativo 2: neutro, 4: positivo) e gli eventi di Tweet vengono indirizzati all'hub eventi.

Attenersi alla seguente procedura per configurare l'applicazione:

1.	[Scaricare la soluzione TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip)
2.	Aprire TwitterClient.exe.config e sostituire oauth\_consumer\_key, oauth\_consumer\_secret, oauth\_token, oauth\_token\_secret con i token di Twitter con i propri valori.

	[Passaggi per generare un token di accesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)

	Si noti che è necessario eseguire un'applicazione vuota per generare un token.
3.	Sostituire i valori EventHubConnectionString ed EventHubName in TwitterClient.exe.config con la stringa di connessione hub eventi e il nome. La stringa di connessione copiata in precedenza fornisce sia la stringa di connessione di Hub eventi che il nome, pertanto è necessario fare attenzione a separarli e a inserire ogni elemento nel campo corretto. Ad esempio con una stringa di connessione:

    Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

	Il file TwitterClient.exe.config deve contenere le impostazioni come nell'esempio seguente:

	add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey" add key="EventHubName" value="yourhub"

	È importante notare che il testo "EntityPath=" NON viene visualizzato nel valore EventHubName.
	
4.	*Facoltativo:* modificare le parole chiave da cercare. Per impostazione predefinita, questa applicazione cerca le parole chiave "Azure, Skype, XBox, Microsoft, Seattle". È possibile modificare i valori di twitter\_keywords in TwitterClient.exe.config, se necessario.
5.	Eseguire **TwitterClient.exe** per avviare l'applicazione. Gli eventi di Tweet con i valori CreatedAt, Topic e SentimentScore inviati all'hub eventi:

	![Analisi dei sentimenti: valori SentimentScore inviati a un hub eventi.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## Creare un processo di Analisi di flusso

Ora che si dispone di uno streaming di eventi di Tweet in tempo reale, è possibile impostare un processo di Analisi di flusso per analizzare questi eventi in tempo reale.

### Eseguire il provisioning di un processo di Analisi dei flussi

1.	Nel [portale di Azure](https://manage.windowsazure.com/), fare clic su **NUOVO** > **SERVIZI DATI** > **ANALISI DI FLUSSO** > **CREAZIONE RAPIDA**.
2.	Specificare i valori seguenti, quindi fare clic su **CREA PROCESSO DI ANALISI DEI FLUSSI**:

	* **NOME PROCESSO**: immettere un nome del processo.
	* **AREA**: scegliere l'area in cui si desidera eseguire il processo. È consigliabile inserire il processo e l'hub eventi nella stessa area per garantire prestazioni migliori ed evitare addebiti connessi al trasferimento di dati tra aree diverse.
	* **ACCOUNT DI ARCHIVIAZIONE**: scegliere l'account di archiviazione da usare per archiviare i dati di monitoraggio per tutti i processi di Analisi dei flussi in esecuzione all'interno dell'area scelta. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.

3.	Fare clic su **ANALISI DEI FLUSSI** nel riquadro sinistro per visualizzare un elenco dei processi di Analisi dei flussi. ![Icona di servizio Analisi di flusso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

4.	Il nuovo processo verrà visualizzato nell'elenco con uno stato **CREATO**. Si noti che il pulsante **AVVIA** nella parte inferiore della pagina è disabilitato. Prima di poter avviare il processo, è necessario configurare l'input, l'output, la query per l'esecuzione.


### Specificare l'input del processo
1.	In Analisi dei flussi fare clic su **INPUT** nella parte superiore della pagina, quindi scegliere **AGGIUNGI INPUT**. La finestra di dialogo visualizzata presenterà un numero di passaggi per l'impostazione dell'input.
2.	Selezionare **FLUSSO DATI**, quindi fare clic sul pulsante a destra.
3.	Selezionare **HUB EVENTI**, quindi fare clic sul pulsante a destra.
4.	Digitare o selezionare i valori seguenti nella terza pagina:

	* **ALIAS di INPUT**: immettere un nome descrittivo per il processo di input, ad esempio TwitterStream. Si noti che il nome verrà usato nella query in un secondo momento. **HUB EVENTI**: se l'hub eventi creato si trova nella stessa sottoscrizione del processo di Analisi di flusso, selezionare lo spazio dei nomi in cui si trova l'hub eventi.

		Se l'hub eventi si trova in un'altra sottoscrizione, selezionare **Usa hub eventi da un'altra sottoscrizione**, quindi immettere manualmente i valori per **SPAZIO DEI NOMI DEL BUS DI SERVIZIO**, **NOME HUB EVENTI**, **NOME CRITERIO HUB EVENTI**, **CHIAVE CRITERIO HUB EVENTI** e **CONTEGGIO PARTIZIONI HUB EVENTI**.

	* **NOME HUB EVENTI**: selezionare il nome dell'hub eventi
	* **NOME DEL CRITERIO HUB EVENTI**: selezionare il criterio dell'hub eventi creato precedentemente in questa esercitazione.
	* **GRUPPO DI CONSUMER DI HUB EVENTI**: digitarne il nome nel gruppo di consumer creato in precedenza in questa esercitazione.
5.	Fare clic sul pulsante a destra.
6.	Specificare i valori seguenti:

	* **FORMATO SERIALIZZATORE EVENTI**: JSON
	* **CODIFICA**: UTF8

7.	Fare clic sul pulsante con il segno di spunta per aggiungere l'origine e per verificare che Analisi dei flussi possa connettersi all'hub eventi.

### Specificare una query del processo

Analisi dei flussi supporta un semplice modello di query dichiarative per descrivere le trasformazioni. Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx). Questa esercitazione consente di creare e testare diverse query su dati di Twitter.

#### Input dati di esempio

Per convalidare la query in base al tipo di dati effettivo del processo, è possibile utilizzare la funzionalità DATI DI ESEMPIO per estrarre gli eventi dal flusso di e creare un file .JSON degli eventi per il test.

1.	Selezionare l'input dell'hub eventi e fare clic su **DATI DI ESEMPIO** nella parte inferiore della pagina.
2.	Nella finestra di dialogo visualizzata, specificare un’**ORA DI INIZIO** da cui iniziare a raccogliere dati e una **DURATA** per la quantità di dati da utilizzare.
3.	Fare clic sul pulsante **DETTAGLI**, quindi sul collegamento**Fare clic qui** per scaricare e salvare il File .JSON generato.

#### Query pass-through
Per iniziare, faremo una semplice query pass-through che proietti tutti i campi in un evento.

1.	Fare clic su **QUERY** dalla parte superiore della pagina del processo di analisi dei flussi.
2.	Nell'editor di codice sostituire il modello di query iniziale con le operazioni seguenti:

		SELECT * FROM TwitterStream

	Assicurarsi che il nome dell'origine di input corrisponda al nome dell'input specificato precedentemente.

3.	Fare clic su **TEST** nell'editor di query
4.	Individuare il file di esempio .JSON
5.	Fare clic sul segno di spunta e vedere i risultati visualizzati sotto la definizione della query.

	![Risultati visualizzati sotto la definizione della query](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### Numero di tweet per argomento: finestra a cascata con aggregazione

Per confrontare il numero di riferimenti tra gli argomenti, è possibile sfruttare una [finestra a cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) per ottenere il conteggio dei riferimenti per argomento ogni 5 secondi.

1.	Modificare la query nell'editor di codice nel modo seguente:

		SELECT System.Timestamp as Time, Topic, COUNT(*)
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY TUMBLINGWINDOW(s, 5), Topic

	Si noti che in questa query viene utilizzata la parola chiave **TIMESTAMP BY** per specificare un campo timestamp nel payload da utilizzare nel calcolo temporale. Se il campo non è stato specificato, verrà eseguita l'operazione di windowing utilizzando l’orario in cui ogni evento è arrivato all’hub eventi. Ulteriori informazioni sono disponibili nel relativo argomento delle [informazioni sulla query dell’analisi del flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx).

	Questa query consente di accedere anche a un timestamp per la fine di ogni finestra con **System.Timestamp**.

2.	Fare clic su **RIESEGUI** con l'editor di query per visualizzare i risultati della query.

#### Identificazione degli argomenti di tendenza: finestra scorrevole

Per identificare argomenti di tendenza si cercheranno argomenti che superano un valore soglia per i riferimenti entro un determinato periodo di tempo. Ai fini di questa esercitazione, verranno selezionati gli argomenti menzionati più di 20 volte negli ultimi 5 secondi usando una [finestra scorrevole](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.	Modificare la query nell'editor di codice nel modo seguente:

		SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY SLIDINGWINDOW(s, 5), topic
		HAVING COUNT(*) > 20

2.	Fare clic su **RIESEGUI** con l'editor di query per visualizzare i risultati della query.

	![Output della query della finestra scorrevole](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### Conteggio dei riferimenti e sentimenti: finestra a cascata con aggregazione

Nella query finale testata viene utilizzata una finestra a cascata per ottenere il numero di riferimenti e Media, minimo, massimo e deviazione standard del punteggio dei sentimenti per ogni argomento ogni 5 secondi.

1.	Modificare la query nell'editor di codice nel modo seguente:

		SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
    	Max(SentimentScore), STDEV(SentimentScore)
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.	Fare clic su **RIESEGUI** con l'editor di query per visualizzare i risultati della query.
3.	Questa è la query che verrà utilizzata per il nostro dashboard. Fare clic su **SAVE** nella parte inferiore della pagina.


## Creare un sink di output

Ora che è stato definito un flusso di eventi, un input dell’hub eventi per acquisire gli eventi e una query per eseguire una trasformazione nel flusso, l'ultimo passaggio consiste nel definire un sink di output per il processo. Gli eventi tweet aggregati tweet dalla query del processo verranno scritti in un blob di Microsoft Azure. È inoltre possibile effettuare il push dei risultati al database SQL, all’archivio tabelle o all’hub eventi, a seconda delle esigenze specifiche dell'applicazione.

Se non se ne dispone, attenersi alla procedura seguente per creare un contenitore per l'archiviazione BLOB:

1.	Dal portale, creare un nuovo account di archiviazione facendo clic su **NUOVO** > **SERVIZI DATI** > **ARCHIVIAZIONE** > **CREAZIONE RAPIDA** e attenendosi alle istruzioni visualizzate.
2.	Selezionare l'account di archiviazione e quindi fare clic su **CONTENITORI** nella parte superiore della pagina, quindi fare clic su **AGGIUNGI**.
3.	Specificare un **NOME** per il contenitore e impostare il relativo **ACCESSO** al BLOB pubblico.

## Specificare l'output del processo

1.	In Analisi dei flussi fare clic su **OUTPUT** nella parte superiore della pagina, quindi scegliere **AGGIUNGI OUTPUT**. La finestra di dialogo visualizzata presenterà un numero di passaggi per l'impostazione dell'input.
2.	Selezionare **ARCHIVIAZIONE BLOB**, quindi fare clic sul pulsante a destra.
3.	Digitare o selezionare i valori seguenti nella terza pagina:

	* **ALIAS DI OUTPUT**: immettere un nome descrittivo per l'output del processo.
	* **SOTTOSCRIZIONE**: se l'archiviazione BLOB creata si trova nella stessa sottoscrizione del processo di analisi di flusso, selezionare **Usare l'account di archiviazione dalla sottoscrizione corrente**. Se il dispositivo di archiviazione è in una sottoscrizione diversa, selezionare l’opzione per **utilizzare l’account di archiviazione utilizzato da un'altra sottoscrizione** e immettere manualmente le informazioni relative all’**ACCOUNT DI ARCHIVIAZIONE**, **CHIAVE DELL’ACCOUNT DI ARCHIVIAZIONE**, **CONTENITORE**.
	* In **Nome account di archiviazione** scegliere l'account di archiviazione da utilizzare.
	* **CONTENITORE**: selezionare il nome del contenitore
	* **PREFISSO DEL NOME FILE**: digitare un prefisso di file da usare per la scrittura dell'output di BLOB

4.	Fare clic sul pulsante a destra.
5.	Specificare i valori seguenti:
	* **FORMATO SERIALIZZATORE EVENTI**: JSON
	* **CODIFICA** = "UTF8",
6.	Fare clic sul pulsante con il segno di spunta per aggiungere l'origine e per verificare che l’analisi dei flussi possa connettersi all’account di archiviazione.

## Avviare il processo

Poiché sono stati specificati un processo di input, la query e l'output, a questo punto è possibile avviare il processo di analisi dei flussi.

1.	Dal processo **DASHBOARD**, fare clic su **AVVIA** nella parte inferiore della pagina.
2.	Nella finestra di dialogo visualizzata, selezionare **ORA DI INIZIO DEL PROCESSO**, quindi fare clic sul pulsante del segno di spunta nella parte inferiore della finestra di dialogo. Lo stato del processo cambierà da **Avvio** e a breve passerà a **In esecuzione**.


## Visualizzare l'output per l'analisi dei sentimenti

Una volta che il processo è in esecuzione ed elabora il flusso di Twitter in tempo reale, è necessario scegliere come si desidera visualizzare l'output per l'analisi dei sentimenti. Utilizzare uno strumento come [Esplora archivi Azure](https://azurestorageexplorer.codeplex.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) per visualizzare l'output del processo in tempo reale. A questo punto, è possibile estendere l'applicazione in modo da includere un dashboard personalizzato dell'output, come quello illustrato di seguito utilizzando [Power BI](https://powerbi.com/).

![Analisi di social media: output dell’analisi dei sentimenti (opinion mining) di Analisi di flusso in un dashboard di Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics).


## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0928_2016-->