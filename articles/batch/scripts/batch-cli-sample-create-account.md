---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un account Batch | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un account Batch
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 4d37dfc6c7110aa32788bbd3c3f81cd84a1426d2
ms.lasthandoff: 03/24/2017

---

# <a name="create-a-batch-account-with-the-azure-cli"></a>Creare un account Batch con l'interfaccia della riga di comando di Azure

Questo script crea un account Batch di Azure e illustra come le varie proprietà dell'account possono essere sottoposte a query e aggiornate.

Se necessario, installare l'interfaccia della riga di comando di Azure usando le istruzioni presenti nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e quindi eseguire `az login` per accedere ad Azure.

## <a name="batch-account-sample-script"></a>Script di esempio per un account Batch

Quando si crea un account Batch, per impostazione predefinita i relativi nodi di calcolo sono assegnati internamente dal servizio Batch. I nodi di calcolo allocati saranno soggetti a una quota di memoria centrale separata e l'account può essere autenticato tramite le credenziali a chiave condivisa o un token di Azure Active Dirctory.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "Creare un account")]

## <a name="batch-account-using-user-subscription-sample-script"></a>Account Batch con lo script di esempio della sottoscrizione utente

È possibile anche fare in modo che Batch crei i nodi di calcolo nella propria sottoscrizione di Azure Batch.
Gli account che allocano i nodi di calcolo nella propria sottoscrizione devono essere autenticati tramite un token di Azure Active Directory e i nodi di calcolo allocati verranno considerati ai fini della quota di sottoscrizione. Per creare un account in questa modalità, è necessario specificare un riferimento Key Vault durante la creazione dell'account.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Creare un account mediante una sottoscrizione utente")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione di uno degli script dell'esempio precedente, eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate (inclusi gli account Batch, gli account di archiviazione di Azure e gli insiemi di credenziali delle chiavi di Azure).

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, l'account Batch e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#create) | Crea l'account Batch.  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#set) | Aggiorna le proprietà dell'account Batch.  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#show) | Recupera i dettagli dell'account Batch specificato.  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#list) | Recupera le chiavi d'accesso dell'account Batch specificato.  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Effettua l'autenticazione con l'account Batch specificato per un'ulteriore interazione con l'interfaccia della riga di comando.  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crea un account di archiviazione. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#create) | Crea un insieme di credenziali chiave. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#set-policy) | Aggiornare i criteri di sicurezza dell'insieme di credenziali delle chiavi specificato. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Batch sono disponibili nella [documentazione dell'interfaccia della riga di comando di Azure Batch](../batch-cli-samples.md).

