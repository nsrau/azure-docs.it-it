---
title: 'Avvio rapido: Configurare log del flusso del gruppo di sicurezza di rete usando un modello di Azure Resource Manager'
description: Informazioni su come abilitare i log del flusso del gruppo di sicurezza di rete a livello di codice usando un modello di Azure Resource Manager e Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87986318"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Avvio rapido: Configurare i log del flusso del gruppo di sicurezza di rete da un modello di Azure Resource Manager

In questo avvio rapido verranno abilitati i [log del flusso del gruppo di sicurezza di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) a livello di codice usando un modello di [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) e Azure PowerShell. 

Per iniziare, è necessario fornire una panoramica delle proprietà dell'oggetto log del flusso del gruppo di sicurezza di rete, seguito da alcuni modelli di esempio. Il modello viene quindi distribuito con un'istanza di PowerShell locale.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="nsg-flow-logs-object"></a>Oggetto log del flusso del gruppo di sicurezza di rete

Di seguito è illustrato l'oggetto log del flusso del gruppo di sicurezza di rete con tutti i parametri.
Per una panoramica completa delle proprietà, è possibile leggere le [informazioni di riferimento del modello log del flusso del gruppo di sicurezza di rete](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

L'esempio seguente di un modello completo è la versione più semplice con i parametri minimi passati per configurare i log del flusso del gruppo di sicurezza di rete. Per altri esempi, passare a questo [collegamento](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager).

**Esempio**: Il modello seguente abilita i log del flusso del gruppo di sicurezza di rete per un gruppo di sicurezza di rete di destinazione e archivia tali log in un determinato account di archiviazione.

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


## <a name="deploying-your-azure-resource-manager-template"></a>Distribuzione del modello di Azure Resource Manager

Questa esercitazione presuppone che siano presenti un gruppo di risorse e un gruppo di sicurezza di rete in cui abilitare la funzionalità di registrazione del flusso.
È possibile salvare in locale i modelli di esempio precedenti come `azuredeploy.json`. Aggiornare i valori delle proprietà in modo che puntino a risorse valide nella sottoscrizione.

Per distribuire il modello, eseguire il comando seguente in PowerShell.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> I comandi precedenti distribuiscono una risorsa nel gruppo di risorse NetworkWatcherRG e non nel gruppo di risorse contenente il gruppo di sicurezza di rete.


## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Esistono due modi per verificare se la distribuzione ha avuto esito positivo. Nella console di PowerShell è necessario che per "ProvisioningState" sia visualizzato il valore "Succeeded". Inoltre, è possibile visitare la [pagina del portale relativa ai log del flusso del gruppo di sicurezza di rete](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) per confermare le modifiche apportate. Se si sono verificati problemi durante la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Eliminazione della risorsa
Azure consente l'eliminazione di risorse tramite la modalità di distribuzione "Completa". Per eliminare una risorsa dei log del flusso, specificare una distribuzione in modalità Completa senza includere la risorsa che si vuole eliminare. Per altre informazioni, leggere [Modalità di distribuzione completa](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode).

In alternativa è possibile disabilitare un log del flusso del gruppo di sicurezza di rete dal portale di Azure, come illustrato nella procedura seguente:
1. Accedere al portale di Azure
2. Nell'angolo in alto a sinistra del portale selezionare **Tutti i servizi**. Nella **casella del filtro** digitare *Network Watcher*. Selezionare **Network Watcher** quando viene visualizzato tra i risultati della ricerca.
3. In **LOG** selezionare **Log del flusso del NSG**
4. Dall'elenco di gruppi di sicurezza di rete selezionare il gruppo di sicurezza di rete per cui si vogliono disabilitare i log dei flussi
5. In **Impostazioni dei log dei flussi** impostare lo stato dei log dei flussi su **Off**
6. Scorrere verso il basso e selezionare **Salva**

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido sono stati abilitati log del flusso del gruppo di sicurezza di rete. È ora necessario ottenere informazioni su come visualizzare i dati del lusso del gruppo di sicurezza di rete con: 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Strumenti open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Analisi del traffico di Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
