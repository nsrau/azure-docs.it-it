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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Risolvere i problemi di Data factory
È possibile risolvere i problemi relativi a Data factory di Azure usando il portale di Azure oppure i cmdlet di Azure PowerShell. Questo argomento contiene procedure dettagliate che illustrano come usare il portale di Azure per risolvere rapidamente eventuali errori che possono verificarsi con Data factory.

## Problema: Non è possibile eseguire i cmdlet di Factory di dati
Per risolvere questo problema, passare alle modalità Azure per **AzureResourceManager**:

Avviare **Azure PowerShell** ed eseguire il comando seguente per passare al **AzureResourceManager** modalità. I cmdlet di Azure dati Factory sono disponibili nel **AzureResourceManager** modalità.

         switch-azuremode AzureResourceManager

## Problema: Errore non autorizzato quando si esegue un cmdlet di Factory di dati
Probabilmente non si utilizza l'account Azure destra o la sottoscrizione con Azure PowerShell. Utilizzare i cmdlet seguenti per selezionare l'account Azure destra e la sottoscrizione per l'utilizzo con Azure PowerShell.

1. Aggiungere-AzureAccount - utilizzare l'ID utente corretto e la password
2. Get-AzureSubscription - consente di visualizzare tutte le sottoscrizioni per l'account. 
3. Select-AzureSubscription <subscription name> -Selezionare l'abbonamento più adatto. Utilizzare la stessa che consente di creare una factory di dati nel portale di anteprima di Azure.

## Problema: Impossibile avviare l'installazione Express dati Gateway dal portale di Azure
L'installazione di Express per il Gateway di dati richiede Internet Explorer o un browser compatibile con Microsoft ClickOnce. Se Impossibile avviare l'installazione di Express, è possibile

1. Se si verifica un errore con altri browser, passare a Internet Explorer. Oppure
2. Utilizzare i collegamenti "Installazione manuale" sullo stesso blade nel portale per eseguire l'installazione e quindi copiare la chiave fornita sullo schermo e incollare quando la configurazione di Gateway di gestione dati è pronta. Se non consente di avviare, cercare il menu di avvio "Gateway di gestione dati di Microsoft" e incollare la chiave quando si avvia. 


## Problema: Impossibile avviare Gestione credenziali dal portale di Azure
Quando imposta o aggiorna il servizio SQL Server collegato tramite il portale di Azure, la gestione di credenziali verrà avviata l'applicazione per garantire la sicurezza. È richiesto Internet Explorer o un browser compatibile con Microsoft ClickOnce. Se si verifica un errore con altri browser, è possibile passare a Internet Explorer.

## Problema: Impossibile connettersi al Server SQL locale 
Verificare che SQL Server sia raggiungibile dal computer in cui è installato il gateway. Nel computer in cui è installato il gateway, è possibile

1. Eseguire il ping del computer dove è installato SQL Server. Oppure
2. Provare a connettersi all'istanza di SQL Server utilizzando le credenziali specificate nel portale di Azure utilizzando SQL Server Management Studio (SSMS).


## Problema: Intervalli di Input sono in stato PendingExecution o PendingValidation per tutte

Le sezioni potrebbero essere **PendingExecution** o **PendingValidation** stato a causa di una serie di motivi e uno dei motivi comuni è che il **waitOnExternal** proprietà non viene specificata nel **disponibilità** sezione della prima tabella/set di dati della pipeline. Qualsiasi set di dati che viene generato all'esterno dell'ambito della Factory di dati di Azure deve essere contrassegnato con **waitOnExternal** proprietà sotto **disponibilità** sezione. Ciò indica che i dati esterni e non è supportata da qualsiasi pipeline all'interno della factory di dati. Le sezioni di dati vengono contrassegnate come **Pronto** quando i dati sono disponibili nell'archivio del rispettivo.

Vedere l'esempio riportato di seguito per l'utilizzo del **waitOnExternal** proprietà. È possibile specificare **{} waitOnExternal** senza impostare i valori per le proprietà nella sezione in modo che vengono utilizzati i valori predefiniti.

Vedere l'argomento di tabelle in [JSON Scripting Reference][json-scripting-reference] per ulteriori informazioni su questa proprietà.
	
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

 Per risolvere l'errore, aggiungere il **waitOnExternal** sezione alla definizione della tabella di input JSON e ricreare la tabella.

## Problema: Ha esito negativo dell'operazione di copia ibrido
Per ulteriori informazioni:

1. Avviare Gestione configurazione di Gateway di gestione dati nel computer in cui è stato installato il gateway. Verificare che il **nome Gateway** è impostata per il nome logico del gateway sul **portale Azure**, **stato chiave del Gateway** è **registrati** e **stato del servizio** è **avviato**. 
2. Avviare **Visualizzatore eventi**. Espandere **registri applicazioni e servizi** e fare clic su **Gateway di gestione dati**. Verificare se sono presenti errori correlati al Gateway di gestione di dati. 

## Problema: Su richiesta HDInsight Provisioning ha esito negativo con errore

Quando si utilizza un servizio di tipo HDInsightOnDemandLinkedService collegato, è necessario specificare un linkedServiceName che punta all'archiviazione Blob di Azure. Questo account di archiviazione verrà utilizzato per copiare tutti i registri e file di supporto per il cluster HDInsight su richiesta. L'attività che esegue il provisioning su richiesta in HDInsight in alcuni casi potrebbe non riuscire con l'errore seguente:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Questo errore indica generalmente che il percorso dell'account di archiviazione specificato nel linkedServiceName non è nella stessa posizione del data center come dove avviene il provisioning di HDInsight. Ad esempio, se il percorso della Factory di dati di Azure è occidentale degli Stati Uniti e il provisioning di HDInsight su richiesta avviene Stati Uniti occidentali, ma la posizione dell'account di archiviazione blob di Azure è impostato su Stati Uniti orientali, il provisioning su richiesta avrà esito negativo.

È inoltre disponibile un secondo additionalLinkedServiceNames proprietà JSON cui ulteriori account di archiviazione può essere specificato in HDInsight su richiesta. Gli account di archiviazione collegato aggiuntivo devono essere nello stesso percorso del cluster HDInsight o avrà esito negativo con lo stesso errore.



## Problema: Errore di attività personalizzata
Quando si utilizza un'attività personalizzata in Azure dati Factory (tipo di attività pipeline CustomActivity), l'applicazione personalizzata viene eseguito nel servizio collegato specificato in HDInsight come mappa di streaming solo processo MapReduce.

Quando viene eseguita l'attività personalizzata, Factory di dati di Azure sarà in grado di acquisire l'output dal cluster HDInsight e salvarlo nel *adfjobs* contenitore di archiviazione nell'account di archiviazione Blob di Azure. In caso di errore, è possibile leggere il testo da **stderr** file di testo di output dopo che si è verificato un errore. I file sono accessibili e leggibile dal portale di Azure stesso nel web browser o tramite strumenti di esplorazione di archiviazione per accedere ai file conservati direttamente nel contenitore di archiviazione nell'archiviazione Blob di Azure.

Per enumerare e leggere i log per una particolare attività personalizzata, è possibile utilizzare uno di tali procedure illustrate più avanti in questa pagina. Riepilogo:

1.  Nel portale di Azure **Sfoglia** per individuare la Factory di dati.
2.  Utilizzare il **diagramma** pulsante per visualizzare il diagramma di factory di dati e fare clic sui **Dataset** tabella che segue la specifica **Pipeline** con l'attività personalizzata. 
3.  Nel **tabella** blade, fare clic sulla porzione di interesse per il **sezioni problema** per l'intervallo di tempo essere esaminati.
4.  I dettagli **sezione di dati** verrà visualizzato il blade e può elencare più **esegue attività** della sezione. Fare clic su un **attività** dall'elenco. 
5.  Il **dettagli di esecuzione di attività** verrà visualizzato il blade. Verranno visualizzate la **messaggio di errore** al centro il blade e numerosi **file di Log** elencati nella parte inferiore del blade affiliata a tale attività in esecuzione.
	- Sistema/log-0.log
	- Stato
	- Lo stato e di uscita
	- Stato/stderr
	- Stato/stdout

6. Fare clic sul primo **file di Log** elemento nell'elenco e il log verrà aperto in un nuovo blade con il testo completo visualizzato per l'utente per la lettura. Esaminare il testo di ogni log facendo clic su ciascuno di essi. Verrà aperto il pannello del Visualizzatore di testo. È possibile scegliere il **Download** pulsante per scaricare il file di testo per la visualizzazione offline facoltativo.

Un **errore comune** da un'attività personalizzata è l'esecuzione del pacchetto non riuscito con codice di uscita '1'. Vedere ' wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/stato/stderr ' per ulteriori dettagli.

Per visualizzare ulteriori dettagli per questo tipo di errore, aprire il **stderr** file. Un errore comune visualizzato vi è una condizione di timeout come questo: INFO mapreduce. Processo: Id attività: attempt_1424212573646_0168_m_000000_0, stato: non è riuscito AttemptID:attempt_1424212573646_0168_m_000000_0 timeout dopo 600 secondi

Lo stesso errore venga visualizzato più volte, se il processo di tentativi di esecuzione di tre volte, ad esempio, nell'arco di 30 minuti.

Questo errore di timeout indica che un secondo 600 (10 minuti) è verificato il timeout. In genere ciò significa che l'applicazione .net personalizzata non ha rilasciato un aggiornamento di stato per 10 minuti. Se l'applicazione è sporgente o bloccati in attesa su un elemento di timeout è un meccanismo di sicurezza per impedire che in attesa all'infinito e ritardare la pipeline di Azure dati Factory troppo lungo, 10 minuti.

Questo timeout ha origine nella configurazione del cluster HDInsight è collegata l'attività personalizzata. L'impostazione è **mapred.task.timeout**, impostato 600000 millisecondi, come illustrato in questo caso le impostazioni predefinite Apache: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

È possibile ignorare questa impostazione predefinita modificando le impostazioni predefinite al momento del provisioning del cluster di provisioning di HDInsight. Quando si utilizza la Factory di dati di Azure e **HDInsight richiesta** collegato del servizio, è possibile aggiungere la proprietà JSON quasi le proprietà HDInsightOnDemandLinkedService JSON. Ad esempio, è possibile aumentare il valore su 20 minuti di utilizzo di questa proprietà JSON.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Per ulteriori informazioni di contesto e un esempio completo di JSON per modificare questi mapping ridurre vedere proprietà di configurazione nell'esempio n. 3 nella documentazione di MSDN https://msdn.microsoft.com/library/azure/dn893526.aspx

## Problema: PowerShell richiesta ha esito negativo con errore 400 richiesta non valida "Nessun provider di risorse registrato trovato..."

A partire da 10 marzo 2015 Factory dati di Azure PowerShell anteprima privata versioni precedenti 2014-05-01-anteprima, 2014-07-01-anteprima e 2014-08-01-anteprima verranno interrotto. È consigliabile utilizzare la versione più recente di cmdlet del file ADF, fanno ora parte di Azure PowerShell di Download, ad esempio il download dal seguente URL http://go.microsoft.com/?linkid=9811175&clcid=0x409

Se si utilizzano le versioni obsolete di Azure PowerShell SDK è possibile che venga visualizzato i seguenti errori:

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


## <a name="copywalkthrough"></a> Procedura dettagliata: Risoluzione dei problemi di un errore di copia dei dati
In questa procedura dettagliata si introdurrà un errore nell'esercitazione dell'articolo Introduzione a Data factory e si apprenderà come usare il portale di Azure per risolvere l'errore.

### Prerequisiti
1. Completare l'esercitazione nel [iniziare a utilizzare Azure dati Factory][adfgetstarted] articolo.
2. Verificare che il **ADFTutorialDataFactory** produce dati nel **emp** tabella nel Database di SQL Azure.  
3. Ora, eliminare il **emp** tabella (* * eliminazione tabella emp * *) dal Database di SQL Azure. Ciò introdurrà un errore.
4. Eseguire il comando seguente **Azure PowerShell** per aggiornare il periodo attivo per la pipeline in modo che il tentativo di scrivere i dati per il **emp** tabella, non esiste più.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	> [AZURE.NOTE]Sostituire <b>valore StartDateTime</b> valore con il giorno corrente e <b>EndDateTime</b> valore con il giorno successivo.


### Usare il portale di anteprima di Azure per risolvere l'errore

1.	Account di accesso [portale Azure Preview][azure-preview-portal]. 
2.	Fare clic su **ADFTutorialDataFactory** dal **Startboard**. Se non viene visualizzata la factory di dati sul collegamento la **Startboard**, fare clic su **Sfoglia** hub e fare clic su **tutto**. Fare clic su **factory di dati...** nel **Sfoglia** blade e fare clic su **ADFTutorialDataFactory**.
3.	Si noti che è possibile vedere **con errori** sul **set di dati** riquadro. Fare clic su **con errori**. Dovrebbe essere visualizzato **set di dati con errori** blade.

	![Factory di dati con collegamento degli errori][image-data-factory-troubleshoot-with-error-link]

4. Nel **set di dati** con blade di errori, fare clic su **EmpSQLTable** per visualizzare il **tabella** blade.

	![Set di dati con blade errori][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Nel **tabella** blade, si dovrebbero vedere le sezioni del problema, vale a dire sezioni con un errore nel **sezioni problema** elenco nella parte inferiore. È inoltre possibile vedere le sezioni di recente con errori nel **recenti sezioni** elenco. Fare clic su una sezione nel **sezioni problema** elenco.

	![Blade di tabella con sezioni di problema][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Se si fa clic su **sezioni problema** (non su uno specifico problema), verrà visualizzato il **sezioni di dati** blade e quindi fare clic su un **sezione problema specifico** per visualizzare il **sezione di dati** diapositiva per la sezione di dati selezionato.

6. Nel **sezione di dati** blade per **EmpSQLTable**, vengono visualizzati tutti **esegue attività** della sezione nell'elenco nella parte inferiore. Fare clic su un **attività eseguire** nell'elenco non è riuscita.

	![Blade porzione di dati con esecuzioni attive][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Nel **dettagli di esecuzione di attività** blade per l'attività di esecuzione è selezionata, vengono visualizzati i dettagli sull'errore. In questo scenario, vedere: **nome oggetto non valido 'emp'**.

	![Dettagli dell'esecuzione con un errore di attività][image-data-factory-troubleshoot-activity-run-with-error]

Per risolvere questo problema, creare il **emp** Crea script per tabella utilizzando l'istruzione SQL da [Introduzione a Data Factory][adfgetstarted] articolo.


### Usare i cmdlet di Azure PowerShell per risolvere l'errore
1.	Avviare **Azure PowerShell**. 
2.	Passare a **AzureResourceManager** modalità cmdlet Factory dati sono disponibili solo in questa modalità.

         
		switch-azuremode AzureResourceManager

3. Eseguire il comando Get-AzureDataFactorySlice per vedere le sezioni e i relativi stati. Si noterà una sezione con lo stato: non è riuscita.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [AZURE.NOTE]Sostituire **valore StartDateTime** con il valore StartDateTime valore specificato per il **Set AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Nota il **avviare** tempo per la porzione del problema (con sezioni **stato** impostato su **Failed**) nell'output. 
4. A questo punto, eseguire il **Get AzureDataFactoryRun** per ottenere informazioni sulle attività eseguire per la sezione.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	Il valore di **valore StartDateTime** è l'ora di inizio per la porzione di errore/problema annotato nel passaggio precedente. La data e ora dovrebbe essere racchiusa tra virgolette doppie.
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

 

## <a name="pighivewalkthrough"></a> Procedura dettagliata: Risoluzione dei problemi di un errore di elaborazione/Pig Hive
Questa procedura dettagliata illustra come risolvere un errore con i processi Hive/Pig usando il portale di anteprima di Azure e Azure PowerShell.


### Procedura dettagliata: Utilizzo di portale di Azure per risolvere un errore di elaborazione Pig o Hive
In questo scenario, il set di dati è in stato di errore a causa di un errore nell'elaborazione dell'Hive in un cluster HDInsight.

1. Fare clic su **con errori** su **set di dati** riquadro il **dati FACTORY** homepage.

	![Con collegamento di errori nella sezione set di dati][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Nel **set di dati con errori** blade, scegliere il **tabella** che si è interessati.

	![Set di dati con blade errori][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Nel **tabella** blade, fare clic sul **sezione problema** con **stato** impostato su **Failed**.

	![Tabella con le sezioni del problema][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Nel **sezione di dati** blade, fare clic sui **attività eseguita** non riuscita.

	![Sezione di dati con esecuzioni non riuscite][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Nel **dettagli di esecuzione di attività** blade, è possibile scaricare i file associati con l'elaborazione di HDInsight. Fare clic su **Download** per **stato/stderr** per scaricare il file di log di errore che contiene i dettagli sull'errore.

	![Dettagli dell'esecuzione con un collegamento di download di attività][image-data-factory-troubleshoot-activity-run-details]

    
### Procedura dettagliata: Utilizzo di Azure PowerShell per risolvere un errore di elaborazione Pig o Hive
1.	Avviare **Azure PowerShell**. 
2.	Passare a **AzureResourceManager** modalità cmdlet Factory dati sono disponibili solo in questa modalità.

         
		switch-azuremode AzureResourceManager

3. Eseguire il comando Get-AzureDataFactorySlice per vedere le sezioni e i relativi stati. Si noterà una sezione con lo stato: non è riuscita.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [AZURE.NOTE]Sostituire **valore StartDateTime** con il valore StartDateTime valore specificato per il **Set AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Nota il **avviare** tempo per la porzione del problema (con sezioni **stato** impostato su **Failed**) nell'output. 
4. A questo punto, eseguire il **Get AzureDataFactoryRun** per ottenere informazioni sulle attività eseguire per la sezione.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	Il valore di **valore StartDateTime** è l'ora di inizio per la porzione di errore/problema annotato nel passaggio precedente. La data e ora dovrebbe essere racchiusa tra virgolette doppie.
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

6. È possibile eseguire **Salva AzureDataFactoryLog** cmdlet con valore Id vedere dall'output riportato sopra e scaricare il log di file utilizzando il **- DownloadLogs** opzione per il cmdlet.



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

<!---HONumber=GIT-SubDir--> 