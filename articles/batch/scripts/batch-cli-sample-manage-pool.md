---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Gestione dei pool in Batch | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Gestione dei pool in Batch
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
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: ae7eab97c1da1113b0248b74a9dd67de8ce49e36
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---

# <a name="managing-azure-batch-pools-with-azure-cli"></a>Gestione dei pool di Azure Batch con l'interfaccia della riga di comando di Azure

Questi script illustrano alcuni degli strumenti disponibili nell'interfaccia della riga di comando di Azure per creare e gestire pool di nodi di calcolo nel servizio Azure Batch.

> [!NOTE]
> I comandi in questo esempio creano macchine virtuali di Azure. L'esecuzione di macchine virtuali comporterà addebiti all'account. Per ridurre al minimo tali addebiti, eliminare le macchine virtuali dopo aver eseguito l'esempio. Vedere [Eseguire la pulizia dei pool](#clean-up-pools).

È possibile configurare i pool di Batch in due modi: con una configurazione Servizi cloud (solo Windows) o con una configurazoine Macchina virtuale (Windows e Linux). Gli script di esempio seguenti mostrano come creare pool con entrambe le configurazioni.

## <a name="prerequisites"></a>Prerequisiti

- Installare l'interfaccia della riga di comando di Azure usando le istruzioni presenti nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se questa operazione non è stata ancora eseguita.
- Creare un account Batch, se non è già disponibile. Vedere [Creare un account Batch con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) per uno script di esempio che crea un account.
- Se questa operazione non è stata già eseguita, configurare un'applicazione in modo che venga eseguita da un'attività di avvio. Vedere [Aggiunta di applicazioni ad Azure Batch con l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) per uno script di esempio che crea un'applicazione e carica un pacchetto dell'applicazione in Azure.

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
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Eseguire l'autenticazione con un account Batch.  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#az_batch_application_summary_list) | Elencare le applicazioni disponibili nell'account Batch.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Creare un pool di macchine virtuali.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | Aggiornare le proprietà di un pool.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | Elencare gli SKU agente nodo disponibili e le informazioni dell'immagine.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | Ridimensionare il numero di macchine virtuali in esecuzione nel pool specificato.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | Visualizzare le proprietà di un pool.  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_delete) | Eliminare il pool specificato.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Abilitare la scalabilità automatica in un pool e applicare una formula.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Disabilitare la scalabilità automatica in un pool.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | Elencare tutti i nodi di calcolo nel pool specificato.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | Riavviare il nodo di calcolo specificato.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | Eliminare i nodi elencati dal pool specificato.  |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Batch sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Batch](../batch-cli-samples.md).


