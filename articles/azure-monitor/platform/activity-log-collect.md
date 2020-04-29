---
title: Raccogliere e analizzare il log attività di Azure in monitoraggio di Azure
description: Raccogliere il log attività di Azure nei log di monitoraggio di Azure e usare la soluzione di monitoraggio per analizzare e cercare il log attività di Azure in tutte le sottoscrizioni di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382861"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Raccogliere e analizzare il log attività di Azure in monitoraggio di Azure
Il [log attività di Azure](platform-logs-overview.md) è un [log di piattaforma](platform-logs-overview.md) che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Sebbene sia possibile visualizzare il log attività nella portale di Azure, è necessario configurarlo per l'invio a un'area di lavoro Log Analytics per abilitare funzionalità aggiuntive di monitoraggio di Azure. Questo articolo descrive come eseguire questa configurazione e come inviare il log attività ad hub eventi e archiviazione di Azure.

La raccolta del log attività in un'area di lavoro Log Analytics offre i vantaggi seguenti:

- Nessun inserimento di dati o di conservazione dei dati per i dati del log attività archiviati in un'area di lavoro Log Analytics.
- Correlare i dati del log attività con altri dati di monitoraggio raccolti da monitoraggio di Azure.
- Usare le query di log per eseguire analisi complesse e ottenere informazioni approfondite sulle voci del log attività.
- Usare gli avvisi del log con le voci di attività che consentono una logica più complessa per gli avvisi.
- Archivia le voci del log attività per più di 90 giorni.
- Consolidare le voci di log da più sottoscrizioni e tenant di Azure in un'unica posizione per l'analisi insieme.

> [!IMPORTANT]
> Per raccogliere i log tra i tenant è necessario [Azure Lighthouse](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Raccolta del log attività
Il log attività viene raccolto automaticamente per [la visualizzazione nel portale di Azure](activity-log-view.md). Per raccoglierlo in un'area di lavoro Log Analytics o per inviarlo ad archiviazione di Azure o hub eventi, creare un' [impostazione di diagnostica](diagnostic-settings.md). Si tratta dello stesso metodo usato dai log delle risorse per renderlo coerente per tutti i [log della piattaforma](platform-logs-overview.md).  

Per creare un'impostazione di diagnostica per il log attività, selezionare **impostazioni di diagnostica** dal menu **log attività** in monitoraggio di Azure. Per informazioni dettagliate sulla creazione dell'impostazione, vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](diagnostic-settings.md) . Per una descrizione delle categorie che è possibile filtrare, vedere [le categorie nel log attività](activity-log-view.md#categories-in-the-activity-log) . Se sono presenti impostazioni legacy, assicurarsi di disabilitarle prima di creare un'impostazione di diagnostica. L'abilitazione di entrambe le funzionalità può generare dati duplicati.

![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Attualmente, è possibile creare un'impostazione di diagnostica a livello di sottoscrizione solo usando il portale di Azure e un modello di Gestione risorse. 


## <a name="legacy-settings"></a>Impostazioni legacy 
Mentre le impostazioni di diagnostica sono il metodo preferito per inviare il log attività a destinazioni diverse, i metodi legacy continueranno a funzionare se non si sceglie di sostituire con un'impostazione di diagnostica. Le impostazioni di diagnostica offrono i vantaggi seguenti rispetto ai metodi legacy ed è consigliabile aggiornare la configurazione:

- Metodo coerente per la raccolta di tutti i log di piattaforma.
- Raccogliere il log attività tra più sottoscrizioni e tenant.
- Filtra raccolta per raccogliere solo i log per determinate categorie.
- Raccoglie tutte le categorie del log attività. Alcune categorie non vengono raccolte utilizzando il metodo legacy.
- Latenza più veloce per l'inserimento dei log. Il metodo precedente presenta una latenza di circa 15 minuti mentre le impostazioni di diagnostica aggiungono solo circa 1 minuto.



### <a name="log-profiles"></a>Profili dei log
I profili di log sono il metodo legacy per l'invio del log attività ad hub eventi o archiviazione di Azure. Utilizzare la procedura seguente per continuare a utilizzare un profilo di log o per disabilitarlo in preparazione per la migrazione a un'impostazione di diagnostica.

1. Dal menu **monitoraggio di Azure** nella portale di Azure selezionare **log attività**.
3. Fare clic su **impostazioni di diagnostica**.

   ![Impostazioni di diagnostica](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Fare clic sul banner viola per l'esperienza legacy.

    ![Esperienza legacy](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Il metodo legacy per la raccolta del log attività in un'area di lavoro Log Analytics è la connessione del log nella configurazione dell'area di lavoro. 

1. Dal menu **log Analytics aree di lavoro** della portale di Azure selezionare l'area di lavoro per raccogliere il log attività.
1. Nella sezione **origini dati dell'area di lavoro** del menu dell'area di lavoro selezionare **log attività di Azure**.
1. Fare clic sulla sottoscrizione che si desidera connettere.

    ![Aree di lavoro](media/activity-log-collect/workspaces.png)

1. Fare clic su **Connetti** per connettere il log attività nella sottoscrizione all'area di lavoro selezionata. Se la sottoscrizione è già connessa a un'altra area di lavoro, fare clic su **Disconnetti** prima per disconnetterla.

    ![Connettere le aree di lavoro](media/activity-log-collect/connect-workspace.png)


Per disabilitare l'impostazione, eseguire la stessa procedura e fare clic su **Disconnetti** per rimuovere la sottoscrizione dall'area di lavoro.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analizzare il log attività nell'area di lavoro Log Analytics
Quando si connette un log attività a un'area di lavoro Log Analytics, le voci vengono scritte nell'area di lavoro in una tabella denominata *AzureActivity* che è possibile recuperare con una [query di log](../log-query/log-query-overview.md). La struttura di questa tabella varia a seconda della [categoria della voce di log](activity-log-view.md#categories-in-the-activity-log). Per una descrizione di ogni categoria, vedere [schema degli eventi del log attività di Azure](activity-log-schema.md) .


### <a name="data-structure-changes"></a>Modifiche alla struttura dei dati
Le impostazioni di diagnostica raccolgono gli stessi dati del metodo legacy usato per raccogliere il log attività con alcune modifiche alla struttura della tabella *AzureActivity* .

Le colonne nella tabella seguente sono state deprecate nello schema aggiornato. Sono ancora presenti in *AzureActivity* , ma non avranno dati. La sostituzione di queste colonne non è nuova, ma contengono gli stessi dati della colonna deprecata. Sono in un formato diverso, quindi potrebbe essere necessario modificare le query di log che le usano. 

| Colonna deprecata | Colonna sostitutiva |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> In alcuni casi, i valori in queste colonne possono essere tutti in lettere maiuscole. Se si dispone di una query che include queste colonne, è necessario utilizzare l' [operatore = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) per eseguire un confronto senza distinzione tra maiuscole e minuscole.

La colonna seguente è stata aggiunta a *AzureActivity* nello schema aggiornato:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Soluzione di monitoraggio dei log attività
La soluzione di monitoraggio di Azure Log Analytics verrà presto deprecata e sostituita da una cartella di lavoro usando lo schema aggiornato nell'area di lavoro Log Analytics. È comunque possibile usare la soluzione se è già abilitata, ma può essere usata solo se si sta raccogliendo il log attività usando le impostazioni legacy. 



### <a name="use-the-solution"></a>Usare la soluzione
È possibile accedere alle soluzioni di monitoraggio dal menu **monitoraggio** della portale di Azure. Per aprire la pagina **Panoramica** con i riquadri della soluzione, selezionare **altre informazioni** nella sezione **Insights** . Il riquadro **log attività di Azure** Visualizza un conteggio del numero di record **AzureActivity** nell'area di lavoro.

![Riquadro Log attività di Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Fare clic sul riquadro **log attività di Azure** per aprire la visualizzazione **log attività di Azure** . La vista include le parti di visualizzazione nella tabella seguente. Ogni parte elenca fino a 10 elementi corrispondenti ai criteri delle parti per l'intervallo di tempo specificato. È possibile eseguire una query di log che restituisce tutti i record corrispondenti facendo clic su **Visualizza tutto** nella parte inferiore della parte.

![Dashboard Log attività di Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Abilitare la soluzione per le nuove sottoscrizioni
A breve non sarà più possibile aggiungere la soluzione analisi dei log attività alla sottoscrizione usando il portale di Azure. È possibile aggiungerlo usando la procedura seguente con un modello di Resource Manager. 

1. Copiare il codice JSON seguente in un file denominato *ActivityLogTemplate*. JSON.

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

2. Distribuire il modello usando i comandi di PowerShell seguenti:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [log attività](platform-logs-overview.md).
- Scopri di più sulla [piattaforma dati di monitoraggio di Azure](data-platform.md).
- Usare le [query di log](../log-query/log-query-overview.md) per visualizzare informazioni dettagliate dal log attività.
