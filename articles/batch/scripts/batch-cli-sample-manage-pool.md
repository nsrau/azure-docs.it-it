---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Gestione dei pool in Batch | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Gestione dei pool in Batch
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7cec2a8c66868c601c38dc2dea61dda38d3cd3b2
ms.lasthandoff: 03/24/2017

---

# <a name="managing-azure-batch-pools-with-azure-cli"></a>Gestione dei pool di Azure Batch con l'interfaccia della riga di comando di Azure

Questi script illustrano alcuni degli strumenti disponibili nell'interfaccia della riga di comando di Azure per creare e gestire pool di nodi di calcolo nel servizio Azure Batch.

Per eseguire questi script è necessario che siano già stati configurati un account Batch e un'applicazione. Per altre informazioni, vedere gli [script di esempio](../batch-cli-samples.md) per ognuno di questi argomenti.

> [!NOTE]
> I comandi in questo esempio creano macchine virtuali di Azure. L'esecuzione di macchine virtuali comporterà addebiti all'account. Per ridurre al minimo tali addebiti, eliminare le macchine virtuali dopo aver eseguito l'esempio. Vedere [Eseguire la pulizia dei pool](#clean-up-pools).

Se necessario, installare l'interfaccia della riga di comando di Azure usando le istruzioni presenti nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e quindi eseguire `az login` per accedere ad Azure.

È possibile configurare i pool di Batch in due modi: con una configurazione Servizi cloud (solo Windows) o con una configurazoine Macchina virtuale (Windows e Linux).

## <a name="pool-with-cloud-service-configuration-sample-script"></a>Script di esempio per un pool con configurazione Servizi cloud

[!code-azurecli[principale](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Gestire pool con Servizi cloud")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>Script di esempio per un pool con configurazione Macchina virtuale

[!code-azurecli[principale](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Gestire pool con Macchina virtuale")]

## <a name="clean-up-pools"></a>Eseguire la pulizia dei pool

Dopo aver eseguito lo script di esempio precedente, eseguire il comando seguente per eliminare i pool.
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare e manipolare pool Batch.
Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Eseguire l'autenticazione con un account Batch.  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#list) | Elencare le applicazioni disponibili nell'account Batch.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#create) | Creare un pool di macchine virtuali.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#set) | Aggiornare le proprietà di un pool.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#list) | Elencare gli SKU agente nodo disponibili e le informazioni dell'immagine.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#resize) | Ridimensionare il numero di macchine virtuali in esecuzione nel pool specificato.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#show) | Visualizzare le proprietà di un pool.  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#delete) | Eliminare il pool specificato.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#enable) | Abilitare la scalabilità automatica in un pool e applicare una formula.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#disable) | Disabilitare la scalabilità automatica in un pool.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#list) | Elencare tutti i nodi di calcolo nel pool specificato.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#reboot) | Riavviare il nodo di calcolo specificato.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#delete) | Eliminare i nodi elencati dal pool specificato.  |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Batch sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Batch](../batch-cli-samples.md).


