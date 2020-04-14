---
title: Accedere ai log delle query lente - Interfaccia della riga di comando di Azure - Database di Azure per MariaDBAccess slow query logs - Azure CLI - Azure Database for MariaDB
description: Questo articolo descrive come accedere ai log lenti nel database di Azure per MariaDB usando l'utilità della riga di comando dell'interfaccia della riga di comando dell'interfaccia della riga di comando di Azure.This article describes how to access the slow logs in Azure Database for MariaDB by using the Azure CLI command-line utility.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 75efdd8ed855fe78651fce5828aacb2384052ae5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270537"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configurare e accedere ai log di query lenti tramite l'interfaccia della riga di comando di AzureConfigure and access slow query logs by using Azure CLI
È possibile scaricare i log di query lente del database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure, l'utilità della riga di comando di Azure.You can download the Azure Database for MariaDB slow query logs by using Azure CLI, the Azure command-line utility.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o Azure Cloud Shell nel browser

## <a name="configure-logging"></a>Configurare la registrazione
Per configurare il server per l'accesso al log delle query lente di MySQL, seguire questa procedura:
1. Attivare la registrazione delle query lente impostando il parametro del **log di query\_\_lento** su ON.
2. Selezionare la posizione in cui eseguire l'output dei log utilizzando l'output del **log.\_** Per inviare i log sia all'archiviazione locale che ai log di diagnostica di Monitoraggio di Azure, selezionare **File**. Per inviare i log solo ai log di Monitoraggio di Azure, **selezionare NessunoTo** send logs only to Azure Monitor Logs, select None
3. Regolare gli altri parametri, ad esempio **long\_query\_time** e **log\_slow\_admin\_statements**.

Per informazioni su come impostare il valore di questi parametri tramite l'interfaccia della riga di comando di Azure, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-cli.md).

Il comando dell'interfaccia della riga di comando seguente, ad esempio, attiva il log delle query lente, imposta la durata di una query prolungata su 10 secondi e quindi disattiva la registrazione dell'istruzione per un amministratore lento. Infine elenca le opzioni di configurazione da verificare.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Elencare i log del server per Database di Azure per MariaDB
Se **log_output** è configurato su "File", è possibile accedere ai registri direttamente dall'archivio locale del server. Per elencare i file di log di query lente disponibili per il server, eseguire il comando [az mariadb server-logs list.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

È possibile elencare i file di log per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup**. Quindi indirizzare l'elenco dei file di log a un file di testo denominato **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Scaricare i log dal server
Se **log_output** è configurato su "File", è possibile scaricare singoli file di registro dal server con il comando [az mariadb server-logs download.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download)

Usare l'esempio seguente per scaricare il file di log specifico per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui log di query lenti nel database di [Azure per MariaDB](concepts-server-logs.md).
