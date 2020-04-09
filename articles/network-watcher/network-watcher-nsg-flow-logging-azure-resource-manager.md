---
title: Network Watcher - Creare log di flusso del gruppo di sicurezza di rete usando un modello di Azure Resource ManagerNetwork Watcher - Create NSG flow logs using an Azure Resource Manager template
description: Usare un modello di Azure Resource Manager e PowerShell per configurare facilmente i log di flusso del gruppo di sicurezza di rete.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 104311904b99cadbbc8c0267a98f2709443608ea
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891459"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Configurare i log di flusso del gruppo di sicurezza di rete da un modello di Azure Resource ManagerConfigure NSG Flow Logs from an Azure Resource Manager template

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfaccia della riga di comando di Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) è il modo nativo ed efficace di Azure per gestire [l'infrastruttura come codice.](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

Questo articolo illustra come abilitare i log di flusso del gruppo di sicurezza di rete a livello di codice usando un modello di Azure Resource Manager e Azure PowerShell.This article shows how to enable [NSG Flow Logs](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programmatically using an Azure Resource Manager template and Azure PowerShell. Iniziamo fornendo una panoramica delle proprietà dell'oggetto Registro di flusso del gruppo di sicurezza di gruppo, seguito da alcuni modelli di esempio. Quindi distribuire il modello utilizzando un'istanza di PowerShell locale.


## <a name="nsg-flow-logs-object"></a>Oggetto Registri di flusso del gruppo di sicurezza di rete

L'oggetto Registri di flusso del gruppo di sicurezza di rete con tutti i parametri è illustrato di seguito.
Per una panoramica completa delle proprietà, è possibile leggere il riferimento al [modello Registri di flusso del gruppo](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)di sicurezza di rete .

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Per creare una risorsa Microsoft.Network/networkWatchers/flowLogs, aggiungere il codice JSON precedente alla sezione resources del modello.


## <a name="creating-your-template"></a>Creazione del modello

Se si usano i modelli di Azure Resource Manager per la prima volta, è possibile ottenere altre informazioni usando i collegamenti seguenti.

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Esercitazione: Creare e distribuire il primo modello di Azure Resource ManagerTutorial: Create and deploy your first Azure Resource Manager template](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Di seguito sono riportati due esempi di modelli completi per impostare i registri di flusso del gruppo di sicurezza di rete.

**Esempio 1**: La versione più semplice di quanto sopra con parametri minimi passati. Il modello seguente abilita i registri di flusso del gruppo di sicurezza di rete in un gruppo di sicurezza di rete di destinazione e li archivia in un determinato account di archiviazione.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Il nome della risorsa ha il formato "Risorsa padre>/Risorsa figlio". In questo caso, la risorsa padre è<RegionName>l'istanza di Network Watcher regionale (Format: NetworkWatcher_ . Esempio: NetworkWatcher_centraluseuap)
> * targetResourceId è l'ID risorsa del gruppo di sicurezza di sicurezza di gruppo di destinazione
> * storageId è l'ID risorsa dell'account di archiviazione di destinazione

**Esempio 2:** i modelli seguenti che abilitano i registri di flusso del gruppo di sicurezza di rete (versione 2) con una conservazione per 5 giorni. Abilitazione di Analisi del traffico con un intervallo di elaborazione di 10 minuti.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Distribuzione del modello di Azure Resource ManagerDeploying your Azure Resource Manager template

Questa esercitazione presuppone che si disponga di un gruppo di risorse esistente e di un gruppo di sicurezza di rete che è possibile abilitare l'accesso al flusso.
È possibile salvare uno qualsiasi dei `azuredeploy.json`modelli di esempio precedenti localmente come . Aggiornare i valori delle proprietà in modo che puntino a risorse valide nella sottoscrizione.

Per distribuire il modello, eseguire il comando seguente in PowerShell.To deploy the template, run the following command in PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Verifica della distribuzione

Esistono un paio di modi per verificare se la distribuzione è riuscita. La console di PowerShell dovrebbe visualizzare "ProvisioningState" come "Succeeded". Inoltre, è possibile visitare la pagina del [portale Registri di flusso del gruppo](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) di sicurezza di rete per confermare le modifiche. Se si sono verificati problemi con la distribuzione, vedere Risolvere gli errori comuni di distribuzione di [Azure con Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)

## <a name="deleting-your-resource"></a>Eliminazione della risorsa
Azure abilita l'eliminazione delle risorse tramite la modalità di distribuzione "Completa". Per eliminare una risorsa Registri di flusso, specificare una distribuzione in modalità Completa senza includere la risorsa che si desidera eliminare. Ulteriori informazioni sulla [modalità di distribuzione completa](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

## <a name="next-steps"></a>Passaggi successivi

Scopri come visualizzare i dati del flusso NSG usando:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Strumenti open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Analisi del traffico di AzureAzure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
