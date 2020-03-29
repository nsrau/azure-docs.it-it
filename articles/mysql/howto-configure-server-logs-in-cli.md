---
title: Accedere ai log delle query lente - Interfaccia della riga di comando di Azure - Database di Azure per MySQLAccess slow query logs - Azure CLI - Azure Database for MySQL
description: Questo articolo descrive come accedere ai log di query lenti nel database di Azure per MySQL usando l'interfaccia della riga di comando di Azure.This article describes how to access the slow query logs in Azure Database for MySQL by using the Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 93840af61a69599447588be01869a20290b2db94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062471"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configurare e accedere ai log di query lenti tramite l'interfaccia della riga di comando di AzureConfigure and access slow query logs by using Azure CLI
È possibile scaricare il database di Azure per i log delle query lente MySQL usando l'interfaccia della riga di comando di Azure, l'utilità della riga di comando di Azure.You can download the Azure Database for MySQL slow query logs by using Azure CLI, the Azure command-line utility.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Server del Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o Azure Cloud Shell nel browser

## <a name="configure-logging"></a>Configurare la registrazione
Per configurare il server per l'accesso al log delle query lente di MySQL, seguire questa procedura:
1. Attivare la registrazione delle query lente impostando il parametro del **log di query\_\_lento** su ON.
2. Regolare gli altri parametri, ad esempio **long\_query\_time** e **log\_slow\_admin\_statements**.

Per informazioni su come impostare il valore di questi parametri tramite l'interfaccia della riga di comando di Azure, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).

Il comando dell'interfaccia della riga di comando seguente, ad esempio, attiva il log delle query lente, imposta la durata di una query prolungata su 10 secondi e quindi disattiva la registrazione dell'istruzione per un amministratore lento. Infine elenca le opzioni di configurazione da verificare.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Elencare i log per il database di Azure per il server MySQL
Per elencare i file di log di query lente disponibili per il server, eseguire il comando [az mysql server-logs list.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

È possibile elencare i file di log per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**. Quindi indirizzare l'elenco dei file di log a un file di testo denominato **log\_files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Scaricare i log dal server
Tramite il comando [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) è possibile scaricare i singoli file di log per il server. 

Usare l'esempio seguente per scaricare il file di log specifico per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui log di query lenti nel database di [Azure per MySQL](concepts-server-logs.md).
