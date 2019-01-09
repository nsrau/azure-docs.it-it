---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Connettere un'app a un database SQL | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Connettere un'app a un database SQL
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e23ef4a12182aaeb6db84615106ed4e8919938bf
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634253"
---
# <a name="connect-an-app-service-app-to-a-sql-database-using-cli"></a>Connettere un'app del servizio app a un database SQL usando l'interfaccia della riga di comando

Questo script di esempio crea un database SQL di Azure e un'app del servizio app. Collega quindi il database SQL all'app usando le impostazioni dell'app.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, l'app del servizio app, il database SQL e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Consente di creare un'app del servizio app. |
| [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) | Consente di creare un server di database SQL.  |
| [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) | Consente di creare un nuovo database con il server di database SQL. |
| [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) | Genera una stringa di connessione al database. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | Crea o aggiorna un'impostazione app per un'app del servizio app. Le impostazioni delle app vengono esposte come variabili di ambiente dell'applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).
