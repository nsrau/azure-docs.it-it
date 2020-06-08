---
title: Network Watcher - Creare log del flusso del gruppo di sicurezza di rete con un modello di Azure Resource Manager
description: Usare un modello di Azure Resource Manager e PowerShell per configurare facilmente i log del flusso del gruppo di sicurezza di rete.
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
ms.openlocfilehash: 7b7580843bc5ad90ebc394c41e81f4d16f51ccb1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701756"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Configurare log del flusso del gruppo di sicurezza di rete da un modello di Azure Resource Manager

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfaccia della riga di comando di Azure](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) è il potente strumento nativo di Azure per gestire l'[infrastruttura come codice](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code).

Questo articolo illustra come abilitare i [log del flusso del gruppo di sicurezza di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) a livello di codice usando un modello di Azure Resource Manager e Azure PowerShell. Per iniziare, è necessario fornire una panoramica delle proprietà dell'oggetto log del flusso del gruppo di sicurezza di rete, seguito da alcuni modelli di esempio. Il modello viene quindi distribuito con un'istanza di PowerShell locale.


## <a name="nsg-flow-logs-object"></a>Oggetto log del flusso del gruppo di sicurezza di rete

Di seguito è illustrato l'oggetto log del flusso del gruppo di sicurezza di rete con tutti i parametri.
Per una panoramica completa delle proprietà, è possibile leggere le [informazioni di riferimento del modello log del flusso del gruppo di sicurezza di rete](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

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
* [Esercitazione: Creare e distribuire il primo modello di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Di seguito sono illustrati due esempi di modelli completi per la configurazione dei log del flusso del gruppo di sicurezza di rete.

**Esempio 1**:  la versione più semplice del modello precedente con un numero minimo di parametri passati. Il modello seguente abilita i log del flusso del gruppo di sicurezza di rete per un gruppo di sicurezza di rete di destinazione e archivia tali log in un determinato account di archiviazione.

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
> * Il nome della risorsa ha il formato "Risorsa padre_Risorsa figlio". In questo caso, la risorsa padre è l'istanza di Network Watcher dell'area geografica, nel formato NetworkWatcher_NomeAreaGeografica. Esempio: NetworkWatcher_centraluseuap.
> * targetResourceId è l'ID di risorsa del gruppo di sicurezza di rete di destinazione.
> * storageId è l'ID di risorsa dell'account di archiviazione di destinazione.

**Esempio 2:** I modelli seguenti abilitano i log del flusso del gruppo di sicurezza di rete (versione 2) conservandoli per 5 giorni. Abilitazione di Analisi del traffico con un intervallo di elaborazione di 10 minuti.

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

## <a name="deploying-your-azure-resource-manager-template"></a>Distribuzione del modello di Azure Resource Manager

Questa esercitazione presuppone che siano presenti un gruppo di risorse e un gruppo di sicurezza di rete in cui abilitare la funzionalità di registrazione del flusso.
È possibile salvare in locale i modelli di esempio precedenti come `azuredeploy.json`. Aggiornare i valori delle proprietà in modo che puntino a risorse valide nella sottoscrizione.

Per distribuire il modello, eseguire il comando seguente in PowerShell.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> I comandi precedenti distribuiscono una risorsa nel gruppo di risorse NetworkWatcherRG e non nel gruppo di risorse contenente il gruppo di sicurezza di rete.


## <a name="verifying-your-deployment"></a>Verifica della distribuzione

Esistono due modi per verificare se la distribuzione ha avuto esito positivo. Nella console di PowerShell è necessario che per "ProvisioningState" sia visualizzato il valore "Succeeded". Inoltre, è possibile visitare la [pagina del portale relativa ai log del flusso del gruppo di sicurezza di rete](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) per confermare le modifiche apportate. Se si sono verificati problemi durante la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Eliminazione della risorsa
Azure consente l'eliminazione di risorse tramite la modalità di distribuzione "Completa". Per eliminare una risorsa dei log del flusso, specificare una distribuzione in modalità Completa senza includere la risorsa che si vuole eliminare. Per altre informazioni, leggere [Modalità di distribuzione completa](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode).

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni su come visualizzare i dati del flusso del gruppo di sicurezza di rete usando:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Strumenti open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Analisi del traffico di Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
