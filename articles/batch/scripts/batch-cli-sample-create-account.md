---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un account Batch | Documentazione Microsoft
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un account Batch
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
ms.openlocfilehash: fd2f4682a04c557b69bbfce115f41c54a96d462c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-cli"></a>Creare un account Batch con l'interfaccia della riga di comando di Azure

Questo script crea un account Batch di Azure e illustra come le varie proprietà dell'account possono essere sottoposte a query e aggiornate.

## <a name="prerequisites"></a>Prerequisiti

Installare l'interfaccia della riga di comando di Azure usando le istruzioni presenti nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se questa operazione non è stata ancora eseguita.

## <a name="batch-account-sample-script"></a>Script di esempio per un account Batch

Quando si crea un account Batch, per impostazione predefinita i relativi nodi di calcolo sono assegnati internamente dal servizio Batch. I nodi di calcolo allocati saranno soggetti a una quota di memoria centrale separata e l'account può essere autenticato tramite le credenziali a chiave condivisa o un token di Azure Active Dirctory.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="batch-account-using-user-subscription-sample-script"></a>Account Batch con lo script di esempio della sottoscrizione utente

È possibile anche fare in modo che Batch crei i nodi di calcolo nella propria sottoscrizione di Azure Batch.
Gli account che allocano i nodi di calcolo nella propria sottoscrizione devono essere autenticati tramite un token di Azure Active Directory e i nodi di calcolo allocati verranno considerati ai fini della quota di sottoscrizione. Per creare un account in questa modalità, è necessario specificare un riferimento Key Vault durante la creazione dell'account.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Create Account using User Subscription")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione di uno degli script dell'esempio precedente, eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate (inclusi gli account Batch, gli account di archiviazione di Azure e gli insiemi di credenziali delle chiavi di Azure).

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, l'account Batch e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_create) | Crea l'account Batch.  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_set) | Aggiorna le proprietà dell'account Batch.  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_show) | Recupera i dettagli dell'account Batch specificato.  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#az_batch_account_keys_list) | Recupera le chiavi d'accesso dell'account Batch specificato.  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Effettua l'autenticazione con l'account Batch specificato per un'ulteriore interazione con l'interfaccia della riga di comando.  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Crea un account di archiviazione. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Crea un insieme di credenziali chiave. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | Aggiornare i criteri di sicurezza dell'insieme di credenziali delle chiavi specificato. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Batch sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Batch](../batch-cli-samples.md).
