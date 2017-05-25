---
title: Creare una funzione di Azure che si connette a un DocumentDB di Azure | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando - Creare una funzione di Azure che si connette a un database DocumentDB di Azure
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 46e0fe827b7b34010d14a31ff377d4854ec62f6e
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Creare una funzione di Azure che si connette a un database Azure Cosmos DB | Documentazione Microsoft

Questo script di esempio crea un'app per le funzioni di Azure e si connette a un database Azure Cosmos DB.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

Esempio di creazione di app

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Creare una funzione di Azure che si connette a un database Azure Cosmos DB")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Accedere ad Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Creare un gruppo di risorse con una posizione |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Creare un account di archiviazione |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Creare una nuova app per le funzioni |
| [az documentdb create](https://docs.microsoft.com/cli/azure/documentdb#create) | Crea un database DocumentDB |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Eseguire la pulizia |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).





