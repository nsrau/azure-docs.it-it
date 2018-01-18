---
title: 'Interfaccia della riga di comando di Azure: scaricare i log del server nel database di Azure per MySQL'
description: Questo script di esempio dell'interfaccia della riga di comando di Azure mostra come abilitare e scaricare i log del server di un database di Azure per il server MySQL.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/11/2018
ms.openlocfilehash: b0d34009d189ab136dcb6f28fdccc49b6da9e108
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Abilitare e scaricare i log di query lente del server di un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure
Questo script di esempio dell'interfaccia della riga di comando consente di abilitare e scaricare i log di query lente di un database singolo di Azure per il server MySQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo esempio è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per personalizzare nome utente e password amministratore. Sostituire <log_file_name> nei comandi az monitor con il nome del file di log del server.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az mysql server create](/cli/azure/mysql/server#az_msql_server_create) | Crea un server MySQL che ospita i database. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | Elenca i valori di configurazione per un server. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | Aggiorna la configurazione di un server. |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) | Elenca i file di log per un server. |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) | Scarica i file di log. |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'interfaccia della riga di comando di Azure: [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/overview).
- Provare a eseguire altri script: [Esempi dell'interfaccia della riga di comando di Azure per il database di Azure per MySQL](../sample-scripts-azure-cli.md)
