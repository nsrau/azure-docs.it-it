---
title: Creare un'app per le funzioni con archiviazione connessa - Interfaccia della riga di comando di Azure
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una funzione di Azure che si connette ad Archiviazione di Azure
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc
ms.openlocfilehash: 9c2a70066e673bfa164a58884891f29e220c7bd6
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532825"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Creare un'app per le funzioni con una connessione a un account di archiviazione specificato 

Questo script di esempio di Funzioni di Azure crea un'app per le funzioni e connette la funzione a un account di archiviazione di Azure. L'impostazione dell'app creata che contiene la connessione può essere usata con un [trigger o un binding di archiviazione](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si usa l'interfaccia della riga di comando in locale, assicurarsi di eseguire l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

In questo esempio si crea un'app per le funzioni di Azure e si aggiunge la stringa di connessione di archiviazione a un'impostazione dell'app.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crea un gruppo di risorse con una località. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Creare un account di archiviazione. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Crea un'app per le funzioni nel [piano a consumo](../functions-scale.md#consumption-plan) senza server. |
| [az storage account show-connection-string](/cli/azure/storage/account#az-storage-account-show-connection-string) | Ottiene la stringa di connessione per l'account. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Imposta la stringa di connessione come impostazione dell'app nell'app per le funzioni. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
