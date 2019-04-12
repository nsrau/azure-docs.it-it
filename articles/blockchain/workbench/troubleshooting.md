---
title: Risoluzione dei problemi relativi ad Azure Blockchain Workbench
description: Come risolvere i problemi di un'applicazione Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b55c84773d99c325689fbc5182e75c7cb108d00a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890016"
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Risoluzione dei problemi relativi ad Azure Blockchain Workbench

È disponibile uno script di PowerShell per semplificare il debug o il supporto per gli sviluppatori. Lo script genera un riepilogo e raccoglie log dettagliati per la risoluzione dei problemi. I log raccolti includono:

* Rete blockchain, ad esempio Ethereum
* Microservizi Blockchain Workbench
* Application Insights
* Monitoraggio di Azure (log di monitoraggio di Azure)

È possibile usare le informazioni per determinare i passaggi successivi e determinare la causa principale dei problemi.

## <a name="troubleshooting-script"></a>Script di risoluzione dei problemi

Lo script di risoluzione dei problemi di PowerShell è disponibile in GitHub. [Scaricare un file ZIP](https://github.com/Azure-Samples/blockchain/archive/master.zip) o clonare l'esempio da GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Esecuzione dello script
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Eseguire lo script `collectBlockchainWorkbenchTroubleshooting.ps1` per raccogliere i log e creare un file ZIP che contiene una cartella delle informazioni di risoluzione dei problemi. Ad esempio: 

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Questo script accetta i parametri seguenti:

| Parametro  | DESCRIZIONE | Obbligatorio |
|---------|---------|----|
| SubscriptionID | ID sottoscrizione per creare o individuare tutte le risorse. | Sì |
| ResourceGroupName | Nome del gruppo di risorse di Azure in cui è stato distribuito Blockchain Workbench. | Sì |
| OutputDirectory | Percorso in cui creare il file ZIP di output. Se non è specificato, per impostazione predefinita viene usata la directory corrente. | No  |
| LookbackHours | Numero di ore da usare durante il pull dei dati di telemetria. Il valore predefinito è 24 ore. Il valore massimo è 90 ore. | No  |
| OmsSubscriptionId | L'ID sottoscrizione in cui log di monitoraggio di Azure viene distribuito. Passare questo parametro solo se i log di monitoraggio di Azure per la blockchain di rete viene distribuito di fuori di gruppo di risorse di Blockchain Workbench.| No  |
| OmsResourceGroup |Il gruppo di risorse in cui log di monitoraggio di Azure viene distribuito. Passare questo parametro solo se i log di monitoraggio di Azure per la blockchain di rete viene distribuito di fuori di gruppo di risorse di Blockchain Workbench.| No  |
| OmsWorkspaceName | Nome dell'area di lavoro Log Analytics. Questo parametro verrà passato solo se i log di monitoraggio di Azure per la blockchain di rete viene distribuito di fuori di gruppo di risorse di Blockchain Workbench | No  |

## <a name="what-is-collected"></a>Quali dati vengono raccolti?

Il file ZIP di output contiene la struttura di cartelle seguente:

| File o cartella | DESCRIZIONE  |
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
