---
title: Monitoraggio e diagnostica per i contenitori di Windows in Azure Service Fabric | Microsoft Docs
description: Impostare il monitoraggio e la diagnostica per i contenitori di Windows orchestrati in Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 16c9926d44f972d2b38028cb6ab1420de6b60533
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---


# <a name="monitor-windows-containers-on-service-fabric-using-oms"></a>Monitorare i contenitori di Windows in Service Fabric usando OMS

Questa è la terza parte di un'esercitazione in cui si spiega come impostare OMS per monitorare i contenitori di Windows orchestrati in Service Fabric.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare OMS per il cluster di Service Fabric
> * Usare un'area di lavoro di OMS per visualizzare i log ed eseguire query su di essi dai contenitori e dai nodi
> * Configurare l'agente di OMS per raccogliere le metriche relative ai contenitori e ai nodi

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario:
- Disporre di un cluster in Azure o [crearne uno seguendo le istruzioni in questa esercitazione](service-fabric-tutorial-create-cluster-azure-ps.md)
- [Distribuire nel cluster un'applicazione nei contenitori](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-oms-with-your-cluster-in-the-resource-manager-template"></a>Impostazione di OMS con il cluster nel modello di Resource Manager

Nel caso in cui sia stato usato il [modello fornito](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial) nella prima parte di questa esercitazione, è necessario aggiungere le modifiche seguenti a un modello generico di Azure Resource Manager di Service Fabric. Se si dispone di un cluster personale e si desidera impostarlo per monitorare i contenitori con OMS:
* Apportare le modifiche seguenti al modello di Resource Manager.
* Distribuire il modello usando PowerShell per aggiornare il cluster tramite la [distribuzione del modello](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm). Azure Resource Manager viene informato dell'esistenza della risorsa e la distribuisce come aggiornamento.

### <a name="adding-oms-to-your-cluster-template"></a>Aggiunta di OMS al modello del cluster

Apportare le modifiche seguenti nel file *template.json*:

1. Aggiungere il nome e il percorso dell'area di lavoro di OMS nella sezione *parameters*:
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your OMS Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your OMS workspace"
      }
    }
    ```

    Per modificare il valore usato per entrambi, aggiungere gli stessi parametri nel file *template.parameters.json* e modificare i valori usati in quel file.

2. Aggiungere il nome della soluzione e la soluzione nella sezione *variables*: 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Aggiungere Microsoft Monitoring Agent di OMS come estensione della macchina virtuale. Trovare la risorsa set di scalabilità di macchine virtuali: *resources* > *"apiVersion": "[variables('vmssApiVersion')]"*. In *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* aggiungere la descrizione di estensione seguente nell'estensione *ServiceFabricNode*: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Aggiungere l'area di lavoro di OMS come singola risorsa. In *resources*, dopo la risorsa set di scalabilità di macchine virtuali, aggiungere quanto segue:
    
    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Qui](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) è disponibile un modello di esempio (usato nella prima parte dell'esercitazione) che include tutte queste modifiche che può essere usato se necessario come riferimento. Queste modifiche determinano l'aggiunta di un'area di lavoro di Log Analytics di OMS al gruppo di risorse. L'area di lavoro viene quindi configurata per raccogliere gli eventi della piattaforma Service Fabric dalle tabelle di archiviazione configurate con l'agente di [Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md). L'agente Microsoft Monitoring Agent di OMS è stato inoltre aggiunto a ogni nodo del cluster come estensione della macchina virtuale. Ciò significa che, quando si scala il cluster, l'agente viene automaticamente configurato in ogni computer e collegato alla stessa area di lavoro.

Distribuire il modello con le nuove modifiche per aggiornare il cluster attuale. Al termine le risorse OMS dovrebbero essere visibili nel gruppo di risorse. Quando il cluster è pronto, distribuirvi l'applicazione nei contenitori. Nel passaggio successivo si imposterà il monitoraggio dei contenitori.

## <a name="add-the-container-monitoring-solution-to-your-oms-workspace"></a>Aggiungere la Soluzione Monitoraggio contenitori all'area di lavoro di OMS

Per configurare la soluzione Contenitori nell'area di lavoro, cercare *Soluzione Monitoraggio contenitori* e creare una risorsa Contenitori (nella categoria Monitoraggio e gestione).

![Aggiunta della soluzione Contenitori](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Al prompt dell'*area di lavoro di OMS* selezionare l'area di lavoro creata nel gruppo di risorse e fare clic su **Crea**. Questa operazione aggiunge una *Soluzione Monitoraggio contenitori* nell'area di lavoro e fa sì che l'agente di OMS distribuito dal modello inizi automaticamente a raccogliere le statistiche e i log Docker. 

Tornare al *gruppo di risorse*, in cui è ora visibile la soluzione di monitoraggio appena aggiunta. Se si fa clic sulla soluzione, viene visualizzata la pagina di destinazione in cui viene mostrata la serie di immagini di contenitori in esecuzione. 

*Si noti che sono state eseguite 5 istanze del contenitore fabrikam derivanti dalla [seconda parte](service-fabric-host-app-in-a-container.md) dell'esercitazione*.

![Pagina di destinazione della soluzione Contenitori](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Se si fa clic nella **Soluzione Monitoraggio contenitori**, viene visualizzato un dashboard più dettagliato che consente di esaminare più pannelli, nonché di eseguire query in Log Analytics. 

*Si noti che da settembre 2017 la soluzione è oggetto di alcuni aggiornamenti. Eventuali errori che si possono ottenere da eventi Kubernetes devono essere ignorati, in quanto si sta lavorando all'integrazione di più agenti di orchestrazione nella stessa soluzione.*

Poiché raccoglie i log Docker, per impostazione predefinita l'agente mostra *stdout* e *stderr*. Se si scorre la schermata verso destra, si possono vedere l'inventario delle immagini dei contenitori, lo stato, le metriche e alcune query di esempio che è possibile eseguire per ottenere dati più utili. 

![Dashboard della soluzione Contenitori](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Se si fa clic in uno qualsiasi di questi pannelli, viene visualizzata la query di Log Analytics che genera il valore visualizzato. Modificare la query in *\** per vedere tutti i diversi generi di log che è possibile usare. Da qui è possibile eseguire una query o applicare un filtro sulle prestazioni di contenitore, i log, oppure esaminare gli eventi della piattaforma Service Fabric. Gli agenti installati emettono inoltre costantemente un heartbeat da ogni nodo, che è possibile esaminare per verificare che i dati vengano ancora raccolti da tutti i computer interessati nel caso in cui venga modificata la configurazione del cluster.   

![Query di contenitore](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>Configurare l'agente di OMS per prelevare i contatori delle prestazioni

Un altro vantaggio derivante dall'utilizzo dell'agente di OMS è la possibilità di modificare i contatori delle prestazioni che si desidera prelevare attraverso l'interfaccia utente di OMS, anziché dover configurare l'agente di diagnostica di Azure ed eseguire ogni volta un aggiornamento basato sul modello di Resource Manager. A tale scopo, fare clic sul **portale di OMS** nella pagina di destinazione della Soluzione Monitoraggio contenitori (o Service Fabric).

![Portale OMS](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

Verrà aperta l'area di lavoro nel portale di OMS, in cui è possibile visualizzare le proprie soluzioni, creare dashboard personalizzati, nonché configurare l'agente di OMS. 
* Fare clic sull'icona a forma di **ruota dentata** nell'angolo superiore destro della schermata per aprire il menu *Impostazioni*.
* Fare clic su **Origini connesse** > **Server Windows** e verificare che siano presenti *5 computer Windows connessi*.
* Fare clic su **Dati** > **Contatori delle prestazioni di Windows** per cercare e aggiungere nuovi contatori delle prestazioni. Qui viene visualizzato un elenco di contatori delle prestazioni consigliati da OMS da cui è possibile raccogliere dati. È inoltre possibile cercare altri contatori. Fare clic su **Aggiungi i contatori delle prestazioni selezionati** per avviare la raccolta delle metriche consigliate.

    ![Contatori delle prestazioni](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

Nel portale di Azure **aggiornare** la Soluzione Monitoraggio contenitori e in pochi minuti si dovrebbero iniziare a vedere i dati relativi alle *Prestazioni computer*. Questi dati consentono di capire come vengono usate le risorse. È possibile usare queste metriche anche per prendere decisioni appropriate sul ridimensionamento del cluster o per verificare se il bilanciamento del carico in un cluster avviene come previsto.

*Nota: assicurarsi di impostare i filtri relativi al tempo nel modo appropriato per usare queste metriche.* 

![Contatori delle prestazioni 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Configurare OMS per il cluster di Service Fabric
> * Usare un'area di lavoro di OMS per visualizzare i log ed eseguire query su di essi dai contenitori e dai nodi
> * Configurare l'agente di OMS per raccogliere le metriche relative ai contenitori e ai nodi

Il monitoraggio dell'applicazione in contenitori è ora impostato ed è quindi possibile eseguire le operazioni seguenti:

* Configurare OMS per un cluster Linux attenendosi a una procedura simile a quella appena illustrata. Fare riferimento a [questo modello](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) per apportare le modifiche al modello di Resource Manager.
* Configurare OMS per impostare [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) in modo da semplificare il rilevamento e la diagnostica.
* Esplorare l'elenco di [contatori delle prestazioni consigliati](service-fabric-diagnostics-event-generation-perf.md) in Service Fabric per configurare i cluster.
* Acquisire familiarità con le funzionalità di [ricerca log ed esecuzione di query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics.
