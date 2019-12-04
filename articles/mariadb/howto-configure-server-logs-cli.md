---
title: Accedere ai log di query lente-interfaccia della riga di comando di Azure-database Azure per MariaDB
description: Questo articolo descrive come accedere ai log lenti nel database di Azure per MariaDB usando l'utilità della riga di comando dell'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 32e73835732538813f90de5cb737429373c3762a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767382"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configurare e accedere ai log di query lente usando l'interfaccia della riga di comando di Azure
È possibile scaricare i log di query lente del database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida è necessario:
- [Database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o Azure Cloud Shell nel browser

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Configurare la registrazione per Database di Azure per MariaDB
Per configurare il server per l'accesso al log delle query lente di MariaDB, seguire la procedura seguente:
1. Attivare la registrazione impostando il parametro **slow\_query\_log** su ON.
2. Regolare gli altri parametri, ad esempio **long\_query\_time** e **log\_slow\_admin\_statements**.

Per informazioni su come impostare il valore di questi parametri tramite l'interfaccia della riga di comando di Azure, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-cli.md).

Il comando dell'interfaccia della riga di comando seguente, ad esempio, attiva il log delle query lente, imposta la durata di una query prolungata su 10 secondi e quindi disattiva la registrazione dell'istruzione per un amministratore lento. Infine elenca le opzioni di configurazione da verificare.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Elencare i log del server per Database di Azure per MariaDB
Per elencare i file di log di query lente disponibili per il server, eseguire il comando [AZ mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

È possibile elencare i file di log per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup**. Quindi indirizzare l'elenco dei file di log a un file di testo denominato **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Scaricare i log dal server
Tramite il comando [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) è possibile scaricare i singoli file di log per il server.

Usare l'esempio seguente per scaricare il file di log specifico per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [log di query lente nel database di Azure per MariaDB](concepts-server-logs.md).
