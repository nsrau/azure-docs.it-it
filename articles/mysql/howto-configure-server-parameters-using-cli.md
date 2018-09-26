---
title: Configurare i parametri del servizio in Database di Azure per MySQL
description: Questo articolo descrive come configurare i parametri del servizio in Database di Azure per MySQL usando l'utilità dell'interfaccia della riga di comando di Azure.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: b45975bc019da2859da87a40e46970d849312e38
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957058"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure
È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un'istanza di Database di Azure per il server MySQL usando l'utility dell'interfaccia della riga di comando di Azure. Un subset delle configurazioni del motore viene esposto a livello di server e può essere modificato. 

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Un'istanza di Database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- L'utilità della riga di comando dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). In alternativa, è possibile usare Azure Cloud Shell nel browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Elencare i parametri di configurazione del server per Database di Azure per il server MySQL
Per elencare tutti i parametri modificabili in un server e i relativi valori, eseguire il comando [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list).

È possibile elencare i parametri di configurazione per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Per la definizione di ognuno dei parametri elencati, vedere la sezione di riferimento di MySQL nell'articolo sulle [variabili di sistema del server](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visualizzare i dettagli dei parametri di configurazione server
Per visualizzare i dettagli di un determinato parametro di configurazione per un server, eseguire il comando [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show).

Questo esempio illustra i dettagli del parametro di configurazione **slow\_query\_log** per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificare un valore di un parametro di configurazione server
È anche possibile modificare il valore di un determinato parametro di configurazione del server, che aggiorna il valore di configurazione sottostante del motore del server MySQL. Per aggiornare la configurazione, usare il comando [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set). 

Per aggiornare il parametro di configurazione **slow\_query\_log** per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Per reimpostare il valore di un parametro di configurazione, omettere il parametro facoltativo `--value`. In questo caso, il servizio applica il valore predefinito. Nell'esempio precedente, sarà simile a quanto segue:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
In questo modo il parametro di configurazione **slow\_query\_log** viene reimpostato sul valore predefinito **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Uso del parametro di fuso orario

### <a name="populating-the-time-zone-tables"></a>Popolare le tabelle di fuso orario

Per popolare le tabelle di fuso orario nel server, è possibile chiamare la stored procedure `az_load_timezone` da uno strumento come la riga di comando MySQL o MySQL Workbench.

> [!NOTE]
> Se si esegue il comando `az_load_timezone` da MySQL Workbench, può essere necessario disattivare la modalità di aggiornamento sicuro tramite `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Per visualizzare i valori di fuso orario disponibili, eseguire questo comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Impostazione del fuso orario a livello globale

Il fuso orario a livello globale può essere impostato tramite il comando [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set).

Il comando seguente aggiorna il parametro di configurazione **time\_zone** per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup** su **US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Impostazione del fuso orario a livello di sessione

Per impostare il fuso orario a livello di sessione, eseguire il comando `SET time_zone` da uno strumento come la riga di comando MySQL o MySQL Workbench. L'esempio seguente imposta il fuso orario su **US/Pacific**.  

```sql
SET time_zone = 'US/Pacific';
```

Per le [funzioni di data e ora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz), vedere la documentazione di MySQL.


## <a name="next-steps"></a>Passaggi successivi

- Come configurare [i parametri del server nel portale di Azure](howto-server-parameters.md)