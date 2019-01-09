---
title: Script dell'interfaccia della riga di comando di Azure - Creare un'istanza di Database di Azure per MySQL
description: Questo script dell'interfaccia della riga di comando di Azure di esempio crea un singolo database di Azure per il server MySQL e configura una regola di firewall a livello di server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: 6a73041218ab6182d8aa3df6261697cc8dd4efe7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535502"
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Creare un server MySQL e configurare una regola di firewall tramite l'interfaccia della riga di comando di Azure
Questo script dell'interfaccia della riga di comando di Azure di esempio crea un singolo database di Azure per il server MySQL e configura una regola di firewall a livello di server. Una volta che lo script è stato eseguito correttamente, il server MySQL è accessibile da tutti i servizi di Azure e dall'indirizzo IP configurato.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di eseguire l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Controllare la versione eseguendo `az --version`. Per installare l'interfaccia della riga di comando di Azure o aggiornarne la versione, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per aggiornare il nome utente e la password amministratore con i propri valori.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Crea un server MySQL che ospita i database. |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) | Crea una regola di firewall per consentire l'accesso al server e ai database presenti sul server dall'intervallo di indirizzi IP immesso. |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sull'interfaccia della riga di comando di Azure: [Documentazione dell'interfaccia della riga di comando di Azure](/cli/azure).
- Provare altri script: [Esempi dell'interfaccia della riga di comando di Azure per il database di Azure per MySQL](../sample-scripts-azure-cli.md)
