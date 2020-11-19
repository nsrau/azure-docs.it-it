---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Aggiungere un'applicazione in Batch
description: Questo script di esempio mostra come aggiungere un'applicazione da usare con un'attività o un pool di Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 414a401168b1b378ed33f4904607243de0267d22
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565807"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Esempio di interfaccia della riga di comando: aggiungere un'applicazione a un account Azure Batch

Questo script mostra come aggiungere un'applicazione per l'uso con un'attività o un pool di Azure Batch. Per configurare un'applicazione da aggiungere all'account Batch, creare un pacchetto dell'eseguibile, insieme a eventuali dipendenze, come file ZIP. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Per questa esercitazione è necessaria la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti.
Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crea un account di archiviazione. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Crea l'account Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Effettua l'autenticazione con l'account Batch specificato per un'ulteriore interazione con l'interfaccia della riga di comando.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Crea un'applicazione.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Aggiunge un pacchetto di applicazione per l'applicazione specificata.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Aggiorna le proprietà di un'applicazione.  |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
