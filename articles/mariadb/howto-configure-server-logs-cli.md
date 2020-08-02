---
title: Accedere ai log di query lente-interfaccia della riga di comando di Azure-database Azure per MariaDB
description: Questo articolo descrive come accedere ai log lenti nel database di Azure per MariaDB usando l'utilità della riga di comando dell'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cd74feaebe5a89667668c05e332ed9d3c7cdad5d
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87490252"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Configurare e accedere ai log di query lente del database di Azure per Maria usando l'interfaccia della riga di comando di Azure

È possibile scaricare i log di query lente del database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o Azure Cloud Shell nel browser

## <a name="configure-logging"></a>Configurare la registrazione
Per configurare il server per l'accesso al log delle query lente di MySQL, seguire questa procedura:
1. Attivare la registrazione lenta delle query impostando il parametro **Slow \_ query \_ log** su on.
2. Selezionare la posizione in cui restituire i log usando l' ** \_ output del log**. Per inviare i log ai log di diagnostica di monitoraggio di Azure e di archiviazione locale, selezionare **file**. Per inviare i log solo ai log di monitoraggio di Azure, selezionare **nessuno**
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
Se **log_output** è configurato su "file", è possibile accedere ai log direttamente dalla risorsa di archiviazione locale del server. Per elencare i file di log di query lente disponibili per il server, eseguire il comando [AZ mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

È possibile elencare i file di log per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup**. Quindi indirizzare l'elenco dei file di log a un file di testo denominato **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Scaricare i log dal server
Se **log_output** è configurato su "file", è possibile scaricare i singoli file di log dal server con il comando [AZ mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) .

Usare l'esempio seguente per scaricare il file di log specifico per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [log di query lente nel database di Azure per MariaDB](concepts-server-logs.md).
