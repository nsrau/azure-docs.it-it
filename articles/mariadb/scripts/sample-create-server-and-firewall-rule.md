---
title: Script dell'interfaccia della riga di comando di Azure - Creare un'istanza di Database di Azure per MariaDB
description: Questo script dell'interfaccia della riga di comando di Azure di esempio consente di creare un singolo server Database di Azure per MariaDB e di configurare una regola del firewall a livello di server.
services: mariadb
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 80609035933e45ea9b7cc911a908f6ed24b9f6a7
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585242"
---
# <a name="create-a-mariadb-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Creare un server MariaDB e configurare una regola del firewall tramite l'interfaccia della riga di comando di Azure
Questo script dell'interfaccia della riga di comando di Azure di esempio consente di creare un singolo server Database di Azure per MariaDB e di configurare una regola del firewall a livello di server. Dopo che lo script è stato eseguito correttamente, il server MariaDB è accessibile da tutti i servizi di Azure e dall'indirizzo IP configurato.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di eseguire l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Controllare la versione eseguendo `az --version`. Per installare l'interfaccia della riga di comando di Azure o aggiornarne la versione, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per aggiornare il nome utente e la password amministratore con i propri valori.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for mariadb, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/delete-mariadb.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Crea un server MariaDB che ospita i database. |
| [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) | Crea una regola di firewall per consentire l'accesso al server e ai database presenti sul server dall'intervallo di indirizzi IP immesso. |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'interfaccia della riga di comando di Azure: [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
- Provare a eseguire altri script: [esempi dell'interfaccia della riga di comando di Azure per Database di Azure per MariaDB](../sample-scripts-azure-cli.md)
