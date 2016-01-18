<properties 
	pageTitle="Monitorare e gestire le pipeline di Data factory di Azure" 
	description="Informazioni su come usare il portale di Azure classico e Azure PowerShell per monitorare e gestire le istanze di Data factory di Azure e le pipeline create." 
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
	ms.date="01/04/2016" 
	ms.author="spelluru"/>

# Monitorare e gestire le pipeline di Data factory di Azure
Il servizio Data factory offre una visione completa dei servizi di archiviazione, elaborazione e spostamento dei dati e consente di valutare rapidamente l'integrità della pipeline di dati end-to-end, individuare i problemi e adottare le eventuali misure correttive necessarie. È anche possibile tenere visivamente traccia della derivazione dei dati e delle relazioni tra i dati delle diverse origini, nonché visualizzare una presentazione cronologica completa di esecuzione dei processi, integrità del sistema e dipendenze da un unico dashboard di monitoraggio.

Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline. Contiene anche informazioni su come creare avvisi e ricevere notifiche sugli errori.

## Informazioni sulle pipeline e sugli stati delle attività
Con il portale di Azure, è possibile visualizzare la data factory come diagramma, visualizzare le attività in una pipeline, visualizzare i set di dati di input e output e altro ancora. Questa sezione illustra anche come avviene la transizione di una sezione da uno stato a un altro.

### Passare alla data factory
1.	Accedere al [portale di Azure](http://portal.azure.com).
2.	Fare clic su **Esplora tutto** e selezionare **Data factory**.
	
	![Esplora tutto -> Data factory](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

	Dovrebbero essere visualizzate tutte le data factory presenti nel pannello **Data factory**. 
4. Nel pannello Data factory selezionare la data factory desiderata per visualizzare la relativa home page (pannello **Data factory**).

	![Pannello Data factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### Vista diagramma della data factory
La vista diagramma di una data factory fornisce un'unica console da cui monitorare e gestire la data factory e i relativi asset.

Fare clic su **Diagramma** nella home page della data factory precedente per visualizzare la vista diagramma della data factory.

![Vista diagramma](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

È possibile eseguire lo zoom avanti, lo zoom indietro e lo zoom al 100%, adattare alla finestra, bloccare il layout del diagramma, posizionare automaticamente pipeline e tabelle e visualizzare la derivazione, mostrando gli elementi upstream e downstream degli elementi selezionati.
 

### Attività all'interno di una pipeline 
1. Fare clic con il pulsante destro del mouse sulla pipeline e scegliere **Apri pipeline** per visualizzare tutte le attività della pipeline, oltre ai set di dati di input e output relativi a queste attività. Ciò è utile quando la pipeline è costituita da più di 1 attività e si desidera conoscere la derivazione operativa di una singola pipeline.

	![Menu Apri pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)	 
2. Nell'esempio seguente sono visibili due attività nella pipeline con gli input e gli output. L'attività denominata **JoinData** di tipo attività Hive HDInsight e **EgressDataAzure** di tipo attività di copia sono nella stessa pipeline di esempio. 
	
	![Attività all'interno di una pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Per tornare alla Home page di Data factory, fare clic sul collegamento Data factory nella barra di navigazione in alto a sinistra.

	![Ritorno a Data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### Visualizzare lo stato di ogni attività all'interno di una pipeline
Per visualizzare lo stato corrente di un'attività, visualizzare lo stato di uno dei set di dati generati dall'attività.

Ad esempio: nell'esempio seguente **BlobPartitionHiveActivity** è stata eseguita correttamente e ha generato un set di dati denominato **PartitionedProductsUsageTable** con lo stato **Pronto**.

![Stato della pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Facendo doppio clic su **PartitionedProductsUsageTable** nella vista diagramma, verranno presentate tutte le sezioni generate da esecuzioni diverse dell'attività all'interno di una pipeline. Si noti che **BlobPartitionHiveActivity** è stata eseguita correttamente ogni mese negli ultimi 8 mesi e ha generato le sezioni con lo stato **Ready**.

Le sezioni dei set di dati nella data factory possono avere uno degli stati seguenti:

<table>
<tr>
	<th align="left">Stato</th><th align="left">Sottostato</th><th align="left">Descrizione</th>
</tr>
<tr>
	<td rowspan="8">Waiting</td><td>ScheduleTime</td><td>Non è il momento di eseguire la sezione.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Le dipendenze upstream non sono pronte.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Le risorse di calcolo non sono disponibili.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Tutte le istanze di attività sono occupate nell’esecuzione di altre sezioni.</td>
</tr>
<tr>
<td>ActivityResume</td><td>L’attività è sospesa e non è possibile eseguire le sezioni fino a quando non viene ripresa.</td>
</tr>
<tr>
<td>Retry</td><td>L'esecuzione dell'attività verrà ritentata.</td>
</tr>
<tr>
<td>Convalida</td><td>La convalida non è ancora stata avviata.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>In attesa della riesecuzione della convalida.</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>Convalida in corso.</td><td>Convalida in esecuzione.</td>
</tr>
<td></td>
<td>La sezione è in corso.</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>TimedOut</td><td>L'esecuzione ha richiesto più tempo di quello consentito dall'attività.</td>
</tr>
<tr>
<td>Canceled</td><td>Annullato dall'utente.</td>
</tr>
<tr>
<td>Convalida</td><td>Convalida non riuscita.</td>
</tr>
<tr>
<td></td><td>Generazione e/o convalida della sezione non riuscita.</td>
</tr>
<td>Ready</td><td></td><td>La sezione è pronta per essere utilizzata.</td>
</tr>
<tr>
<td>Skipped</td><td></td><td>La sezione non viene elaborata.</td>
</tr>
<tr>
<td>Nessuno</td><td></td><td>Una sezione che solitamente esiste con uno stato differente, ma che è stata reimpostata.</td>
</tr>
</table>



Per visualizzare i dettagli di una sezione, fare clic sulla voce di una sezione nel pannello **Sezioni aggiornate di recente**.

![Dettagli della sezione](./media/data-factory-monitor-manage-pipelines/slice-details.png)
 
Se la sezione è stata eseguita più volte, verranno visualizzate più righe nell'elenco **Esecuzioni attività**.

![Esecuzioni di attività per una sezione](./media/data-factory-monitor-manage-pipelines/activity-runs-for-a-slice.png)

Per visualizzare i dettagli di un'esecuzione attività, fare clic sulla voce di un'esecuzione nell'elenco **Esecuzioni attività**. Verranno presentati tutti i file di log con eventuali messaggi di errore. Ciò è molto utile per visualizzare i log ed eseguirne il debug senza dover uscire dalla data factory.

![Dettagli esecuzione attività](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Se lo stato della sezione non è **Pronto**, sarà possibile visualizzare le sezioni upstream che non sono pronte e bloccano l'esecuzione della sezione corrente nell'elenco **Sezioni upstream non pronte**. Ciò è molto utile quando lo stato della sezione è **In attesa** e si desidera conoscere le dipendenze upstream su cui la sezione è in attesa.

![Sezioni upstream non pronte](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### Diagramma di stato del set di dati
Una volta che una data factory è stata distribuita e le pipeline hanno un periodo attivo valido, le sezioni del set di dati passano da uno stato a un altro. Attualmente lo stato delle sezioni segue il diagramma di stato seguente:

![Diagramma di stato](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Il flusso di transizione di stato del set dati nella data factory è il seguente: Waiting-> In-Progress/In-Progress (Validating) -> Ready/Failed

Lo stato iniziale delle sezioni è **In attesa** in modo da soddisfare le condizioni preliminari prima dell'esecuzione. In seguito inizia l'esecuzione dell'attività e la sezione passa allo stato **In corso**. Lo stato della sezione passa a **Pronto** o **Non riuscito** a seconda che l'esecuzione dell'attività venga completata o meno.

L'utente può reimpostare la sezione in modo che dallo stato **Pronto** o **Non riuscito** torni allo stato **In attesa**. L'utente può anche impostare lo stato della sezione su **Ignora** per impedire l'esecuzione dell'attività e l'elaborazione della sezione.


## Gestire le pipeline
È possibile gestire le pipeline usando Azure PowerShell. Ad esempio, è possibile sospendere e riprendere le pipeline eseguendo i cmdlet di Azure PowerShell.

### Sospendere e riprendere le pipeline
È possibile sospendere le pipeline usando il cmdlet di PowerShell **Suspend-AzureRmDataFactoryPipeline**. Ciò è utile quando si individua un problema relativo ai dati e non si desidera eseguire le pipeline per elaborare ulteriormente i dati fino alla risoluzione del problema.

Ad esempio: nella schermata seguente è stato identificato un problema relativo a **PartitionProductsUsagePipeline** nella data factory **productrecgamalbox1dev** e si desidera sospendere la pipeline.

![Pipeline da sospendere](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Eseguire il comando di PowerShell seguente per sospendere **PartitionProductsUsagePipeline**.

	Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Ad esempio:

	Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

Una volta risolto il problema relativo a **PartitionProductsUsagePipeline**, la pipeline sospesa può essere ripresa eseguendo il comando di PowerShell seguente.

	Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Ad esempio:

	Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## Eseguire il debug delle pipeline
Tramite il portale di Azure classico e Azure PowerShell, Data factory di Azure offre funzionalità avanzate per il debug e la risoluzione dei problemi relativi alle pipeline.

### Trovare gli errori in una pipeline
Se l'esecuzione di un'attività in una pipeline non riesce, il set di dati generato dalla pipeline è in uno stato di errore. È possibile eseguire il debug e risolvere i problemi relativi agli errori in Data factory di Azure usando i meccanismi seguenti.

#### Usare il portale di Azure classico per eseguire il debug di un errore:

1.	Fare clic su **Con errori** nel riquadro **Set di dati** della home page data factory.
	
	![Riquadro Set di dati con errore](./media/data-factory-monitor-manage-pipelines/datasets-tile-with-errors.png)
2.	Nel pannello **Set di dati con errori** fare clic sulla tabella che interessa.

	![Pannello Set di dati con errori](./media/data-factory-monitor-manage-pipelines/datasets-with-errors-blade.png)
3.	Nel pannello **TABELLA** fare clic sulla sezione con errori con **STATO** impostato su **Operazione non riuscita**.

	![Pannello Tabella con sezione con errori](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4.	Nel pannello **SEZIONE DI DATI** fare clic sull'esecuzione dell’attività non riuscita.
	
	![Sezione dati con un errore](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5.	Nel pannello **DETTAGLI ESECUZIONE ATTIVITÀ** è possibile scaricare i file associati all'elaborazione di HDInsight. Fare clic su Scarica in corrispondenza di Stato/stderr per scaricare il file di log degli errori che contiene i dettagli dell'errore stesso.

	![Pannello Dettagli esecuzione attività con errore](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)

#### Usare PowerShell per eseguire il debug di un errore
1.	Avviare **Azure PowerShell**.
3.	Eseguire il comando **Get-AzureRmDataFactorySlice** per vedere le sezioni e i relativi stati. Verrà visualizzata una sezione con lo stato **Non riuscito**.

		Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Ad esempio:


		Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Sostituire **StartDateTime** con il valore StartDateTime specificato per Set-AzureRmDataFactoryPipelineActivePeriod.
4. Eseguire ora il cmdlet **Get-AzureRmDataFactoryRun** per ottenere i dettagli sull'esecuzione dell'attività per la sezione.

		Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-
		DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
		<DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Ad esempio:


		Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	Il valore di StartDateTime è l'orario di inizio per la sezione con errori/problemi di cui si è preso nota nel passaggio precedente. La data e ora dovrebbe essere racchiusa tra virgolette doppie.
5. 	Si otterrà l'output con informazioni dettagliate sull'errore (simile al seguente):

		Id                  	: 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   	: ADF
		DataFactoryName     	: LogProcessingFactory3
		TableName           	: EnrichedGameEventsTable
		ProcessingStartTime 	: 10/10/2014 3:04:52 AM
		ProcessingEndTime   	: 10/10/2014 3:06:49 AM
		PercentComplete     	: 0
		DataSliceStart      	: 5/5/2014 12:00:00 AM
		DataSliceEnd        	: 5/6/2014 12:00:00 AM
		Status              	: FailedExecution
		Timestamp           	: 10/10/2014 3:04:52 AM
		RetryAttempt        	: 0
		Properties          	: {}
		ErrorMessage        	: Pig script failed with exit code '5'. See wasb://		adfjobs@spestore.blob.core.windows.net/PigQuery
		                        		Jobs/841b77c9-d56c-48d1-99a3-
					8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
					more details.
		ActivityName        	: PigEnrichLogs
		PipelineName        	: EnrichGameLogsPipeline
		Type                	:
	
	
6. 	È possibile eseguire il cmdlet **Save-AzureRmDataFactoryLog** con il valore ID visualizzato nell'output riportato sopra e scaricare i file di log usando l'opzione **-DownloadLogsoption** del cmdlet.

	Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\\Test"


## Eseguire nuovamente le operazioni non riuscite in una pipeline

### Uso del portale di Azure classico

Dopo avere risolto i problemi relativi agli errori in una pipeline e averne eseguito il debug, è possibile eseguire nuovamente le operazioni non riuscite passando alla sezione degli errori e facendo clic sul pulsante **Esegui** sulla barra dei comandi.

![Nuova esecuzione di una sezione non riuscita](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Se non è possibile eseguire la convalida della sezione a causa di un errore relativo ai criteri (ad esempio, dati non disponibili), è possibile correggere l'errore ed eseguire nuovamente la convalida facendo clic sul pulsante **Convalida** sulla barra dei comandi. ![Correzione di errori e convalida](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### Uso di Azure PowerShell

È possibile eseguire nuovamente le operazioni non riuscite usando il cmdlet 'Set-AzureRmDataFactorySliceStatus'.

	Set-AzureRmDataFactorySliceStatus [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Status] <String> [[-UpdateType] <String> ] [-Profile <AzureProfile> ] [ <CommonParameters>]

**Esempio:** l'esempio seguente imposta lo stato di tutte le sezioni per la tabella 'DAWikiAggregatedData' su 'PendingExecution' nella data factory di Azure 'WikiADF'.

**Nota:** poiché UpdateType è impostato su UpstreamInPipeline, lo stato di ogni sezione della tabella e di tutte le tabelle dipendenti (upstream) usate come tabelle di input per le attività nella pipeline viene impostato su "PendingExecution". Un altro possibile valore per questo parametro è "Individual".

	Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status PendingExecution -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## Creare avvisi
Azure registra eventi utente quando una risorsa di Azure (ad esempio, una data factory) viene creata, aggiornata o eliminata. È possibile creare avvisi per questi eventi. Data factory consente di acquisire diverse metriche e di creare avvisi per le metriche. Si consiglia di usare gli eventi per il monitoraggio in tempo reale e le metriche per motivi cronologici.

### Avvisi relativi agli eventi
Gli eventi di Azure forniscono utili informazioni su quanto accade alle risorse di Azure. Azure registra eventi utente quando una risorsa di Azure (ad esempio, una data factory) viene creata, aggiornata o eliminata. Quando si usa Data factory di Azure, vengono generati eventi quando:

- Data factory di Azure viene creata/aggiornata/eliminata.
- L'elaborazione dati (esecuzione) è stata avviata/completata.
- Quando un cluster HDInsight su richiesta viene creato e rimosso.

È possibile creare avvisi per questi eventi utente e configurarli per l'invio di notifiche tramite posta elettronica all'amministratore e ai coamministratori della sottoscrizione. Inoltre, è possibile specificare altri indirizzi di posta elettronica di utenti che devono ricevere notifiche tramite posta elettronica quando vengono soddisfatte le condizioni. Ciò è molto utile quando si desidera ricevere notifiche sugli errori senza monitorare continuamente la data factory.

#### Specifica di una definizione di avviso:
Per specificare una definizione di avviso, creare un file JSON che descrive le operazioni per cui si desidera essere avvisati. Nell'esempio seguente l'avviso invierà una notifica tramite posta elettronica per l'operazione RunFinished. In particolare, viene inviata una notifica tramite posta elettronica quando un'esecuzione nella data factory viene completata, ma l'esito è negativo (stato = FailedExecution).

	{
	    "contentVersion": "1.0.0.0",
	     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters": {},
	    "resources": 
	    [
	        {
	            "name": "ADFAlertsSlice",
	            "type": "microsoft.insights/alertrules",
	            "apiVersion": "2014-04-01",
	            "location": "East US",
	            "properties": 
	            {
	                "name": "ADFAlertsSlice",
	                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
	                "isEnabled": true,
	                "condition": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
	                    "dataSource": 
	                    {
	                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
	                        "operationName": "RunFinished",
	                        "status": "Failed",
	                        "subStatus": "FailedExecution"   
	                    }
	                },
	                "action": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails": [ "<your alias>@contoso.com" ]
	                }
	            }
	        }
	    ]
	}

**subStatus** può essere rimosso dalla definizione JSON precedente se non si desidera ricevere un avviso in caso di errore specifico.

Nell'esempio precedente viene configurato l'avviso per tutte le factory di dati nella sottoscrizione. Se si desidera che venga impostato l'avviso per una factory di dati specifica, è possibile indicare la factory dei dati **resourceUri** nel blocco **dataSource** come di seguito:

	"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

La tabella seguente fornisce l'elenco di operazioni e di stati (e degli stati secondari) disponibili.

Nome operazione | Stato | Stato secondario
-------------- | ------ | ----------
RunStarted | Started | Starting
RunFinished | Failed/Succeeded | <p>FailedResourceAllocation</p><p>Succeeded</p><p>FailedExecution</p><p>TimedOut</p><p><Canceled/p><p>FailedValidation</p><p>Abandoned</p>
OnDemandClusterCreateStarted | Started
OnDemandClusterCreateSuccessful | Succeeded
OnDemandClusterDeleted | Succeeded

Vedere [Crea regola di avviso](https://msdn.microsoft.com/library/azure/dn510366.aspx) per informazioni dettagliate sugli elementi JSON usati nell'esempio precedente.

#### Distribuzione dell'avviso 
Per distribuire l'avviso, usare il cmdlet di Azure PowerShell **New-AzureRmResourceGroupDeployment**, come mostrato nell'esempio seguente:

	New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  

Una volta completata la distribuzione del gruppo di risorse, si noteranno i messaggi seguenti:

	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

#### Recupero dell'elenco di distribuzioni del gruppo di risorse Azure
Per recuperare l'elenco di distribuzioni del gruppo di risorse di Azure distribuite, usare il cmdlet **Get-AzureRmResourceGroupDeployment**, come mostrato nell'esempio seguente:

	Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :


#### Risoluzione dei problemi relativi agli eventi utente


- È possibile visualizzare tutti gli eventi generati dopo aver selezionato il riquadro **Operazioni**. Gli avvisi possono essere configurati per qualsiasi operazione visibile nel pannello **Eventi**:

	![Operazioni](./media/data-factory-monitor-manage-pipelines/operations.png)


- Vedere l’articolo [Cmdlet di Azure Insight](https://msdn.microsoft.com/library/mt282452.aspx) per i cmdlet PowerShell che è possibile usare per gli avvisi add/get/remove. Ecco alcuni esempi di uso del cmdlet **Get-AlertRule**:


		PS C:\> get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
			
				Properties :
		        Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
		        Condition   :
				DataSource :
				EventName             :
				Category              :
				Level                 :
				OperationName         : RunFinished
				ResourceGroupName     :
				ResourceProviderName  :
				ResourceId            :
				Status                : Failed
				SubStatus             : FailedExecution
				Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
		        Condition  	:
				Description : One or more of the data slices for the Azure Data Factory has failed processing.
				Status      : Enabled
				Name:       : ADFAlertsSlice
				Tags       :
				$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
				Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
				Location   : West US
				Name       : ADFAlertsSlice
		
		PS C:\> Get-AlertRule -res $resourceGroup
	
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
				Location   : West US
				Name       : FailedExecutionRunsWest3
	
		PS C:\> Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0

	Eseguire i seguenti comandi get-help per visualizzare informazioni dettagliate ed esempi per il cmdlet Get-AlertRule.

		get-help Get-AlertRule -detailed 
		get-help Get-AlertRule -examples


- Se si visualizzano gli eventi di generazione avvisi nel pannello del portale, ma non si ricevono le notifiche di posta elettronica, controllare se l'indirizzo di posta elettronica specificato è impostato per la ricezione di messaggi da mittenti esterni. I messaggi di avviso potrebbero essere stati bloccati dalle impostazioni di posta elettronica.

### Avvisi relativi alle metriche
Data factory consente di acquisire diverse metriche e di creare avvisi per le metriche. È possibile monitorare e creare avvisi per le metriche seguenti relative alle sezioni della data factory.
 
- Esecuzioni non riuscite
- Esecuzioni riuscite

Queste metriche sono molto utili e consentono agli utenti di ottenere una panoramica di tutte le esecuzioni riuscite e non riuscite nella data factory. Le metriche vengono emesse ogni volta che viene eseguita una sezione. All'inizio di ogni ora, queste metriche vengono aggregate e inserite nell'account di archiviazione. Per abilitare le metriche, sarà quindi necessario configurare un account di archiviazione.

#### Abilitazione delle metriche:
Per abilitare le metriche, fare clic sul pannello Data factory seguente:

**Monitoraggio** -> **Metrica** -> **Impostazioni di diagnostica** -> **Diagnostica**

Nel pannello **Diagnostica** fare clic sul pulsante di **attivazione**, selezionare l'account di archiviazione e salvare.

![Abilitazione di metriche](./media/data-factory-monitor-manage-pipelines/enable-metrics.png)

Dopo avere salvato, potrebbe trascorrere anche un'ora prima che le metriche siano visibili nel pannello di monitoraggio, perché l'aggregazione delle metriche viene eseguita ogni ora.


### Configurazione dell'avviso relativo alle metriche:

Per configurare avvisi relativi alle metriche, fare clic sulle opzioni seguenti nel pannello Data factory: **Monitoraggio** -> **Metrica** -> **Aggiungi avviso** -> **Aggiungi una regola di avviso**.

Inserire i dettagli per la regola di avviso, specificare gli indirizzi di posta elettronica e fare clic su **OK**.


![Configurazione di avvisi relativi alle metriche](./media/data-factory-monitor-manage-pipelines/setting-up-alerts-on-metrics.png)

Al termine, nel riquadro Regole di avviso dovrebbe essere visualizzata una nuova regola di avviso come la seguente:

![Regole di avviso abilitate](./media/data-factory-monitor-manage-pipelines/alert-rule-enabled.png)

Congratulazioni. È stata configurata la prima regola di avviso relativa alle metriche. Ora si dovrebbero ricevere le notifiche ogni volta che la regola di avviso corrisponde nella finestra di tempo indicata.

### Notifiche di avviso:
Una volta che la regola configurata corrisponde alla condizione, dovrebbe essere attivato un avviso tramite posta elettronica. Una volta che il problema è risolto e la condizione di avviso non corrisponde più, si riceverà tramite posta elettronica un avviso relativo alla risoluzione.

Questo comportamento è diverso da quello degli eventi in cui verrà inviata una notifica per ogni singolo errore qualificato dalla regola di avviso.

### Distribuzione di avvisi tramite PowerShell
È possibile distribuire avvisi per le metriche esattamente come avviene per gli eventi.

**Definizione dell'avviso:**

	{
	    "contentVersion" : "1.0.0.0",
	    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters" : {},
	    "resources" : [
		{
	            "name" : "FailedRunsGreaterThan5",
	            "type" : "microsoft.insights/alertrules",
	            "apiVersion" : "2014-04-01",
	            "location" : "East US",
	            "properties" : {
	                "name" : "FailedRunsGreaterThan5",
	                "description" : "Failed Runs greater than 5",
	                "isEnabled" : true,
	                "condition" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
	                    "dataSource" : {
	                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
	                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
	                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
	>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
	                        "metricName" : "FailedRuns"
	                    },
	                    "threshold" : 5.0,
	                    "windowSize" : "PT3H",
	                    "timeAggregation" : "Total"
	                },
	                "action" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails" : ["abhinav.gpt@live.com"]
	                }
	            }
	        }
	    ]
	}
 
Sostituire subscriptionId resourceGroupName e dataFactoryName dell'esempio precedente con i valori appropriati.

*metricName* supporta al momento 2 valori: - FailedRuns - SuccessfulRuns

**Distribuzione dell'avviso:**

Per distribuire l'avviso, usare il cmdlet di Azure PowerShell **New-AzureRmResourceGroupDeployment**, come mostrato nell'esempio seguente:

	New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

Dopo il completamento della distribuzione, verrà visualizzato il messaggio seguente:

	VERBOSE: 12:52:47 PM - Template is valid.
	VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
	VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded
	
	
	DeploymentName    : FailedRunsGreaterThan5
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 7/27/2015 7:52:56 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           


È anche possibile usare il cmdlet **Add-AlertRule** per distribuire una regola di avviso. Per informazioni dettagliate ed esempi, vedere l'argomento [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

<!---HONumber=AcomDC_0107_2016-->