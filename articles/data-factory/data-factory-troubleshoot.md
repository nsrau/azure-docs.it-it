<properties 
	pageTitle="Risolvere i problemi di Data factory di Azure" 
	description="Informazioni su come risolvere i problemi relativi all'uso di Data factory di Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Risolvere i problemi di Data factory
È possibile risolvere i problemi relativi a Data factory di Azure usando il portale di Azure oppure i cmdlet di Azure PowerShell. Questo argomento contiene procedure dettagliate che illustrano come usare il portale di Azure per risolvere rapidamente eventuali errori che possono verificarsi con Data factory.

## Problema: impossibile eseguire i cmdlet di Data factory
Per risolvere questo problema, passare la modalità Azure su **AzureResourceManager**:

Avviare **Azure PowerShell** ed eseguire il comando seguente per passare in modalità **AzureResourceManager**. I cmdlet di Data factory di Azure sono disponibili in modalità **AzureResourceManager**.

         switch-azuremode AzureResourceManager

## Problema: errore di mancata autorizzazione quando si esegue un cmdlet di Data factory
Probabilmente non si sta utilizzando l'account o la sottoscrizione Azure appropriata con Azure PowerShell. Usare i cmdlet seguenti per selezionare l'account e la sottoscrizione Azure appropriati per l'uso con Azure PowerShell.

1. Add-AzureAccount: usare l'ID utente e la password appropriati
2. Get-AzureSubscription: visualizzare tutte le sottoscrizioni relative all'account. 
3. Select-AzureSubscription <subscription name>: selezionare la sottoscrizione appropriata. Usare la stessa che consente di creare una data factory nel portale di anteprima di Azure.

## Problema: impossibile possibile avviare la configurazione rapida del gateway dati dal portale di Azure
La configurazione rapida del gateway dati richiede Internet Explorer o un Web browser compatibile con Microsoft ClickOnce. Se non si riesce ad avviare la configurazione rapida, è possibile

1. Passare a Internet Explorer se l'installazione non riesce in altri browser. Oppure
2. Usare i collegamenti "Installazione manuale" mostrati sullo stesso pannello nel portale per eseguire l'installazione, quindi copiare la chiave visualizzata sullo schermo e incollarla quando la configurazione del gateway di gestione dati è pronta. Se non viene avviata, cercare il gateway di gestione dati Microsoft nel menu Start e incollarvi la chiave all'avvio. 


## Problema: impossibile avviare Gestione credenziali dal portale di Azure
Quando si configura o si aggiorna un servizio collegato di SQL Server con il portale di Azure, l'applicazione Gestione credenziali verrà avviata per garantire la sicurezza. È richiesto Internet Explorer o un Web browser compatibile con Microsoft ClickOnce. È possibile passare a Internet Explorer se l'installazione non riesce in altri browser.

## Problema: impossibile connettersi all'istanza di SQL Server locale 
Verificare che SQL Server sia raggiungibile dal computer in cui è installato il gateway. Nel computer in cui è installato il gateway, è possibile

1. Eseguire il ping del computer in cui è installato SQL Server. Oppure
2. Provare a connettersi all'istanza di SQL Server con le credenziali specificate nel portale di Azure usando SQL Server Management Studio (SSMS).


## Problema: le sezioni di input sono sempre nello stato PendingExecution o PendingValidation

Le sezioni potrebbero essere nello stato **PendingExecution** o **PendingValidation** a causa di una serie di motivi: uno dei più comuni è che la proprietà **waitOnExternal** non viene specificata nella sezione **disponibilità** della prima tabella e/o del primo set di dati nella pipeline. Qualsiasi set di dati che viene generato all'esterno dell'ambito di Data factory di Azure deve essere contrassegnato con la proprietà **waitOnExternal** nella la sezione **availability**. Ciò indica che i dati sono esterni e non sono supportati da alcuna pipeline all'interno della data factory. Le sezioni di dati vengono contrassegnate con **Pronto** quando i dati sono disponibili nel rispettivo archivio.

Per l'utilizzo della proprietà **waitOnExternal**, vedere l'esempio riportato di seguito. È possibile specificare **waitOnExternal{}** senza impostare i valori per le proprietà nella sezione in modo che vengano usati i valori predefiniti.

Per altre informazioni su questa proprietà, vedere l'argomento Tabelle in [Informazioni di riferimento sugli script JSON][json-scripting-reference].
	
	{
	    "name": "CustomerTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "MyContainer/MySubFolder/",
	            "linkedServiceName": "MyLinkedService",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ",",
	                "rowDelimiter": ";"
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	            "waitOnExternal":
	            {
	                "dataDelay": "00:10:00",
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

 Per risolvere l'errore, aggiungere la sezione **waitOnExternal** alla definizione JSON della tabella di input e ricreare la tabella.

## Problema: l'operazione di copia ibrida non riesce
Per ulteriori informazioni:

1. Avviare il programma per la gestione della configurazione del gateway di gestione dei dati nel computer in cui è stato installato il gateway stesso. Verificare che **Nome gateway** sia impostato sul nome del gateway logico nel **portale di Azure**, che **Stato della chiave del gateway** sia **registrato** e che **Stato del servizio** sia **Avviato**. 
2. Avviare **Visualizzatore eventi**. Espandere **Registri applicazioni e servizi** e fare clic su **Gateway di gestione dati**. Verificare se sono presenti errori correlati al Gateway di gestione di dati. 

## Problema: il provisioning di HDInsight su richiesta non riesce con errore

Quando si usa un servizio collegato di tipo HDInsightOnDemandLinkedService, è necessario specificare un linkedServiceName che punta all'archivio BLOB di Azure. Questo account di archiviazione verrà usato per copiare tutti i registri e file di supporto per il cluster HDInsight su richiesta. L'attività che esegue il provisioning su richiesta in HDInsight in alcuni casi potrebbe non riuscire con l'errore seguente:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Questo errore indica generalmente che il percorso dell'account di archiviazione specificato nel linkedServiceName non è nella stessa posizione del data center in cui viene eseguito il provisioning di HDInsight. Ad esempio, se il servizio Data factory di Azure si trova negli Stati Uniti Occidentali e il provisioning di HDInsight su richiesta viene eseguito negli Stati Uniti Occidentali, ma la posizione dell'account di archiviazione BLOB di Azure è impostata su Stati Uniti Orientali, il provisioning su richiesta non riuscirà.

È anche disponibile una seconda proprietà JSON additionalLinkedServiceNames in cui è possibile specificare account di archiviazione aggiuntivi in HDInsight su richiesta. Gli account di archiviazione aggiuntivi collegati devono trovarsi nello stesso percorso del cluster HDInsight o non riusciranno, producendo lo stesso errore.



## Problema: l'attività personalizzata non riesce
Quando si usa un'attività personalizzata in Data factory di Azure (tipo di attività pipeline CustomActivity), l'applicazione personalizzata viene eseguita nel servizio collegato specificato in HDInsight come processo MapReduce di streaming solo di mapping.

Quando viene eseguita l'attività personalizzata, Data factory di Azure potrà acquisire l'output dal cluster HDInsight e salvarlo nel contenitore di archiviazione *adfjobs* nell'account di archiviazione BLOB di Azure. In caso di errore, è possibile leggere il testo dal file di testo di output **stderr** dopo che si è verificato un errore. I file sono accessibili e leggibili dal portale di Azure stesso nel Web browser o con gli strumenti di esplorazione delle risorse di archiviazione per accedere ai file conservati direttamente nel contenitore di archiviazione nell'archivio BLOB di Azure.

Per enumerare e leggere i log per una particolare attività personalizzata, è possibile usare una delle procedure dettagliate illustrate più avanti in questa pagina. Riepilogo:

1.  Nel portale di Azure, scegliere **Sfoglia** per trovare la data factory.
2.  Usare il pulsante **Diagramma** per visualizzare il diagramma della data factory e fare clic sulla tabella **Set di dati** che segue la specifica **Pipeline** contenente l'attività personalizzata. 
3.  Nel pannello **Tabella**, fare clic sulla sezione di interesse in **Sezioni con errori** relativa all'intervallo di tempo da esaminare.
4.  Viene visualizzato il pannello **Sezione dati** in cui sono elencate più **Esecuzioni attività** per la sezione. Fare clic su un'**attività** dall'elenco. 
5.  Viene visualizzato il pannello **Dettagli esecuzione attività**, con al centro **Messaggio di errore** e nella parte inferiore diversi **File di log** affiliati a tale esecuzione attività.
	- Logs/system-0.log
	- Stato
	- Stato/exit
	- Stato/stderr
	- Stato/stdout

6. Fare clic sul primo elemento **File di log** nell'elenco per aprire il log in un nuovo pannello e visualizzare il testo completo da leggere. Esaminare il testo di ogni log facendo clic su ciascuno di essi. Verrà aperto il pannello del visualizzatore di testo. È possibile scegliere il pulsante **Download** per scaricare il file di testo per la visualizzazione offline facoltativa.

Un **errore comune** di un'attività personalizzata è l'esecuzione del pacchetto non riuscita con codice di uscita "1". Per altri dettagli, vedere "wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status/stderr".

Per visualizzare ulteriori dettagli relativi a questo tipo di errore, aprire il file **stderr**. Un errore comune che è possibile osservare è una condizione di timeout come la seguente: INFO mapreduce.Job: Task Id : attempt_1424212573646_0168_m_000000_0, Status : FAILED AttemptID:attempt_1424212573646_0168_m_000000_0 Timed out after 600 secs

Lo stesso errore può essere visualizzato più volte se, ad esempio, il processo viene ritentato per tre volte nell'arco di almeno 30 minuti.

Questo errore di timeout indica che si è verificato un timeout di 600 secondi (10 minuti). In genere, ciò significa che l'applicazione .NET personalizzata non ha rilasciato alcun aggiornamento di stato per 10 minuti. Se l'applicazione è sospesa o bloccata in attesa per troppo tempo, il timeout di 10 minuti è un meccanismo di sicurezza che impedisce di attendere all'infinito e ritardare la pipeline di Data factory di Azure.

Questo timeout ha origine nella configurazione del cluster HDInsight collegato all'attività personalizzata. L'impostazione è **mapred.task.timeout**, con valore predefinito di 600000 millisecondi, come documentato nelle impostazioni predefinite di Apache: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

È possibile ignorare questa impostazione predefinita modificando le impostazioni predefinite al momento del provisioning del cluster di provisioning di HDInsight. Quando si usa Data factory di Azure e il servizio collegato **HDInsight su richiesta**, è possibile aggiungere la proprietà JSON accanto alle proprietà JSON HDInsightOnDemandLinkedService. Ad esempio, usando questa proprietà JSON, è possibile aumentare il valore a 20 minuti.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Per altre informazioni di contesto e un esempio completo di JSON per modificare queste proprietà di configurazione MapReduce, vedere l'esempio numero 3 nella documentazione di MSDN qui https://msdn.microsoft.com/library/azure/dn893526.aspx

## Problema: richiesta di PowerShell non riuscita con errore 400 - Richiesta non valida: "Non sono stati trovati provider di risorse..."

A partire dal 10 marzo 2015, le versioni di anteprima privata PowerShell di Data factory di Azure 2014-05-01-preview, 2014-07-01-preview, e 2014-08-01-preview non saranno più disponibili. È consigliabile usare la versione più recente dei cmdlet ADF, che ora fanno parte del download di Azure PowerShell, ad esempio il download dal seguente URL: http://go.microsoft.com/?linkid=9811175&clcid=0x409

Se si usano le versioni obsolete di Azure PowerShell SDK potrebbero essere visualizzati i seguenti errori:

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a>Procedura dettagliata: Risoluzione dei problemi relativi a errori di copia dei dati
In questa procedura dettagliata si introdurrà un errore nell'esercitazione dell'articolo Introduzione a Data factory e si apprenderà come usare il portale di Azure per risolvere l'errore.

### Prerequisiti
1. Completare l'esercitazione nell'articolo [Introduzione a Data factory di Azure][adfgetstarted].
2. Verificare che **ADFTutorialDataFactory** produca dati nella tabella **emp** del database SQL di Azure.  
3. Eliminare quindi la tabella **emp** (***drop table emp***) dal database SQL di Azure. Ciò introdurrà un errore.
4. Eseguire il comando seguente in **Azure PowerShell** per aggiornare il periodo attivo della pipeline in modo che cerchi di scrivere i dati nella tabella **emp** che non esiste più.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	Sostituire il valore di **StartDateTime** con il giorno corrente e il valore di **EndDateTime** con il giorno successivo.


### Usare il portale di anteprima di Azure per risolvere l'errore

1.	Accedere al [portale di anteprima di Azure][azure-preview-portal]. 
2.	Fare clic su **ADFTutorialDataFactory** da **Schermata iniziale**. Se il collegamento a Data factory non è visualizzato in **Schermata iniziale**, fare clic sull'hub **SFOGLIA**, quindi scegliere **Tutto**. Fare clic su **Istanze di Data factory** nel pannello **Sfoglia**, quindi fare clic su **ADFTutorialDataFactory**.
3.	Si noti la visualizzazione della dicitura **Con errori** nel riquadro **Set di dati**. Fare clic su **Con errori**. Viene visualizzato il pannello **Set di dati con errori**.

	![Collegamento Data factory con errori][image-data-factory-troubleshoot-with-error-link]

4. Nel pannello **Set di dati con errori** fare clic su **EmpSQLTable** per visualizzare il pannello **TABELLA**.

	![Pannello Set di dati con errori][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Nel pannello **TABELLA** saranno visibili le sezioni relative agli errori, ad esempio sezioni con un errore nell'elenco **Sezioni con errori** nella parte inferiore dello schermo. Saranno inoltre visibili eventuali sezioni recenti con errori nell'elenco **Sezioni recenti**. Fare clic su una sezione nell'elenco **Sezioni con errori**.

	![Pannello Tabella con sezioni con errori][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Se si fa clic su **Sezioni con errori** (non su un problema specifico) verrà visualizzato il pannello **SEZIONI DI DATI**, quindi fare clic su una **sezione con errori specifica** per vedere la diapositiva **SEZIONE DI DATI** corrispondente alla sezione di dati specificata.

6. Nel pannello **SEZIONE DI DATI** per **EmpSQLTable** verranno visualizzate tutte le **esecuzioni di attività** per la sezione nell'elenco in basso. Fare clic su un'**esecuzione attività** non riuscita dall'elenco.

	![Pannello Sezione dati con esecuzioni attività][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Nel pannello **Dettagli esecuzione attività** per l'esecuzione attività selezionata saranno visibili i dettagli relativi all'errore. In questo scenario verrà visualizzato un messaggio simile al seguente: **Il nome di oggetto 'emp' non è valido**.

	![Dettagli esecuzione attività con un errore][image-data-factory-troubleshoot-activity-run-with-error]

Per risolvere il problema, creare la tabella **emp** usando lo script SQL nell'articolo [Introduzione a Data factory][adfgetstarted].


### Usare i cmdlet di Azure PowerShell per risolvere l'errore
1.	Avviare **Azure PowerShell**. 
2.	Passare alla modalità **AzureResourceManager** perché i cmdlet di Data factory sono disponibili unicamente in questa modalità.

         
		switch-azuremode AzureResourceManager

3. Eseguire il comando Get-AzureDataFactorySlice per vedere le sezioni e i relativi stati. Verrà visualizzata una sezione con lo stato: Operazione non riuscita.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	Sostituire **StartDateTime** con il valore StartDateTime specificato per **Set-AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Notare l'ora in **Inizio** nella sezione con errori (quella con **Stato** impostato su **Operazione non riuscita**) nell'output. 
4. Eseguire ora il cmdlet **Get-AzureDataFactoryRun** per ottenere i dettagli sull'esecuzione dell'attività per la sezione.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	Il valore di **StartDateTime** è l'orario di inizio per la sezione con errori/problemi di cui si è preso nota nel passaggio precedente. La data e ora dovrebbe essere racchiusa tra virgolette doppie.
5. Si otterrà l'output con informazioni dettagliate sull'errore (simile al seguente):

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighivewalkthrough"></a> Procedura dettagliata: Risoluzione dei problemi relativi a errori dei processi Hive/Pig
Questa procedura dettagliata illustra come risolvere un errore con i processi Hive/Pig usando il portale di anteprima di Azure e Azure PowerShell.


### Procedura dettagliata: Usare il portale di Azure per risolvere un errore di un processo Pig o Hive
In questo scenario, il set di dati è in stato di errore a causa di un errore nell'elaborazione dell'Hive in un cluster HDInsight.

1. Fare clic su **Con errori** nel riquadro **Set di dati** della home page **DATA FACTORY**.

	![Collegamento Con errori nel riquadro Set di dati][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Nel pannello **Set di dati con errori**, fare clic sulla **tabella** che interessa.

	![Pannello Set di dati con errori][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Nel pannello **TABELLA**, fare clic sulla **sezione con errori** con **STATO** impostato su **Operazione non riuscita**.

	![Tabella con sezioni con errori][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Nel pannello **SEZIONE DI DATI**, fare clic sull'**esecuzione attività** non riuscita.

	![Sezione dati con esecuzioni non riuscite][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Nel pannello **DETTAGLI ESECUZIONE ATTIVITÀ** è possibile scaricare i file associati all'elaborazione di HDInsight. Fare clic su **Scarica** in corrispondenza di **Stato/stderr** per scaricare il file di log degli errori contenente i dettagli dell'errore stesso.

	![Dettagli esecuzione attività con collegamento di download][image-data-factory-troubleshoot-activity-run-details]

    
### Procedura dettagliata: Usare Azure PowerShell per risolvere un errore di un processo Pig o Hive
1.	Avviare **Azure PowerShell**. 
2.	Passare alla modalità **AzureResourceManager** perché i cmdlet di Data factory sono disponibili unicamente in questa modalità.

         
		switch-azuremode AzureResourceManager

3. Eseguire il comando Get-AzureDataFactorySlice per vedere le sezioni e i relativi stati. Verrà visualizzata una sezione con lo stato: Operazione non riuscita.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Sostituire **StartDateTime** con il valore StartDateTime specificato per **Set-AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Notare l'ora in **Inizio** nella sezione con errori (quella con **Stato** impostato su **Operazione non riuscita**) nell'output. 
4. Eseguire ora il cmdlet **Get-AzureDataFactoryRun** per ottenere i dettagli sull'esecuzione dell'attività per la sezione.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	Il valore di **StartDateTime** è l'orario di inizio per la sezione con errori/problemi di cui si è preso nota nel passaggio precedente. La data e ora dovrebbe essere racchiusa tra virgolette doppie.
5. Si otterrà l'output con informazioni dettagliate sull'errore (simile al seguente):

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. È possibile eseguire il cmdlet **Save-AzureDataFactoryLog** con il valore ID visualizzato nell'output sopra e scaricare i file di log usando l'opzione del cmdlet **-DownloadLogs**.



[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 

<!---HONumber=July15_HO3-->