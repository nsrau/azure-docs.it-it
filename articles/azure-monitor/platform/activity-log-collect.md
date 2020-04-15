---
title: Raccogliere e analizzare il log attività di Azure in Monitoraggio di AzureCollect and analyze Azure activity log in Azure Monitor
description: Raccogliere il log attività di Azure nei log di monitoraggio di Azure e usare la soluzione di monitoraggio per analizzare e cercare il log attività di Azure in tutte le sottoscrizioni di Azure.Collect the Azure Activity Log in Azure Monitor Logs and use the monitoring solution to analyze and search the Azure activity log across all your Azure subscriptions.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382861"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Raccogliere e analizzare il log attività di Azure in Monitoraggio di AzureCollect and analyze Azure Activity log in Azure Monitor
Il log attività di Azure è un log della piattaforma che fornisce informazioni dettagliate sugli eventi a livello di sottoscrizione che si sono verificati in Azure.The [Azure Activity log](platform-logs-overview.md) is a [platform log](platform-logs-overview.md) that provides insight into subscription-level events that have occurred in Azure. Sebbene sia possibile visualizzare il log attività nel portale di Azure, è consigliabile configurarlo per l'invio a un'area di lavoro di Log Analytics per abilitare funzionalità aggiuntive di Monitoraggio di Azure.While you can view the Activity log in the Azure portal, you should configure it to send to a Log Analytics workspace to enable additional features of Azure Monitor. Questo articolo descrive come eseguire questa configurazione e come inviare il log attività agli hub di eventi e archiviazione di Azure.This article describes how to perform this configuration and how to send the Activity log to Azure storage and event hubs.

La raccolta del log attività in un'area di lavoro di Log Analytics offre i vantaggi seguenti:Collecting the Activity Log in a Log Analytics workspace provides the following advantages:

- Nessun costo di inserimento o conservazione dei dati per i dati del log attività archiviati in un'area di lavoro di Log Analytics.No data ingestion or data retention charge for Activity log data stored in a Log Analytics workspace.
- Correlare i dati del log attività con altri dati di monitoraggio raccolti da Monitoraggio di Azure.Correlate Activity log data with other monitoring data collected by Azure Monitor.
- Usare le query di log per eseguire analisi complesse e ottenere informazioni approfondite sulle voci del log attività.
- Usare gli avvisi di log con le voci di attività che consentono una logica di avviso più complessa.
- Archiviare le voci del log attività per più di 90 giorni.
- Consolidare le voci di log da più sottoscrizioni e tenant di Azure in un'unica posizione per l'analisi insieme.

> [!IMPORTANT]
> La raccolta dei log tra tenant richiede [Azure Lighthouse](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Raccolta del registro attività
Il log attività viene raccolto automaticamente per la visualizzazione nel portale di [Azure.](activity-log-view.md) Per raccoglierlo in un'area di lavoro di Log Analytics o per inviarle hub di archiviazione o eventi di Azure, creare [un'impostazione di diagnostica.](diagnostic-settings.md) Si tratta dello stesso metodo utilizzato dai log delle risorse che lo rende coerente per tutti i log della [piattaforma.](platform-logs-overview.md)  

Per creare un'impostazione di diagnostica per il log attività, selezionare **Impostazioni di diagnostica** dal menu Log attività in Monitoraggio di Azure.To create a diagnostic setting for the Activity log, select Diagnostic settings from the Activity **log** menu in Azure Monitor. Per informazioni dettagliate sulla creazione dell'impostazione, vedere [Creare l'impostazione di diagnostica per raccogliere log e metriche](diagnostic-settings.md) della piattaforma in Azure.See Create diagnostic setting to collect platform logs and metrics in Azure for details on creating the setting. Per una descrizione delle categorie che è possibile filtrare, vedere [Categorie nel registro attività.](activity-log-view.md#categories-in-the-activity-log) Se sono presenti impostazioni legacy, assicurarsi di disabilitarle prima di creare un'impostazione di diagnostica. Avendo entrambi abilitati può provocare dati duplicati.

![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Attualmente, è possibile creare solo un'impostazione di diagnostica a livello di sottoscrizione usando il portale di Azure e un modello di Resource Manager.Currently, you can only, create a subscription level diagnostic setting using the Azure portal and a Resource Manager template. 


## <a name="legacy-settings"></a>Impostazioni legacy 
Mentre le impostazioni di diagnostica sono il metodo preferito per inviare il log attività a destinazioni diverse, i metodi legacy continueranno a funzionare se non si sceglie di sostituire con un'impostazione di diagnostica. Le impostazioni di diagnostica presentano i vantaggi seguenti rispetto ai metodi legacy ed è consigliabile aggiornare la configurazione:

- Metodo coerente per la raccolta di tutti i log della piattaforma.
- Raccogliere il log attività tra più sottoscrizioni e tenant.
- Raccolta filtri per raccogliere i log solo per determinate categorie.
- Raccogliere tutte le categorie del log attività. Alcune categorie non vengono raccolte utilizzando il metodo legacy.
- Latenza più rapida per l'inserimento del log. Il metodo precedente ha una latenza di circa 15 minuti, mentre le impostazioni di diagnostica aggiungono solo circa 1 minuto.



### <a name="log-profiles"></a>Profili dei log
I profili di log sono il metodo legacy per l'invio del log attività agli hub eventi o di archiviazione di Azure.Log profiles are the legacy method for sending the Activity log to Azure storage or event hubs. Utilizzare la procedura seguente per continuare a utilizzare un profilo di log o per disabilitarlo in preparazione per la migrazione a un'impostazione di diagnostica.

1. Dal menu **Monitoraggio di Azure** nel portale di Azure selezionare Log **attività**.
3. Fare clic su **Impostazioni di diagnostica**.

   ![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Fai clic sul banner viola per l'esperienza legacy.

    ![Esperienza legacy](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Il metodo legacy per la raccolta del log attività in un'area di lavoro di Log Analytics sta connettendo il log nella configurazione dell'area di lavoro. 

1. Dal menu **Aree di lavoro** di Log Analytics nel portale di Azure selezionare l'area di lavoro per raccogliere il log attività.
1. Nella sezione **Origini dati area di lavoro** del menu dell'area di lavoro selezionare Log attività di **Azure.**
1. Fare clic sull'abbonamento da connettere.

    ![Aree di lavoro](media/activity-log-collect/workspaces.png)

1. Fare clic su **Connetti** per connettere il log attività nella sottoscrizione all'area di lavoro selezionata. Se la sottoscrizione è già connessa a un'altra area di lavoro, fare clic su **Disconnetti** prima per disconnetterla.

    ![Connetti aree di lavoro](media/activity-log-collect/connect-workspace.png)


Per disabilitare l'impostazione, eseguire la stessa procedura e fare clic su **Disconnetti** per rimuovere la sottoscrizione dall'area di lavoro.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analizzare il log attività nell'area di lavoro di Log AnalyticsAnalyze Activity log in Log Analytics workspace
Quando si connette un log attività a un'area di lavoro log Analytics, le voci verranno scritte nell'area di lavoro in una tabella denominata *AzureActivity* che è possibile recuperare con una [query di log.](../log-query/log-query-overview.md) La struttura di questa tabella varia a seconda della [categoria della voce di registro.](activity-log-view.md#categories-in-the-activity-log) Vedere Schema di [eventi del log attività](activity-log-schema.md) di Azure per una descrizione di ogni categoria.


### <a name="data-structure-changes"></a>Modifiche alla struttura dei dati
Le impostazioni di diagnostica raccolgono gli stessi dati del metodo legacy usato per raccogliere il log attività con alcune modifiche alla struttura della tabella *AzureActivity.Diagnostic* settings collect the same data as the legacy method used to collect the Activity log with some changes to the structure of the AzureActivity table.

Le colonne nella tabella seguente sono state deprecate nello schema aggiornato. Esistono ancora in *AzureActivity,* ma non dispereranno dati. La sostituzione di queste colonne non è nuova, ma contiene gli stessi dati della colonna deprecata. Si trovano in un formato diverso, pertanto potrebbe essere necessario modificare le query di log che le utilizzano. 

| Colonna deprecata | Colonna di sostituzione |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus (Substato attività) | ActivitySubstatusValue |
| OperationName     | Valore NomeOperazione     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> In alcuni casi, i valori in queste colonne possono essere tutti in maiuscolo. Se si dispone di una query che include queste colonne, è necessario utilizzare [l'operatore di tipo ,](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) per eseguire un confronto senza distinzione tra maiuscole e minuscole.

La colonna seguente è stata aggiunta ad AzureActivity nello schema aggiornato:The following column have been added to *AzureActivity* in the updated schema:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Soluzione di monitoraggio di Activity Logs Analytics
La soluzione di monitoraggio di Azure Log Analytics sarà presto deprecata e sostituita da una cartella di lavoro usando lo schema aggiornato nell'area di lavoro di Log Analytics.The Azure Log Analytics monitoring solution will be deprecated soon and replaced by a workbook using the updated schema in the Log Analytics workspace. È comunque possibile usare la soluzione se è già abilitata, ma può essere usata solo se si raccoglie il log attività usando le impostazioni legacy. 



### <a name="use-the-solution"></a>Usare la soluzione
Le soluzioni di monitoraggio sono accessibili dal menu Monitoraggio nel portale di Azure.Monitoring solutions are accessed from the **Monitor** menu in the Azure portal. Selezionare **Altro** nella sezione **Insights** per aprire la pagina **Panoramica** con i riquadri della soluzione. Nel riquadro **Log attività** di Azure viene visualizzato un conteggio del numero di record **AzureActivity** nell'area di lavoro.

![Riquadro Log attività di Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Fare clic sul riquadro Log attività di Azure per aprire la visualizzazione Log attività di **Azure.Click** the **Azure Activity Logs** tile to open the Azure Activity Logs view. La visualizzazione include le parti di visualizzazione nella tabella seguente. Ogni parte elenca fino a 10 elementi corrispondenti ai criteri di tale parte per l'intervallo di tempo specificato. È possibile eseguire una query di log che restituisce tutti i record corrispondenti facendo clic su **Visualizza tutto** nella parte inferiore della parte.

![Dashboard Log attività di Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Abilitare la soluzione per le nuove sottoscrizioniEnable the solution for new subscriptions
Presto non sarà più possibile aggiungere la soluzione Activity Logs Analytics alla sottoscrizione tramite il portale di Azure.You will soon longer be able to add the Activity Logs Analytics solution to your subscription using the Azure portal. È possibile aggiungerlo utilizzando la procedura seguente con un modello di gestione delle risorse. 

1. Copiare il codice json seguente in un file denominato *ActivityLogTemplate*.json.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Distribuire il modello usando i comandi di PowerShell seguenti:Deploy the template using the following PowerShell commands:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul [Registro attività](platform-logs-overview.md).
- Altre informazioni sulla piattaforma dati di Monitoraggio di Azure .Learn more about the [Azure Monitor data platform](data-platform.md).
- Utilizzare [le query di log](../log-query/log-query-overview.md) per visualizzare informazioni dettagliate dal log attività.
