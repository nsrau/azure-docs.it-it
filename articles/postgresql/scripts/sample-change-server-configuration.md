---
title: Script dell'interfaccia della riga di comando di Azure - Modificare le configurazioni del server
description: Questo script dell'interfaccia della riga di comando di esempio elenca tutte le opzioni di configurazione server disponibili e aggiorna il valore di una delle opzioni.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 9aff0dca1d09e31f0c6f761dd236a7ef7439486b
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Elencare e aggiornare le configurazioni di un server di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
Questo script dell'interfaccia della riga di comando di esempio elenca tutti i parametri di configurazione disponibili, nonché i relativi valori consentiti per un server di Database di Azure per PostgreSQL e imposta *log_retention_days* su un valore diverso da quello predefinito.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di eseguire l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Controllare la versione eseguendo `az --version`. Per installare l'interfaccia della riga di comando di Azure o aggiornarne la versione, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per aggiornare il nome utente e la password amministratore con i propri valori.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=18-19 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Crea un server PostgreSQL che ospita i database. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Elenca le configurazioni di un server di Database di Azure per PostgreSQL. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Aggiorna la configurazione di un server di Database di Azure per PostgreSQL. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) | Mostra la configurazione di un server di Database di Azure per PostgreSQL. |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'interfaccia della riga di comando di Azure: [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/overview).
- Provare a eseguire altri script: [esempi dell'interfaccia della riga di comando di Azure per il database di Azure per PostgreSQL](../sample-scripts-azure-cli.md)
- Per altre informazioni sui parametri di server, vedere [Come configurare i parametri del server nel portale di Azure](../howto-configure-server-parameters-using-portal.md).
