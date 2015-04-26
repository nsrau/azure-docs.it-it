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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Risolvere i problemi di Data factory
È possibile risolvere i problemi relativi a Data factory di Azure usando il portale di Azure oppure i cmdlet di Azure PowerShell. Questo argomento contiene procedure dettagliate che illustrano come usare il portale di Azure per risolvere rapidamente eventuali errori che possono verificarsi con Data factory. 

## Contenuto dell'articolo

- [Procedura dettagliata: Risoluzione dei problemi relativi a errori di copia dei dati](#copywalkthrough)
- [Procedura dettagliata: Risoluzione dei problemi relativi a errori dei processi Hive/Pig](#pighivewalkthrough)

## <a name="copywalkthrough"></a>Procedura dettagliata: Risoluzione dei problemi relativi a errori di copia dei dati
In questa procedura dettagliata si introdurrà un errore nell'esercitazione dell'articolo Introduzione a Data factory e si apprenderà come usare il portale di Azure per risolvere l'errore.

### Prerequisiti
1. Completare l'esercitazione nell'articolo [Introduzione a Data factory di Azure][adfgetstarted].
2. Verificare che **ADFTutorialDataFactory** produca dati nella tabella **emp** del database SQL di Azure.  
3. Eliminare quindi la tabella **emp** (**drop table emp**) dal database SQL di Azure. Ciò introdurrà un errore.
4. Eseguire il comando seguente in **Azure PowerShell** per aggiornare il periodo attivo della pipeline in modo che cerchi di scrivere i dati nella tabella **emp** che non esiste più.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline
	
	> [WACOM.NOTE] Sostituire il valore di <b>StartDateTime</b> con il giorno corrente e il valore di <b>EndDateTime</b> con il giorno seguente. 


### Usare il portale di anteprima di Azure per risolvere l'errore

1.	Accedere al [portale di anteprima di Azure][azure-preview-portal]. 
2.	Fare clic su **ADFTutorialDataFactory** dalla **schermata iniziale**. Se il collegamento a Data factory non è visualizzato nella **schermata iniziale**, fare clic sull'hub **SFOGLIA**, quindi scegliere **Tutto**. Fare clic su **Istanze di Data factory** nel pannello **Sfoglia**, quindi fare clic su **ADFTutorialDataFactory**.
3.	Si noti la visualizzazione della dicitura **Con errori** nel riquadro **Set di dati**. Fare clic su **Con errori**. Viene visualizzato il pannello **Set di dati con errori**.

	![Data Factory with Errors link][image-data-factory-troubleshoot-with-error-link]

4. Nel pannello **Set di dati** con errori fare clic su **EmpSQLTable** per visualizzare il riquadro **TABELLA**.	

	![Datasets with errors blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Nel pannello **TABELLA** saranno visibili le sezioni relative agli errori, ad esempio sezioni con un errore nell'elenco **Sezioni problematiche** nella parte inferiore dello schermo. Saranno anche visibili eventuali sezioni recenti con errori nell'elenco **Sezioni recenti**. Fare clic su una sezione nell'elenco **Sezioni problematiche**. 

	![Table blade with problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Se si fa clic su **Sezioni problematiche** (non su un problema specifico) verrà visualizzato il pannello **SEZIONI DI DATI**, fare clic su una **sezione problematica specifica** per vedere la **SEZIONE DI DATI** corrispondente alla sezione di dati specificata.

6. Nel pannello **SEZIONE DI DATI** per **EmpSQLTable**verranno visualizzate tutte le **esecuzioni di attività** per la sezione nell'elenco in basso. Fare clic su un'**esecuzione attività** dall'elenco non riuscito.

	![Data Slice blade with active runs][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Nel pannello **Dettagli esecuzione attività** per l'esecuzione attività selezionata saranno visibili i dettagli relativi all'errore. In questo scenario verrà visualizzato un messaggio simile al seguente: **Nome oggetto non valido 'emp'**.

	![Activity run details with an error][image-data-factory-troubleshoot-activity-run-with-error]

Per risolvere il problema, creare la tabella **emp** usando lo script SQL script nell'articolo [Introduzione a Data factory][adfgetstarted].


### Usare i cmdlet di Azure PowerShell per risolvere l'errore
1.	Avviare **Azure PowerShell**. 
2.	Passare alla modalità **AzureResourceManager** perché i cmdlet di Data factory sono disponibili unicamente in questa modalità.

         
		switch-azuremode AzureResourceManager

3. Eseguire il comando Get-AzureDataFactorySlice per vedere le sezioni e i relativi stati. Verrà visualizzata una sezione con lo stato: Non riuscito.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [WACOM.NOTE] Sostituire **StartDateTime** con il valore StartDateTime specificato per **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Notare l'ora di **Inizio** nella sezione problematica (quella con lo **Stato** impostato su **Non riuscito**) nell'output. 
4. Eseguire ora il cmdlet **Get-AzureDataFactoryRun** per ottenere i dettagli sull'esecuzione dell'attività per la sezione.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	Il valore di **StartDateTime** è l'orario di inizio per la sezione problematica di cui si è preso nota nel passaggio precedente. La data e ora dovrebbe essere racchiusa tra virgolette doppie.
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

 

## <a name="pighavewalkthrough"></a>Procedura dettagliata: Risoluzione dei problemi relativi a errori dei processi Hive/Pig
Questa procedura dettagliata illustra come risolvere un errore con i processi Hive/Pig usando il portale di anteprima di Azure e Azure PowerShell. 


### Procedura dettagliata: Usare il portale di Azure per risolvere un errore di un processo Pig o Hive
In questo scenario, il set di dati è in stato di errore a causa di un errore nell'elaborazione dell'Hive in un cluster HDInsight.

1. Fare clic su **Con errori** nel riquadro **Set di dati** della home page **DATA FACTORY**.

	![With errors link on Datasets tile][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Nel pannello **Set di dati con errori** fare clic sulla **tabella** che interessa.

	![Datasets with errors blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Nel pannello **TABELLA** fare clic sulla **sezione problematica** con **STATO** impostato su **Non riuscito**.

	![Table with problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Nel pannello **SEZIONE DI DATI** fare clic sull'**esecuzione attività** non riuscita.

	![Data slice with failed runs][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Nel pannello **DETTAGLI ESECUZIONE ATTIVITÀ** è possibile scaricare i file associati all'elaborazione di HDInsight. Fare clic su **Scarica** in corrispondenza di **Stato/stderr** per scaricare il file di log degli errori che contiene i dettagli dell'errore stesso.

	![Activity run details with download link][image-data-factory-troubleshoot-activity-run-details]

    
### Procedura dettagliata: Usare Azure PowerShell per risolvere un errore di un processo Pig o Hive
1.	Avviare **Azure PowerShell**. 
2.	Passare alla modalità **AzureResourceManager** perché i cmdlet di Data factory sono disponibili unicamente in questa modalità.

         
		switch-azuremode AzureResourceManager

3. Eseguire il comando Get-AzureDataFactorySlice per vedere le sezioni e i relativi stati. Verrà visualizzata una sezione con lo stato: Non riuscito.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [WACOM.NOTE] Sostituire **StartDateTime** con il valore StartDateTime specificato per **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Notare l'ora di **Inizio** nella sezione problematica (quella con lo **Stato** impostato su **Non riuscito**) nell'output. 
4. Eseguire ora il cmdlet **Get-AzureDataFactoryRun** per ottenere i dettagli sull'esecuzione dell'attività per la sezione.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	Il valore di **StartDateTime** è l'orario di inizio per la sezione problematica di cui si è preso nota nel passaggio precedente. La data e ora dovrebbe essere racchiusa tra virgolette doppie.
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

6. È possibile eseguire il cmdlet **Save-AzureDataFactoryLog** con il valore di ID visualizzato nell'output sopra e scaricare i file di log usando l'opzione del cmdlet **-DownloadLogs**.

## Suggerimenti per la risoluzione dei problemi

### Non è possibile connettersi all'istanza di SQL Server locale 
Verificare che SQL Server sia raggiungibile dal computer in cui è installato il gateway.


1. Eseguire il ping del computer in cui è installato SQL Server
2. Nel computer in cui è installato il gateway, provare a connettersi all'istanza di SQL Server con le credenziali specificate nel portale di Azure usando SQL Server Management Studio (SSMS).

### L'operazione di copia non riesce
1. Avviare il programma per la gestione della configurazione del gateway di gestione dei dati nel computer in cui è stato installato il gateway stesso. Verificare che il **nome del gateway** sia impostato sul nome del gateway logico nel **portale di azure**, che lo **stato della chiave** del gateway sia **registrata** e che lo **stato del servizio** sia **avviato**. 
2. Avviare il **Visualizzatore eventi**. Espandere **Registri applicazioni e servizi** e fare clic su **Gateway di gestione dati**. Verificare se sono presenti errori correlati al Gateway di gestione di dati. 



## Vedere anche

Articolo | Descrizione
------ | ---------------
[Consentire alle pipeline di usare dati locali][use-onpremises-datasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un database SQL Server locale a un BLOB di Azure.
[Usare Pig e Hive con Data factory][use-pig-and-hive-with-data-factory] | Questo articolo contiene una procedura dettagliata che mostra come usare l'attività HDInsight per eseguire uno script hive/pig per elaborare i dati di input in modo da generare i dati di output. 
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una procedura dettagliata end-to-end che mostra come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Monitorare e gestire Data factory di Azure con PowerShell][monitor-manage-using-powershell] | Questo articolo descrive come monitorare Data factory di Azure con i cmdlet di Azure PowerShell. 
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come risolvere i problemi di Data factory di Azure. È possibile provare la procedura dettagliata di questo articolo in ADFTutorialDataFactory introducendo un errore (eliminando la tabella nel database SQL di Azure). 
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 
[Informazioni di riferimento per i cmdlet di Data factory di Azure][cmdlet-reference] | Questo contenuto di riferimento include i dettagli su tutti i **cmdlet di Data factory**.

[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png

<!--HONumber=46--> 

<!--HONumber=46--> 
