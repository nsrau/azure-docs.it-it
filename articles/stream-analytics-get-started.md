<properties 
	pageTitle="Introduzione all'uso di Analisi dei flussi di Azure | Azure" 
	description="Informazioni su come iniziare a usare Analisi dei flussi di Azure per elaborare e trasformare gli eventi in Hub eventi del bus di servizio di Azure e archiviare i risultati nel database SQL di Azure." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/17/2015" 
	ms.author="jgao" />


# Introduzione all'uso di Analisi dei flussi di Azure

Analisi dei flussi di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Per altre informazioni, vedere [Introduzione ad Analisi dei flussi di Azure][stream.analytics.introduction] e la [documentazione relativa ad Analisi dei flussi di Azure][stream.analytics.documentation].

Per iniziare subito a usare Analisi dei flussi, questa esercitazione illustrerà come usare i dati delle letture di temperatura da un [hub eventi del bus di servizio di Azure,][azure.event.hubs.documentation] e come elaborarli, inviando l'output risultante a un [database SQL di Azure][azure.sql.database.documentation].  Il diagramma seguente illustra il flusso di eventi input - elaborazione - output:
  
![Azure Stream Analytics get started flow][img.get.started.flowchart]

##Generare dati di esempio dell'hub eventi
In questa esercitazione verrà usata l'applicazione introduttiva dell'hub eventi del bus di servizio, un esempio di codice disponibile in MSDN Code Gallery, per creare un nuovo hub eventi, generare delle letture di esempio delle temperature di un dispositivo, quindi inviare i dati delle letture del dispositivo all'hub eventi.

###Creare uno spazio dei nomi del bus di servizio
L'applicazione di esempio creerà un hub eventi in uno spazio dei nomi del bus di servizio preesistente.  È possibile usare uno spazio dei nomi del bus di servizio di cui è già stato eseguito il provisioning oppure seguire la procedura seguente per crearne uno nuovo:

1.	Accedere al [portale di gestione di Azure][azure.management.portal].
2.	Fare clic su **CREA** nella parte inferiore della pagina del bus di servizio, quindi seguire le istruzioni per creare uno spazio dei nomi. Usare **MESSAGGISTICA** come tipo.
3.	Fare clic nello spazio dei nomi appena creato, quindi su **INFORMAZIONI DI CONNESSIONE** nella parte inferiore della pagina.
4.	Copiare la stringa di connessione. Questa stringa verrà usata più avanti nell'esercitazione.

###Creare un account di archiviazione di Azure

Questa applicazione di esempio richiede un account di archiviazione di Azure o un emulatore di archiviazione per mantenere lo stato dell'applicazione. È possibile usare un account di archiviazione esistente o eseguire la procedura seguente per crearne uno: 

1.	Dal portale, creare un nuovo account di archiviazione facendo clic su **NUOVO**, **SERVIZI DATI**, **ARCHIVIAZIONE**, **CREAZIONE RAPIDA** e seguire le istruzioni.
2.	Fare clic nell'account di archiviazione appena creato, quindi su **GESTISCI CHIAVI DI ACCESSO** nella parte inferiore della pagina.
3.	Copiare il nome dell'account di archiviazione e una delle chiavi di accesso.

###Generare dati di esempio dell'hub eventi

1.	Scaricare e decomprimere [Service Bus Event Hubs Getting Started.zip](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Event-Hub-286fd097) nella workstation in uso.
2.	Aprire il file di soluzione **EventHubSample.sln** in Visual Studio.
3.	Aprire **app.config**.
4.	Specificare le stringhe di connessione per il bus di servizio e per l'account di archiviazione. I nomi delle chiavi sono **Microsoft.ServiceBus.ConnectionString** e **AzureStorageConnectionString**. La stringa di connessione dell'account di archiviazione sarà nel formato seguente: 	

		DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<yourAccountKey>;
5.	Compilare la soluzione.
6.	Eseguire l'applicazione dalla cartella bin.  L'utilizzo è il seguente: 

		BasicEventHubSample <eventhubname> <NumberOfMessagesToSend> <NumberOfPartitions> 

	L'esempio seguente crea un nuovo hub eventi denominato **devicereadings** con **16** partizioni, quindi invia ad esso **200** eventi: 

		BasicEventHubSample devicereadings 200 16

 	![insert image here][img.stream.analytics.event.hub.client.output] 
 	

###Creare un criterio di accesso condiviso dell'hub eventi
Anche se nello spazio dei nomi del bus di servizio esiste già un criterio di accesso condiviso che può essere usato per connettersi a tutti gli elementi all'interno dello spazio dei nomi stesso, per rispettare le procedure di sicurezza consigliate si creerà un criterio separato per il solo hub eventi.

1.	Dall'area di lavoro del bus di servizio nel portale, fare clic sul nome dello spazio dei nomi del bus di servizio.
2.	Fare clic su **HUB EVENTI** nella parte superiore della pagina.
3.	Fare clic su **devicereadings**, ovvero sull'hub eventi di questa esercitazione.
4.	Fare clic su **CONFIGURA** nella parte superiore della pagina.
5.	In Criteri di accesso condivisi creare un nuovo criterio con autorizzazioni di **gestione**.

	![][img.stream.analytics.event.hub.shared.access.policy.config]
6.	Fare clic su **SALVA** nella parte inferiore della pagina.
7.	Se l'hub eventi si trova in una sottoscrizione diversa da quella in cui si troverà il processo di Analisi dei flussi, sarà necessario copiare e salvare le informazioni di connessione per l'uso successivo.  Per eseguire questa operazione, fare clic su **DASHBOARD**, quindi su **INFORMAZIONI DI CONNESSIONE** nella parte inferiore della pagina e infine salvare la stringa di connessione.


##Preparare un database SQL di Azure per archiviare i dati di output
Analisi dei flussi di Azure può restituire dati a un database SQL di Azure, a una risorsa di archiviazione BLOB di Azure e a Hub eventi di Azure. In questa esercitazione verrà definito un processo che restituisce l'output a un database SQL di Azure. Per altre informazioni, vedere Introduzione al database SQL di Microsoft Azure.

###Creare un database SQL di Azure
Se si dispone già di un database SQL di Azure da usare per questa esercitazione, ignorare questa sezione.

1.	Nel portale di gestione fare clic su **NUOVO**, **SERVIZI DATI**, **DATABASE SQL**, **CREAZIONE RAPIDA**.  Specificare un nome di database in un server esistente o in un nuovo server di database SQL.
2.	Selezionare il database appena creato
3.	Fare clic su **DASHBOARD**, quindi su **Mostra stringhe di connessione** nella parte destra della pagina, quindi copiare la stringa di connessione **ADO.NET**. Questa stringa verrà usata più avanti nell'esercitazione.  
4.	Assicurarsi che le impostazioni del firewall a livello di server consentano di connettersi al database.  A tale scopo, è possibile aggiungere una nuova regola IP nella scheda Configurazione del Server. Per altri dettagli, comprese le informazioni sulla gestione degli IP dinamici, vedere [http://msdn.microsoft.com/library/azure/ee621782.aspx](http://msdn.microsoft.com/library/azure/ee621782.aspx).

###Creare tabelle di output
1.	Aprire Visual Studio o SQL Server Management Studio.
2.	Connettersi al database SQL di Azure
3.	Usare le istruzioni T-SQL seguenti per creare due tabelle nel database:

		CREATE TABLE [dbo].[PassthroughReadings] (
		    [DeviceId]      BIGINT NULL,
			[Temperature] BIGINT    NULL
		);

		GO
		CREATE CLUSTERED INDEX [PassthroughReadings]
		    ON [dbo].[PassthroughReadings]([DeviceId] ASC);
		GO

		CREATE TABLE [dbo].[AvgReadings] (
		    [WinStartTime]   DATETIME2 (6) NULL,
		    [WinEndTime]     DATETIME2 (6) NULL,
		    [DeviceId]      BIGINT NULL,
			[AvgTemperature] FLOAT (53)    NULL,
			[EventCount] BIGINT null
		);
		
		GO
		CREATE CLUSTERED INDEX [AvgReadings]
		    ON [dbo].[AvgReadings]([DeviceId] ASC);

	>[WACOM.NOTE] Gli indici cluster sono obbligatori in tutte le tabelle di database SQL per consentire l'inserimento di dati.
	   
##Creare un processo di Analisi dei flussi

Dopo aver creato l'hub eventi del bus di servizio di Azure, il database SQL di Azure e le tabelle di output, si è pronti per creare un processo di Analisi dei flussi.

###Eseguire il provisioning di un processo di Analisi dei flussi
1.	Nel portale di gestione fare clic su **NUOVO**, **SERVIZI DATI**, **ANALISI DEI FLUSSI**, **CREAZIONE RAPIDA**. 
2.	Specificare i valori seguenti, quindi fare clic su **CREA PROCESSO DI ANALISI DEI FLUSSI**:

	- **NOME PROCESSO**: Indicare un nome per il processo. Ad esempio, **DeviceTemperatures**.
	- **AREA**: Scegliere l'area in cui si vuole eseguire il processo. Analisi dei flussi di Azure è attualmente disponibile solo in 2 aree durante l'anteprima. Per altre informazioni, vedere [Limitazioni e problemi noti di Analisi dei flussi di Azure][stream.analytics.limitations]. È consigliabile inserire il processo e l'hub eventi nella stessa area per garantire prestazioni migliori ed evitare addebiti connessi al trasferimento di dati tra aree diverse.
	- **ACCOUNT DI ARCHIVIAZIONE**: Scegliere l'account di archiviazione da usare per archiviare i dati di monitoraggio per tutti i processi di Analisi dei flussi in esecuzione all'interno dell'area scelta. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.
	
3.	Fare clic su **ANALISI DEI FLUSSI** nel riquadro sinistro per visualizzare un elenco dei processi di Analisi dei flussi.

	![][img.stream.analytics.portal.button]
 
	Il nuovo processo verrà visualizzato nell'elenco con uno stato di **NON AVVIATO**.  Si noti che il pulsante **AVVIA** nella parte inferiore della pagina è disabilitato. Prima di poter avviare il processo, è necessario configurare l'input, l'output, la query e quant'altro necessario per l'esecuzione. 

###Specificare l'input del processo

1.	Fare clic sul nome del processo.
2.	Fare clic su **INPUT** nella parte superiore della pagina, quindi scegliere **AGGIUNGI INPUT**. La finestra di dialogo visualizzata presenterà un numero di passaggi per l'impostazione dell'input.
3.	Selezionare **FLUSSO DATI**, quindi fare clic sul pulsante a destra.
4.	Selezionare **HUB EVENTI**, quindi fare clic sul pulsante a destra.
5.	Digitare o selezionare i valori seguenti nella terza pagina: 

	- **ALIAS INPUT**: Immettere un nome descrittivo per l'input del processo. Si noti che il nome verrà usato nella query in un secondo momento.
	- **HUB EVENTI**: Se l'hub eventi creato si trova nella stessa sottoscrizione del processo di Analisi dei flussi, selezionare lo spazio dei nomi in cui si trova l'hub eventi.  

		Se l'hub eventi si trova in un'altra sottoscrizione, selezionare **Usa hub eventi da un'altra sottoscrizione** quindi immettere manualmente i valori per **SPAZIO DEI NOMI DEL BUS DI SERVIZIO**, **NOME HUB EVENTI**, **NOME CRITERIO HUB EVENTI**, **CHIAVE CRITERIO HUB EVENTI** e **CONTEGGIO PARTIZIONI HUB EVENTI**.  

		>[WACOM.NOTE] Questo esempio usa il numero predefinito di partizioni, che è pari a 16.
		
	- **NOME HUB EVENTI**: Selezionare il nome dell'hub eventi di Azure creato. Per questa esercitazione, usare **devicereadings**.
	- **NOME CRITERIO HUB EVENTI**: Selezionare il criterio dell'hub eventi creato precedentemente in questa esercitazione.
 
	![][img.stream.analytics.config.input]

6.	Fare clic sul pulsante a destra.
7.	Specificare i valori seguenti:

	- **FORMATO SERIALIZZATORE EVENTI**: JSON
	- **CODIFICA**: UTF8

8.	Fare clic sul pulsante con il segno di spunta per aggiungere l'origine e per verificare che Analisi dei flussi possa connettersi all'hub eventi.

###Specificare l'output del processo
1.	Fare clic su **OUTPUT** nella parte superiore della pagina, quindi scegliere **AGGIUNGI OUTPUT**.
2.	Selezionare **DATABASE SQL**, quindi fare clic sul pulsante a destra.
3.	Digitare o selezionare i valori seguenti:  Usare la stringa di connessione ADO.NET dal database per compilare i campi seguenti:

	- **DATABASE SQL**: Scegliere il database SQL creato in precedenza nell'esercitazione.  Se questo si trova nella stessa sottoscrizione, selezionare il database dal menu a discesa.   In caso contrario, compilare manualmente i campi Nome Server e Database SQL. 
	- **NOME UTENTE**: Immettere il nome di accesso al database SQL.
	- **PASSWORD**: Immettere la password di accesso al database SQL.
	- **TABELLA**: Specificare la tabella a cui inviare l'output.  Per il momento, usare **PassthroughReadings**.

	![][img.stream.analytics.config.output]

4.	Fare clic sul pulsante con il segno di spunta per creare l'output e per verificare che Analisi dei flussi possa connettersi al database SQL come specificato.

###Specificare una query del processo
Analisi dei flussi supporta un semplice modello di query dichiarative per descrivere le trasformazioni.  Per altre informazioni sul linguaggio, vedere le Informazioni di riferimento sul linguaggio di Query di Analisi dei flussi di Azure.  

Questa esercitazione inizia con una semplice query pass-through che restituisce le letture delle temperature di un dispositivo a una tabella di Database SQL.

1.	Fare clic su **Query** nella parte superiore della pagina. 
2.	Aggiungere il codice seguente all'editor del codice:

		SELECT DeviceId, Temperature FROM input
Assicurarsi che il nome dell'origine di input corrisponda al nome dell'input specificato precedentemente.
3.	Fare clic su **SALVA** nella parte inferiore della pagina, quindi su **SÌ** per confermare.

##Avviare il processo
Per impostazione predefinita, i processi di Analisi dei flussi iniziano a leggere gli eventi in ingresso dal momento dell'avvio del processo.  Poiché l'hub eventi contiene i dati esistenti da elaborare, è necessario configurare il processo per usare questi dati cronologici.  

1.	Fare clic su **CONFIGURA** nella parte superiore della pagina.
2.	Modificare il valore di **AVVIO OUTPUT** in **ORA PERSONALIZZATA** e specificare un'ora di inizio.  Assicurarsi che l'ora di inizio sia precedente all'ora di avvio di BasicEventHubSample.  
3.	Fare clic su **SALVA** nella parte inferiore della pagina, quindi su **SÌ** per confermare.
3.	Fare clic su **DASHBOARD** nella parte superiore della pagina, quindi fare clic su **AVVIA** nella parte inferiore e infine su **SÌ** per confermare.  Nel riquadro **riepilogo rapido** lo **STATO** cambierà nello stato di **avvio**. Potrebbero essere necessari alcuni minuti per completare il processo di avvio e passare allo stato di **esecuzione**.   


##Visualizzare l'output del processo

1.	In Visual Studio o SQL Server Management Studio connettersi al database SQL ed eseguire la query seguente: 

		SELECT * FROM PassthroughReadings

2.	Verranno visualizzati record di Hub eventi corrispondenti agli eventi di lettura.   

	![][img.stream.analytics.job.output1]

	È possibile eseguire nuovamente l'applicazione BasicEventHubSample per generare nuovi eventi e seguirne la propagazione nell'output in tempo reale rieseguendo la query SELECT *.
	
	Se si verificano problemi come output mancante o imprevisto, visualizzare i log delle operazioni per il processo, collegato nel riquadro destro della pagina del dashboard.

##Arrestare, aggiornare e riavviare il processo
Si può ora eseguire una query più interessante sui dati.
1.	Nella pagina **DASHBOARD** o **MONITOR** fare clic su **ARRESTA**.
2.	Nella pagina **QUERY** sostituire la query esistente con la seguente, quindi fare clic su **SALVA**:

		SELECT DateAdd(second,-5,System.TimeStamp) as WinStartTime, system.TimeStamp as WinEndTime, DeviceId, Avg(Temperature) as AvgTemperature, Count(*) as EventCount 
		FROM input
		GROUP BY TumblingWindow(second, 5), DeviceId

	Questa nuova query userà come timestamp l'ora in cui è stato eseguito il push dell'evento nell'hub eventi, quindi cercherà e proietterà la lettura della temperatura media ogni 5 secondi e il numero di eventi che rientrano in tale finestra di 5 secondi.
3.	Nella pagina **OUTPUT** fare clic su **MODIFICA**.  Modificare la tabella di output da PassthroughReadings a AvgReadings, quindi fare clic sull'icona con il segno di spunta.

4.	Dalla pagina **DASHBOARD** fare clic su **AVVIA**.

##Visualizzare l'output del processo

1.	In Visual Studio o SQL Server Management Studio connettersi al database SQL ed eseguire la query seguente:

		SELECT * from AvgReadings

2.	Verranno visualizzati i record per gli intervalli di 5 secondi che mostrano la temperatura media e il numero di eventi per ogni dispositivo: 

	![][img.stream.analytics.job.output2]
 
3.	 Per continuare a visualizzare gli eventi elaborati dal processo in esecuzione, eseguire nuovamente l'applicazione BasicEventHubSample.







##<a name="nextsteps"></a>Passaggi successivi
In questa esercitazione si è appreso a usare Analisi dei flussi per elaborare dati meteo. Per altre informazioni, vedere gli articoli seguenti:


- [Introduzione ad Analisi dei flussi di Azure][stream.analytics.introduction]
- [Guida per gli sviluppatori di Analisi dei flussi di Azure][stream.analytics.developer.guide]
- [Ridimensionare i processi di Analisi dei flussi di Azure][stream.analytics.scale]
- [Limitazioni e problemi noti di Analisi dei flussi di Azure][stream.analytics.limitations]
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure][stream.analytics.query.language.reference]
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure][stream.analytics.rest.api.reference]




[img.stream.analytics.event.hub.client.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHClientOuput.png
[img.stream.analytics.event.hub.shared.access.policy.config]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHSharedAccessPolicyConfig.png
[img.stream.analytics.job.output2]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput2.png
[img.stream.analytics.job.output1]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput1.png
[img.stream.analytics.config.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureOutput.png
[img.stream.analytics.config.input]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureInput.png



[img.get.started.flowchart]: ./media/stream-analytics-get-started/StreamAnalytics.get.started.flowchart.png
[img.job.quick.create]: ./media/stream-analytics-get-started/StreamAnalytics.quick.create.png
[img.stream.analytics.portal.button]: ./media/stream-analytics-get-started/StreamAnalyticsPortalButton.png
[img.event.hub.policy.configure]: ./media/stream-analytics-get-started/StreamAnalytics.Event.Hub.policy.png
[img.create.table]: ./media/stream-analytics-get-started/StreamAnalytics.create.table.png
[img.stream.analytics.job.output]: ./media/stream-analytics-get-started/StreamAnalytics.job.output.png
[img.stream.analytics.operation.logs]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.png
[img.stream.analytics.operation.log.details]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.details.png


[azure.sql.database.firewall]: http://msdn.microsoft.com/library/azure/ee621782.aspx
[azure.event.hubs.documentation]: http://azure.microsoft.com/services/event-hubs/
[azure.sql.database.documentation]: http://azure.microsoft.com/services/sql-database/

[sql.database.introduction]: http://azure.microsoft.com/services/sql-database/
[event.hubs.introduction]: http://azure.microsoft.com/services/event-hubs/
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.sdk.net]: ../dotnet-sdk/

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.scale]: ../stream-analytics-scale-jobs/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093




[azure.management.portal]: https://manage.windowsazure.com


<!--HONumber=46--> 

<!--HONumber=46--> 
