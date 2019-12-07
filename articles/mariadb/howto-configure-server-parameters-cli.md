---
title: Configurare parametri del server-interfaccia della riga di comando di Azure-database di Azure per MariaDB
description: Questo articolo descrive come configurare i parametri del servizio in Database di Azure per MariaDB usando l'utilità dell'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 707f4eca440c0e8461420ff0bbc5e67f8e5ad69d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888513"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure
È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un'istanza di Database di Azure per il server MariaDB usando la utilità dell'interfaccia della riga di comando di Azure. Un subset delle configurazioni del motore viene esposto a livello di server e può essere modificato.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida è necessario:
- [Un database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- L'utilità della riga di comando dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). In alternativa, è possibile usare Azure Cloud Shell nel browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Elencare i parametri di configurazione del server per Database di Azure per il server MariaDB
Per elencare tutti i parametri modificabili in un server e i relativi valori, eseguire il comando [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list).

È possibile elencare i parametri di configurazione per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Per la definizione di ognuno dei parametri elencati, vedere la sezione di riferimento di MariaDB nell'articolo sulle [variabili di sistema del server](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Visualizzare i dettagli dei parametri di configurazione server
Per visualizzare i dettagli di un determinato parametro di configurazione per un server, eseguire il comando [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show).

Questo esempio illustra i dettagli del parametro di configurazione **slow\_query\_log** per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificare un valore di un parametro di configurazione server
È anche possibile modificare il valore di un determinato parametro di configurazione del server, che aggiorna il valore di configurazione sottostante del motore del server MariaDB. Per aggiornare la configurazione, usare il comando [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set). 

Per aggiornare il parametro di configurazione **slow\_query\_log** per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Per reimpostare il valore di un parametro di configurazione, omettere il parametro facoltativo `--value`. In questo caso, il servizio applica il valore predefinito. Nell'esempio precedente, sarà simile a quanto segue:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

In questo modo il parametro di configurazione **slow\_query\_log** viene reimpostato sul valore predefinito **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Uso del parametro di fuso orario

### <a name="populating-the-time-zone-tables"></a>Popolare le tabelle di fuso orario

Per popolare le tabelle di fuso orario nel server, è possibile chiamare la stored procedure `az_load_timezone` da uno strumento come la riga di comando MariaDB o MariaDB Workbench.

> [!NOTE]
> Se si esegue il comando `az_load_timezone` da MariaDB Workbench, può essere necessario disattivare la modalità di aggiornamento sicuro tramite `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> È necessario riavviare il server per assicurarsi che le tabelle del fuso orario siano popolate correttamente. Per riavviare il server, usare il [portale di Azure](howto-restart-server-portal.md) o l' [interfaccia](howto-restart-server-cli.md)della riga di comando.

Per visualizzare i valori di fuso orario disponibili, eseguire questo comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Impostazione del fuso orario a livello globale

Il fuso orario a livello globale può essere impostato tramite il comando [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set).

Il comando seguente aggiorna il parametro di configurazione **time\_zone** per il server **mydemoserver.mariadb.database.azure.com** nel gruppo di risorse **myresourcegroup** su **US/Pacific**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Impostazione del fuso orario a livello di sessione

Per impostare il fuso orario a livello di sessione, eseguire il comando `SET time_zone` da uno strumento come la riga di comando MariaDB o MariaDB Workbench. L'esempio seguente imposta il fuso orario su **US/Pacific**.  

```sql
SET time_zone = 'US/Pacific';
```

Vedere la documentazione relativa a MariaDB per le [Funzioni di data e ora](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Passaggi successivi

- Come configurare [i parametri del server nel portale di Azure](howto-server-parameters.md)
