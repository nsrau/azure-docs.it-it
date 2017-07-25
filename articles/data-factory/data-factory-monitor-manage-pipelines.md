---
title: Monitorare e gestire le pipeline con il portale di Azure e PowerShell | Microsoft Docs
description: Informazioni su come usare il portale di Azure e Azure PowerShell per monitorare e gestire le pipeline e le data factory di Azure create.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 61bb5379cd94dd00814e14420947e7783999ff0a
ms.contentlocale: it-it
ms.lasthandoff: 06/03/2017


---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorare e gestire le pipeline di Azure Data Factory con il portale di Azure e PowerShell
> [!div class="op_single_selector"]
> * [Con il portale di Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md)


> [!IMPORTANT]
> L'applicazione di monitoraggio e gestione offre un supporto migliore per il monitoraggio e la gestione delle pipeline di dati, nonché per la risoluzione di eventuali problemi. Per dettagli sull'uso dell'applicazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md). 


Questo articolo descrive come monitorare e gestire le pipeline ed eseguirne il debug tramite il Portale di Azure e PowerShell. L'articolo contiene anche informazioni su come creare avvisi e ricevere notifiche sugli errori.

## <a name="understand-pipelines-and-activity-states"></a>Informazioni sulle pipeline e sugli stati delle attività
L'uso del portale di Azure consente di:

* Visualizzare la data factory come diagramma.
* Visualizzare le attività all'interno di una pipeline.
* Visualizzare set di dati di input e di output.

Questa sezione illustra anche come avviene la transizione di una sezione di un set di dati da uno stato a un altro.   

### <a name="navigate-to-your-data-factory"></a>Passare alla data factory
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Data factory** nel menu a sinistra. Se non è visibile, fare clic su **Altri servizi >**, quindi selezionare **Data factory** nella categoria **Intelligence e analisi**.

   ![Esplora tutto > Data factory](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Nel pannello **Data factory** selezionare la data factory a cui si è interessati.

    ![Selezionare la data factory](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Verrà visualizzata la home page della data factory.

   ![Pannello Data factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Vista diagramma della data factory
La vista **Diagramma** di una data factory offre un'unica console da cui monitorare e gestire la data factory e i relativi asset. Per visualizzare la vista **Diagramma** della data factory, fare clic su **Diagramma** nella home page della data factory.

![Vista diagramma](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

È possibile eseguire lo zoom avanti, lo zoom indietro, lo zoom 100%, adattare alla finestra, bloccare il layout del diagramma e posizionare automaticamente pipeline e set di dati. È anche possibile visualizzare le informazioni sulla derivazione dei dati, vale a dire gli elementi upstream e downstream degli elementi selezionati.

### <a name="activities-inside-a-pipeline"></a>Attività all'interno di una pipeline
1. Fare clic con il pulsante destro del mouse sulla pipeline e scegliere **Apri pipeline** per visualizzare tutte le attività della pipeline, oltre ai set di dati di input e output relativi a tali attività. Questa funzionalità è utile quando la pipeline include più di una attività e si vuole conoscere la derivazione operativa di una singola pipeline.

    ![Menu Apri pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. L'esempio seguente mostra un'attività di copia nella pipeline con un input e un output. 

    ![Attività all'interno di una pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Per tornare alla home page della data factory, fare clic sul collegamento **Data factory** nella barra di navigazione nell'angolo superiore sinistro.

    ![Ritorno a Data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Visualizzare lo stato di ogni attività all'interno di una pipeline
Per visualizzare lo stato corrente di un'attività, visualizzare lo stato di uno dei set di dati generati dall'attività.

Facendo doppio clic su **OutputBlobTable** nella vista **Diagramma**, è possibile visualizzare tutte le sezioni generate da esecuzioni diverse dell'attività all'interno di una pipeline. Si noti che l'attività di copia è stata eseguita correttamente nelle ultime otto ore e ha generato sezioni con lo stato **Pronto**.  

![Stato della pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

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
<td>ActivityResume</td><td>L'attività è sospesa e non può eseguire le sezioni fino a quando non viene ripresa.</td>
</tr>
<tr>
<td>Retry</td><td>L'esecuzione dell'attività viene ritentata.</td>
</tr>
<tr>
<td>Convalida</td><td>La convalida non è ancora stata avviata.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>La convalida è in attesa di essere ripetuta.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Convalida in corso.</td><td>La convalida è in esecuzione.</td>
</tr>
<td>-</td>
<td>La sezione è in corso.</td>
</tr>
<tr>
<td rowspan="4">Operazione non riuscita</td><td>TimedOut</td><td>L'esecuzione dell'attività ha richiesto più tempo di quello consentito dall'attività.</td>
</tr>
<tr>
<td>Canceled</td><td>La sezione è stata annullata dall'utente.</td>
</tr>
<tr>
<td>Convalida</td><td>Convalida non riuscita.</td>
</tr>
<tr>
<td>-</td><td>Non è stato possibile generare e/o convalidare la sezione.</td>
</tr>
<td>Ready</td><td>-</td><td>La sezione è pronta per essere utilizzata.</td>
</tr>
<tr>
<td>Skipped</td><td>None</td><td>La sezione non viene elaborata.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Esisteva una sezione con uno stato differente, ma è stata reimpostata.</td>
</tr>
</table>



Per visualizzare i dettagli di una sezione, fare clic sulla voce di una sezione nel pannello **Sezioni aggiornate di recente**.

![Dettagli della sezione](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Se la sezione è stata eseguita più volte, vengono visualizzate più righe nell'elenco **Esecuzioni attività** . Per visualizzare i dettagli di un'esecuzione attività, fare clic sulla voce di un'esecuzione nell'elenco **Esecuzioni attività** . L'elenco presenta tutti i file di log insieme a eventuali messaggi di errore. Questa funzionalità è utile per visualizzare i log ed eseguirne il debug senza dover uscire dalla data factory.

![Dettagli esecuzione attività](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Se lo stato della sezione non è **Pronto**, sarà possibile visualizzare le sezioni upstream che non sono pronte e bloccano l'esecuzione della sezione corrente nell'elenco **Sezioni upstream non pronte**. Questa funzionalità è utile quando lo stato della sezione è **In attesa** e si vogliono conoscere le dipendenze upstream di cui la sezione è in attesa.

![Sezioni upstream non pronte](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagramma di stato del set di dati
Quando la data factory è stata distribuita e le pipeline hanno un periodo attivo valido, le sezioni del set di dati passano da uno stato a un altro. Attualmente lo stato delle sezioni segue questo diagramma di stato:

![Diagramma di stato](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Il flusso di transizione di stato del set dati nella data factory è il seguente: In attesa -> In corso/In corso (Convalida) -> Pronto/Non riuscito.

La sezione viene avviata nello stato **In attesa** e prima dell'esecuzione è necessario che vengano soddisfatte le precondizioni. In seguito inizia l'esecuzione dell'attività e la sezione passa allo stato **In corso**. L'esecuzione dell'attività può avere esito positivo o negativo. Lo stato della sezione sarà **Pronto** o **Non riuscito** in base al risultato dell'esecuzione.

È possibile reimpostare la sezione in modo che dallo stato **Pronto** o **Non riuscito** torni allo stato **In attesa**. È anche possibile impostare lo stato della sezione su **Ignora**per impedire l'esecuzione dell'attività e l'elaborazione della sezione.

## <a name="pause-and-resume-pipelines"></a>Sospendere e riprendere le pipeline
È possibile gestire le pipeline usando Azure PowerShell. Ad esempio, è possibile sospendere e riprendere le pipeline eseguendo i cmdlet di Azure PowerShell. 

> [!NOTE] 
> La vista diagramma non supporta la sospensione e la ripresa di pipeline. Se si vuole usare un'interfaccia utente, usare l'applicazione di gestione e monitoraggio. Per dettagli sull'uso dell'applicazione, vedere l'articolo [Monitorare e gestire le pipeline di Azure Data Factory con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md). 

È possibile sospendere le pipeline usando il cmdlet di PowerShell **Suspend-AzureRmDataFactoryPipeline**. Questo cmdlet è utile quando non si desidera eseguire le pipeline finché non viene risolto un problema. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
ad esempio:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Dopo aver risolto il problema della pipeline, è possibile riprendere l'esecuzione della pipeline sospesa tramite il comando di PowerShell seguente:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
ad esempio:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Eseguire il debug delle pipeline
Azure Data Factory offre funzionalità avanzate per il debug e la risoluzione dei problemi relativi alle pipeline tramite il portale di Azure e Azure PowerShell.

> [!NOTA} È molto più semplice risolvere gli errori tramite l'app di monitoraggio e gestione. Per dettagli sull'uso dell'applicazione, vedere l'articolo [Monitorare e gestire le pipeline di Azure Data Factory con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md). 

### <a name="find-errors-in-a-pipeline"></a>Trovare gli errori in una pipeline
Se l'esecuzione di un'attività in una pipeline non riesce, il set di dati generato dalla pipeline è in uno stato di errore. È possibile eseguire il debug e risolvere i problemi relativi agli errori in Azure Data Factory usando i metodi seguenti.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Usare il portale di Azure per eseguire il debug di un errore
1. Nel pannello **Tabella** fare clic sulla sezione con errori con **Stato** impostato su **Non riuscito**.

   ![Pannello Tabella con sezione con errori](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Nel pannello **Sezione dati** fare clic sull'esecuzione dell'attività non riuscita.

   ![Sezione dati con un errore](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Nel pannello **Dettagli esecuzione attività** è possibile scaricare i file associati all'elaborazione di HDInsight. Fare clic su **Scarica** in corrispondenza di Stato/stderr per scaricare il file di log degli errori che contiene i dettagli dell'errore stesso.

   ![Pannello Dettagli esecuzione attività con errore](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Usare PowerShell per eseguire il debug di un errore
1. Avviare **PowerShell**.
2. Eseguire il comando **Get-AzureRmDataFactorySlice** per vedere le sezioni e i relativi stati. Verrà visualizzata una sezione con lo stato **Non riuscito**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   ad esempio:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Sostituire **StartDateTime** con l'ora di inizio della pipeline. 
3. Eseguire ora il cmdlet **Get-AzureRmDataFactoryRun** per ottenere i dettagli sull'esecuzione dell'attività per la sezione.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Ad esempio:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Il valore di StartDateTime è l'orario di inizio per la sezione con errori/problemi di cui si è preso nota nel passaggio precedente. La data e ora dovrebbe essere racchiusa tra virgolette doppie.
4. Si otterrà l'output con informazioni dettagliate sull'errore e sarà simile al seguente:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. È possibile eseguire il cmdlet **Save-AzureRmDataFactoryLog** con il valore ID visualizzato nell'output e scaricare i file di log usando l'opzione **-DownloadLogsoption** del cmdlet.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Eseguire nuovamente le operazioni non riuscite in una pipeline

> [!IMPORTANT]
> È più semplice risolvere gli errori e ripetere l'esecuzione di sezioni non riuscite tramite l'app di monitoraggio e gestione. Per dettagli sull'uso dell'applicazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Usare il portale di Azure
Dopo aver risolto i problemi relativi agli errori in una pipeline e averne eseguito il debug, è possibile eseguire nuovamente le operazioni non riuscite passando alla sezione degli errori e facendo clic sul pulsante **Esegui** sulla barra dei comandi.

![Nuova esecuzione di una sezione non riuscita](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Se non è possibile eseguire la convalida della sezione a causa di un errore relativo ai criteri, ad esempio se i dati non sono disponibili, è possibile correggere l'errore ed eseguire nuovamente la convalida facendo clic sul pulsante **Convalida** sulla barra dei comandi.

![Correggere gli errori e convalidare](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Uso di Azure PowerShell
È possibile eseguire nuovamente le operazioni non riuscite usando il cmdlet **Set-AzureRmDataFactorySliceStatus**. Per informazioni sulla sintassi e altri dettagli sul cmdlet, vedere l'argomento [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx).

**Esempio:**

L'esempio seguente mostra come impostare lo stato di tutte le sezioni per la tabella "DAWikiAggregatedData" su "In attesa" nella data factory "WikiADF" di Azure.

"UpdateType" è impostato su "UpstreamInPipeline". Ciò significa che lo stato di ogni sezione della tabella e di tutte le tabelle dipendenti, ovvero upstream, viene impostato su "In attesa". Un altro possibile valore per questo parametro è "Individuale".

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>Creare avvisi
Azure registra eventi utente quando una risorsa di Azure (ad esempio, una data factory) viene creata, aggiornata o eliminata. È possibile creare avvisi per questi eventi. Azure Data Factory consente di acquisire diverse metriche e di creare avvisi relativi alle metriche. È consigliabile usare gli eventi per il monitoraggio in tempo reale e le metriche per motivi cronologici.

### <a name="alerts-on-events"></a>Avvisi relativi agli eventi
Gli eventi di Azure forniscono utili informazioni su quanto accade alle risorse di Azure. Quando si usa Azure Data Factory, vengono generati eventi quando:

* Una data factory viene creata, aggiornata o eliminata.
* L'elaborazione dati (esecuzione) viene avviata o completata.
* Un cluster HDInsight su richiesta viene creato o rimosso.

È possibile creare avvisi per questi eventi utente e configurarli per l'invio di notifiche tramite posta elettronica all'amministratore e ai coamministratori della sottoscrizione. Inoltre, è possibile specificare altri indirizzi di posta elettronica di utenti che devono ricevere notifiche tramite posta elettronica quando vengono soddisfatte le condizioni. Questa funzionalità è molto utile quando si desidera ricevere notifiche sugli errori senza monitorare continuamente la data factory.

> [!NOTE]
> Attualmente il portale non visualizza gli avvisi relativi agli eventi. Usare l'[app di monitoraggio e gestione](data-factory-monitor-manage-app.md) per visualizzare tutti gli avvisi.


#### <a name="specify-an-alert-definition"></a>Specificare una definizione di avviso
Per specificare una definizione di avviso, creare un file JSON che descrive le operazioni per cui si vuole essere avvisati. Nell'esempio seguente l'avviso invia una notifica tramite posta elettronica per l'operazione RunFinished. In particolare, viene inviata una notifica tramite posta elettronica quando un'esecuzione nella data factory viene completata, ma l'esito è negativo (stato = FailedExecution).

```JSON
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
```

Se non si vuole ricevere un avviso in caso di errore specifico, è possibile rimuovere **subStatus** dalla definizione JSON.

Questo esempio configura l'avviso per tutte le data factory nella sottoscrizione. Se si desidera che venga impostato l'avviso per una data factory specifica, è possibile indicare la data factory **resourceUri** nel blocco **dataSource**:

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

La tabella seguente contiene l'elenco delle operazioni e degli stati e stati secondari disponibili.

| Nome operazione | Stato | Stato secondario |
| --- | --- | --- |
| RunStarted |Started |Starting |
| RunFinished |Failed/Succeeded |Allocazione risorse non riuscita<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abbandonato |
| OnDemandClusterCreateStarted |Started | |
| OnDemandClusterCreateSuccessful |Operazione completata | |
| OnDemandClusterDeleted |Operazione completata | |

Per informazioni dettagliate sugli elementi JSON usati nell'esempio, vedere [Crea regola avviso](https://msdn.microsoft.com/library/azure/dn510366.aspx).

#### <a name="deploy-the-alert"></a>Distribuire l'avviso
Per distribuire l'avviso, usare il cmdlet **New-AzureRmResourceGroupDeployment**di Azure PowerShell, come mostrato nell'esempio seguente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

Al termine della distribuzione del gruppo di risorse, verranno visualizzati i messaggi seguenti:

```
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
```

> [!NOTE]
> È possibile usare l'API REST di [Crea regola avviso](https://msdn.microsoft.com/library/azure/dn510366.aspx) per creare una regola di avviso. Il payload JSON è simile all'esempio JSON riportato sopra.  


#### <a name="retrieve-the-list-of-azure-resource-group-deployments"></a>Recuperare l'elenco delle distribuzioni di gruppi di risorse di Azure
Per recuperare l'elenco delle distribuzioni di gruppi di risorse di Azure distribuite, usare il cmdlet **Get-AzureRmResourceGroupDeployment**, come mostrato nell'esempio seguente:

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshoot-user-events"></a>Risolvere i problemi relativi a eventi utente
1. È possibile visualizzare tutti gli eventi generati dopo aver selezionato il riquadro **Metriche e operazioni**.

    ![Riquadro Metriche e operazioni](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Fare clic sul riquadro **Eventi** per visualizzare gli eventi.

    ![Riquadro Eventi](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. Nel pannello **Eventi** è possibile visualizzare i dettagli sugli eventi, filtrare gli eventi e così via.

    ![Pannello Eventi](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Fare clic su un'**operazione** nell'elenco delle operazioni che genera un errore.

    ![Selezionare un'operazione](./media/data-factory-monitor-manage-pipelines/select-operation.png)
5. Fare clic su un evento di **errore** per visualizzare i dettagli dell'errore.

    ![Evento di errore](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

Per informazioni sui cmdlet di PowerShell che è possibile usare per aggiungere, ottenere o rimuovere avvisi, vedere l'articolo relativo ai [cmdlet di Azure Insights](https://msdn.microsoft.com/library/mt282452.aspx). Ecco alcuni esempi di uso del cmdlet **Get-AlertRule** :

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
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
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
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
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

Eseguire i seguenti comandi get-help per visualizzare informazioni dettagliate ed esempi per il cmdlet Get-AlertRule.

```powershell
get-help Get-AlertRule -detailed
```

```powershell
get-help Get-AlertRule -examples
```


Se gli eventi di generazione avvisi vengono visualizzati nel pannello del portale, ma non si ricevono le notifiche di posta elettronica, controllare se l'indirizzo di posta elettronica specificato è impostato per la ricezione di messaggi da mittenti esterni. I messaggi di avviso potrebbero essere stati bloccati dalle impostazioni di posta elettronica.

### <a name="alerts-on-metrics"></a>Avvisi relativi alle metriche
Azure Data Factory consente di acquisire diverse metriche e di creare avvisi relativi alle metriche. È possibile monitorare e creare avvisi per le metriche seguenti relative alle sezioni della data factory:

* **Esecuzioni non riuscite**
* **Esecuzioni riuscite**

Queste metriche sono utili e consentono di ottenere una panoramica di tutte le esecuzioni riuscite e non riuscite nella data factory. Le metriche vengono emesse ogni volta che viene eseguita una sezione. All'inizio di ogni ora, queste metriche vengono aggregate e inserite nell'account di archiviazione. Per abilitare le metriche, configurare un account di archiviazione.

#### <a name="enable-metrics"></a>Abilitazione di metriche
Per abilitare le metriche, fare clic su quanto segue nel pannello **Data factory**:

**Monitoraggio** > **Metrica** > **Impostazioni di diagnostica** > **Diagnostica**

![Collegamento per la diagnostica](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

Nel pannello **Diagnostica** fare clic su **Sì**, selezionare l'account di archiviazione e fare clic su **Salva**.

![Pannello Diagnostica](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Potrebbe trascorrere anche un'ora prima che le metriche siano visibili nel pannello **Monitoraggio**, perché l'aggregazione delle metriche viene eseguita ogni ora.

### <a name="set-up-an-alert-on-metrics"></a>Configurare un avviso relativo alle metriche
Fare clic sul riquadro **Metriche data factory**:

![Riquadro Metriche data factory](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

Nel pannello **Metrica** fare clic su **+ Aggiungi avviso** nella barra degli strumenti.
![Pannello Metriche data factory > Aggiungi avviso](./media/data-factory-monitor-manage-pipelines/add-alert.png)

Nella pagina **Aggiungi una regola di avviso**, attenersi alla procedura seguente e fare clic su **OK**.

* Immettere un nome per l'avviso, ad esempio "Avviso non riuscito".
* Immettere una descrizione dell'avviso, ad esempio "Invia un messaggio di posta elettronica quando si verifica un errore".
* Selezionare una metrica tra "Esecuzioni non riuscite" ed "Esecuzioni riuscite".
* Specificare una condizione e un valore di soglia.   
* Specificare il periodo di tempo.
* Specificare se inviare un'e-mail a proprietari, collaboratori e lettori.

![Pannello Metriche data factory > Aggiungi regola di avviso](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

Dopo aver aggiunto la regola di avviso, il pannello si chiude e il nuovo avviso viene visualizzato nel pannello **Metrica**.

![Pannello Metriche data factory > Nuovo avviso aggiunto](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

Nel riquadro **Regole di avviso** si dovrebbe vedere anche il numero degli avvisi. Fare clic sul riquadro **Regole di avviso**.

![Pannello Metriche data factory - Regole di avviso](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

Nel pannello **Regole di avviso** vengono visualizzati gli avvisi esistenti. Per aggiungere un avviso, fare clic su **Aggiungi avviso** nella barra degli strumenti.

![Pannello Regole di avviso](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Notifiche di avviso
Quando la regola di avviso corrisponde alla condizione, si riceve un messaggio di posta elettronica che conferma l'attivazione dell'avviso. Quando il problema viene risolto e la condizione di avviso non corrisponde più, si riceve un messaggio di posta elettronica che conferma la risoluzione dell'avviso.

Questo comportamento è diverso da quello degli eventi in cui viene inviata una notifica per ogni errore qualificato da una regola di avviso.

### <a name="deploy-alerts-by-using-powershell"></a>Distribuire avvisi tramite PowerShell
È possibile distribuire avvisi per le metriche esattamente come avviene per gli eventi.

**Definizione di avviso**

```JSON
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
```

Sostituire *subscriptionId*, *resourceGroupName* e *dataFactoryName* nell'esempio precedente con i valori appropriati.

*metricName* supporta attualmente due valori:

* FailedRuns
* SuccessfulRuns

**Distribuire l'avviso**

Per distribuire l'avviso, usare il cmdlet **New-AzureRmResourceGroupDeployment**di Azure PowerShell, come mostrato nell'esempio seguente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

Al termine della distribuzione, verrà visualizzato il messaggio seguente:

```
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
```

È anche possibile usare il cmdlet **Add-AlertRule** per distribuire una regola di avviso. Per informazioni dettagliate ed esempi, vedere l'argomento [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).  

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Spostare una data factory in un gruppo di risorse diverso o una sottoscrizione diversa
È possibile spostare una data factory in un gruppo di risorse diverso o in una sottoscrizione diversa usando il pulsante **Sposta** della barra dei comandi nella home page della data factory.

![Spostare una data factory](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

È anche possibile spostare le risorse correlate insieme alla data factory, ad esempio gli avvisi ad essa associati.

![Finestra di dialogo Sposta risorse](./media/data-factory-monitor-manage-pipelines/MoveResources.png)

