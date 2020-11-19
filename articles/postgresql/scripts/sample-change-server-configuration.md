---
title: Script dell'interfaccia della riga di comando di Azure - Modificare le configurazioni del server (PostgreSQL)
description: Questo script dell'interfaccia della riga di comando di esempio elenca tutte le opzioni di configurazione server disponibili e aggiorna il valore di una delle opzioni.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 313c3a59c1b47b4cc6ee6a38e46e85a2343bfb31
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660526"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Elencare e aggiornare le configurazioni di un server di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
Questo script dell'interfaccia della riga di comando di esempio elenca tutti i parametri di configurazione disponibili, nonché i relativi valori consentiti per un server di Database di Azure per PostgreSQL e imposta *log_retention_days* su un valore diverso da quello predefinito.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per aggiornare il nome utente e la password amministratore con i propri valori.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az postgres server create](/cli/azure/postgres/server) | Crea un server PostgreSQL che ospita i database. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Elenca le configurazioni di un server di Database di Azure per PostgreSQL. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Aggiorna la configurazione di un server di Database di Azure per PostgreSQL. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Mostra la configurazione di un server di Database di Azure per PostgreSQL. |
| [az group delete](/cli/azure/group) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'interfaccia della riga di comando di Azure: [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
- Provare altri script: [Azure CLI samples for Azure Database for PostgreSQL](../sample-scripts-azure-cli.md) (Esempi di interfaccia della riga di comando di Azure per Database di Azure per PostgreSQL)
- Per altre informazioni sui parametri di server, vedere [Come configurare i parametri del server nel portale di Azure](../howto-configure-server-parameters-using-portal.md).
