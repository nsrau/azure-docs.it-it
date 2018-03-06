---
title: Configurare i parametri del servizio in Database di Azure per MySQL
description: "Questo articolo descrive come configurare i parametri del servizio in Database di Azure per MySQL usando l'utilità dell'interfaccia della riga di comando di Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9caf6b164f2433ab3c1b701554f562211cc2de80
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure
È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un'istanza di Database di Azure per il server MySQL usando l'utility dell'interfaccia della riga di comando di Azure. Un subset delle configurazioni del motore viene esposto a livello di server e può essere modificato. 

## <a name="prerequisites"></a>prerequisiti
Per proseguire con questa guida, si richiedono:
- [Un'istanza di Database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- L'utilità della riga di comando dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). In alternativa, è possibile usare Azure Cloud Shell nel browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Elencare i parametri di configurazione del server per Database di Azure per il server MySQL
Per elencare tutti i parametri modificabili in un server e i relativi valori, eseguire il comando [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list).

È possibile elencare i parametri di configurazione per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Per la definizione di ognuno dei parametri elencati, vedere la sezione di riferimento di MySQL nell'articolo sulle [variabili di sistema del server](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visualizzare i dettagli dei parametri di configurazione server
Per visualizzare i dettagli di un determinato parametro di configurazione per un server, eseguire il comando [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show).

Questo esempio illustra i dettagli del parametro di configurazione **slow\_query\_log** per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificare un valore di un parametro di configurazione server
È anche possibile modificare il valore di un determinato parametro di configurazione del server, che aggiorna il valore di configurazione sottostante del motore del server MySQL. Per aggiornare la configurazione, usare il comando [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set). 

Per aggiornare il parametro di configurazione **slow\_query\_log** per il server **mydemoserver.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Per reimpostare il valore di un parametro di configurazione, omettere il parametro facoltativo `--value`. In questo caso, il servizio applica il valore predefinito. Nell'esempio precedente, sarà simile a quanto segue:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
In questo modo il parametro di configurazione **slow\_query\_log** viene reimpostato sul valore predefinito **OFF**. 

## <a name="next-steps"></a>Passaggi successivi
- Come configurare [i parametri del server nel portale di Azure](howto-server-parameters.md)