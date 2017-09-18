---
title: Creare una funzione di Azure che si connette ad Archiviazione di Azure | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una funzione di Azure che si connette ad Archiviazione di Azure
services: functions
documentationcenter: functions
author: rachelappel
manager: cfowler
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
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: af90702601d1bd05836dbf2b20cd3e318832b07c
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---
# <a name="integrate-function-app-into-azure-storage-account"></a>Integrare l'app per le funzioni nell'account di archiviazione di Azure

Questo script di esempio crea un'app per le funzioni e un account di archiviazione.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

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
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Creare un gruppo di risorse con una posizione |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Creare un account di archiviazione |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Creare una nuova app per le funzioni |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Eseguire la pulizia |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).

