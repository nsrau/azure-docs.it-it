---
title: Creare una funzione di Azure che si connette ad Archiviazione di Azure | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una funzione di Azure che si connette ad Archiviazione di Azure
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 3925c1a3d2c5a9fc3a82e8823dbdeffabeb38a3a
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---
# <a name="integrate-function-app-into-azure-storage-account"></a>Integrare l'app per le funzioni nell'account di archiviazione di Azure

Questo script di esempio crea un'app per le funzioni e un account di archiviazione.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

In questo esempio si crea un'app per le funzioni di Azure e si aggiunge la stringa di connessione di archiviazione a un'impostazione dell'app.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrare un'app per le funzioni nell'account di archiviazione di Azure")]


## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, eseguire questo comando per rimuovere il gruppo di risorse, l'app del servizio app e tutte le risorse correlate:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Accedere ad Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Creare un gruppo di risorse con una posizione |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Creare un account di archiviazione |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Creare una nuova app per le funzioni |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Eseguire la pulizia |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).

