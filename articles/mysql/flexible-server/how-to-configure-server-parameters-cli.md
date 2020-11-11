---
title: Configurare i parametri del server-interfaccia della riga di comando di Azure-server flessibile per database di Azure
description: Questo articolo descrive come configurare i parametri del servizio nel database di Azure per il server flessibile MySQL usando l'utilità della riga di comando dell'interfaccia della riga di comando di Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58e7c024d6494aee745884997e42b527c51ab237
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489540"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Configurare i parametri del server nel database di Azure per MySQL server flessibile usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

È possibile elencare, visualizzare e aggiornare i parametri per un server flessibile di database di Azure per MySQL usando l'interfaccia della riga di comando di Azure. Quando si crea il server, i parametri del server sono configurati con il valore predefinito e consigliato.  

Questo articolo descrive come elencare, visualizzare e aggiornare i parametri del server usando l'interfaccia della riga di comando di Azure.

>[!Note]
> I parametri del server possono essere aggiornati globalmente a livello di server, usare l'interfaccia della riga di comando di [Azure](./how-to-configure-server-parameters-cli.md) o [portale di Azure](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Un server flessibile per database di Azure per MySQL](quickstart-create-server-cli.md)
- L'utilità della riga di comando dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). In alternativa, è possibile usare Azure Cloud Shell nel browser.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Elenca i parametri del server per il server flessibile database di Azure per MySQL
Per elencare tutti i parametri in un server e i relativi valori, eseguire il comando [AZ MySQL flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter) .

È possibile elencare i parametri del server per il server **mydemoserver.MySQL.database.Azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Per la definizione di ognuno dei parametri elencati, vedere la sezione di riferimento di MySQL nell'articolo sulle [variabili di sistema del server](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-parameter-details"></a>Mostra dettagli parametro Server
Per visualizzare i dettagli di un determinato parametro per un server, eseguire il comando [AZ MySQL flexible-server Parameter Show](/cli/azure/mysql/flexible-server/parameter) .

Questo esempio mostra i dettagli del parametro del server di **\_ \_ log di query lente** per il server **mydemoserver.MySQL.database.Azure.com** nel gruppo di risorse **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Modificare un valore del parametro del server
È anche possibile modificare il valore di un determinato parametro del server, che aggiorna il valore di configurazione sottostante per il motore del server MySQL. Per aggiornare il parametro Server, usare il comando [AZ MySQL flexible-server Parameter Set](/cli/azure/mysql/flexible-server/parameter) . 

Per aggiornare il parametro server del **\_ \_ log di query lente** del server **mydemoserver.MySQL.database.Azure.com** nel gruppo di risorse **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Se si desidera reimpostare il valore di un parametro, omettere il `--value` parametro facoltativo e il servizio applica il valore predefinito. Nell'esempio precedente, sarà simile a quanto segue:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Questo codice Reimposta il **log delle \_ query \_ lente** sul valore predefinito **off**. 

## <a name="setting-non-modifiable-server-parameters"></a>Impostazione dei parametri del server non modificabili

Se il parametro Server che si desidera aggiornare non è modificabile, è possibile impostare facoltativamente il parametro a livello di connessione utilizzando `init_connect` . In questo modo vengono impostati i parametri del server per ogni client che si connette al server. 

Aggiornare il parametro **init \_ Connect** server del server **mydemoserver.MySQL.database.Azure.com** nel gruppo di risorse **myresourcegroup** per impostare i valori, ad esempio il set di caratteri.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect` può essere usato per modificare i parametri che non richiedono privilegi avanzati a livello di sessione. Per verificare se è possibile impostare il parametro con `init_connect`, eseguire il comando `set session parameter_name=YOUR_DESIRED_VALUE;` e se si verifica l'errore **Access denied; you need SUPER privileges(s)** (Accesso negato, sono necessari privilegi avanzati), allora non è possibile impostare il parametro con "init_connect".

## <a name="working-with-the-time-zone-parameter"></a>Uso del parametro di fuso orario

### <a name="populating-the-time-zone-tables"></a>Popolare le tabelle di fuso orario

Per popolare le tabelle di fuso orario nel server, è possibile chiamare la stored procedure `mysql.az_load_timezone` da uno strumento come la riga di comando MySQL o MySQL Workbench.

> [!NOTE]
> Se si esegue il comando `mysql.az_load_timezone` da MySQL Workbench, può essere necessario disattivare la modalità di aggiornamento sicuro tramite `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> È necessario riavviare il server per assicurarsi che le tabelle del fuso orario siano popolate correttamente.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Per visualizzare i valori di fuso orario disponibili, eseguire questo comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Impostazione del fuso orario a livello globale

Il fuso orario a livello globale può essere impostato con il comando [AZ MySQL flexible-server Parameter Set](/cli/azure/mysql/flexible-server/parameter) .

Il comando seguente aggiorna il parametro server del **\_ fuso orario** del server **mydemoserver.MySQL.database.Azure.com** nel gruppo di risorse **myresourcegroup** a **US/Pacific**.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Impostazione del fuso orario a livello di sessione

Per impostare il fuso orario a livello di sessione, eseguire il comando `SET time_zone` da uno strumento come la riga di comando MySQL o MySQL Workbench. L'esempio seguente imposta il fuso orario su **US/Pacific**.  

```sql
SET time_zone = 'US/Pacific';
```

Per le [funzioni di data e ora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz), vedere la documentazione di MySQL.


## <a name="next-steps"></a>Passaggi successivi

- Come configurare [i parametri del server nel portale di Azure](./how-to-configure-server-parameters-portal.md)
