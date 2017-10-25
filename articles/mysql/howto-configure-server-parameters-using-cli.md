---
title: Configurare i parametri del servizio in Database di Azure per MySQL | Microsoft Docs
description: "Questo articolo descrive come configurare i parametri del servizio in Database di Azure per MySQL usando l'utilità dell'interfaccia della riga di comando di Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 3b811376e4c5445ee74124553c6bce247e4f8faf
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure
È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un'istanza di Database di Azure per il server MySQL usando l'utility dell'interfaccia della riga di comando di Azure. Un subset delle configurazioni del motore viene esposto a livello di server e può essere modificato. 

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Un'istanza di Database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- L'utilità della riga di comando dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). In alternativa, è possibile usare Azure Cloud Shell nel browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Elencare i parametri di configurazione del server per Database di Azure per il server MySQL
Per elencare tutti i parametri modificabili in un server e i relativi valori, eseguire il comando [az mysql server configuration list](/cli/azure/mysql/server/configuration#list).

È possibile elencare i parametri di configurazione per il server **myserver4demo.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```
Per la definizione di ognuno dei parametri elencati, vedere la sezione di riferimento di MySQL nell'articolo sulle [variabili di sistema del server](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visualizzare i dettagli dei parametri di configurazione server
Per visualizzare i dettagli di un determinato parametro di configurazione per un server, eseguire il comando [az mysql server configuration show](/cli/azure/mysql/server/configuration#show).

Questo esempio mostra i dettagli del parametro di configurazione del server **slow\_query\_log** per il server **myserver4demo.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server myserver4demo
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificare un valore di un parametro di configurazione server
È anche possibile modificare il valore di un determinato parametro di configurazione del server, che aggiorna il valore di configurazione sottostante del motore del server MySQL. Per aggiornare la configurazione, usare il comando [az mysql server configuration set](/cli/azure/mysql/server/configuration#set). 

Per aggiornare il parametro di configurazione del server **slow\_query\_log** per il server **myserver4demo.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
```
Per reimpostare il valore di un parametro di configurazione, omettere il parametro facoltativo `--value`. In questo caso, il servizio applica il valore predefinito. Nell'esempio precedente, sarà simile a quanto segue:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo
```
In questo modo il parametro di configurazione **slow\_query\_log** viene reimpostato sul valore predefinito **OFF**. 

## <a name="next-steps"></a>Passaggi successivi
- Come configurare [i parametri del server nel portale di Azure](howto-server-parameters.md)
