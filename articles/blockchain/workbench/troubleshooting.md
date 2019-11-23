---
title: Risoluzione dei problemi relativi ad Azure Blockchain Workbench
description: How to troubleshoot an Azure Blockchain Workbench Preview application.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324308"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Azure Blockchain Workbench Preview troubleshooting

È disponibile uno script di PowerShell per semplificare il debug o il supporto per gli sviluppatori. Lo script genera un riepilogo e raccoglie log dettagliati per la risoluzione dei problemi. I log raccolti includono:

* Rete blockchain, ad esempio Ethereum
* Microservizi Blockchain Workbench
* Application Insights
* Azure Monitoring (Azure Monitor logs)

È possibile usare le informazioni per determinare i passaggi successivi e determinare la causa principale dei problemi.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Script di risoluzione dei problemi

Lo script di risoluzione dei problemi di PowerShell è disponibile in GitHub. [Scaricare un file ZIP](https://github.com/Azure-Samples/blockchain/archive/master.zip) o clonare l'esempio da GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Esecuzione dello script
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Eseguire lo script `collectBlockchainWorkbenchTroubleshooting.ps1` per raccogliere i log e creare un file ZIP che contiene una cartella delle informazioni di risoluzione dei problemi. ad esempio:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Questo script accetta i parametri seguenti:

| Parametro  | Description | Obbligatoria |
|---------|---------|----|
| SubscriptionID | ID sottoscrizione per creare o individuare tutte le risorse. | SÌ |
| ResourceGroupName | Nome del gruppo di risorse di Azure in cui è stato distribuito Blockchain Workbench. | SÌ |
| OutputDirectory | Percorso in cui creare il file ZIP di output. Se non è specificato, per impostazione predefinita viene usata la directory corrente. | No |
| LookbackHours | Numero di ore da usare durante il pull dei dati di telemetria. Il valore predefinito è 24 ore. Il valore massimo è 90 ore. | No |
| OmsSubscriptionId | The subscription ID where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| No |
| OmsResourceGroup |The resource group where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| No |
| OmsWorkspaceName | Nome dell'area di lavoro Log Analytics. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group | No |

## <a name="what-is-collected"></a>Quali dati vengono raccolti?

Il file ZIP di output contiene la struttura di cartelle seguente:

| File o cartella | Description  |
|---------|---------|
| \Summary.txt | Riepilogo del sistema |
| \Metrics\blockchain | Metriche relative alla blockchain |
| \Metrics\Workbench | Metriche relative a Workbench |
| \Details\Blockchain | Log dettagliati relativi alla blockchain |
| \Details\Workbench | Log dettagliati relativi a Workbench |

Il file di riepilogo offre uno snapshot dello stato complessivo dell'applicazione e l'integrità dell'applicazione. Il riepilogo indica le azioni consigliate, evidenzia gli errori principali e fornisce i metadati sui servizi in esecuzione.

La cartella **Metrics** contiene le metriche dei vari componenti di sistema nel tempo. Ad esempio, il file di output `\Details\Workbench\apiMetrics.txt` contiene un riepilogo dei diversi codici di risposta e i tempi di risposta per tutto il periodo di raccolta. La cartella **Details** contiene log dettagliati per la risoluzione di problemi specifici relativi a Workbench o alla rete blockchain sottostante. Ad esempio, `\Details\Workbench\Exceptions.csv` contiene un elenco delle eccezioni più recenti che si sono verificate nel sistema, utile per la correzione di errori relativi a contratti intelligenti o interazioni con la blockchain. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Manuale di risoluzione dei problemi relativi ad Azure Blockchain Workbench Application Insights](https://aka.ms/workbenchtroubleshooting)
