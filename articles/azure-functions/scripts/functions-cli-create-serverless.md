---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un&quot;app per le funzioni per l&quot;esecuzione senza server | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un&quot;app per le funzioni per l&quot;esecuzione senza server
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a9ab21a4eb6839006c4a7eca2037308646180010
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-function-app-for-serverless-execution"></a>Creare un'app per le funzioni per l'esecuzione senza server

Questo script di esempio crea un'app per le funzioni di Azure che è un contenitore per le funzioni. L'app per le funzioni viene creata usando il [piano a consumo](../functions-scale.md#consumption-plan), ideale per i carichi di lavoro senza server guidati dagli eventi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

Questo script crea un'app per le funzioni di Azure usando il [piano di consumo](../functions-scale.md#consumption-plan) dedicato.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Creare una funzione di Azure in un piano a consumo")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#create) | Creare un account di Archiviazione di Azure. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Crea una funzione di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).

