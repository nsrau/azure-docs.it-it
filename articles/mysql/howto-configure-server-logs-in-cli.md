---
title: Accedere ai log del server in Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: "Questo articolo descrive come accedere ai log del server in Database di Azure per MySQL usando l'utilità dell'interfaccia della riga di comando di Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 6ee2c2c6e6cff824d3167ea600a1ddc778ad011b
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure
È possibile scaricare i log del server di Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure, l'utilità della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). In alternativa, è possibile usare Azure Cloud Shell nel browser.

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configurare la registrazione per Database di Azure per MySQL
È possibile configurare il server per l'accesso ai log delle query lente di MySQL.
1. Attivare la registrazione impostando il parametro **slow\_query\_log** su ON.
2. Regolare gli altri parametri, ad esempio **long\_query\_time** e **log\_slow\_admin\_statements**.

Per informazioni su come impostare il valore di questi parametri tramite l'interfaccia della riga di comando di Azure, vedere [Come configurare i parametri del server](howto-configure-server-parameters-using-cli.md).

Il comando dell'interfaccia della riga di comando seguente, ad esempio, attiva il log delle query lente, imposta la durata di una query prolungata su 10 secondi e disattiva la registrazione dell'istruzione per un amministratore lento. Infine elenca le opzioni di configurazione da verificare.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Elencare i log per il database di Azure per il server MySQL
Per elencare i file di log disponibili per il server, eseguire il comando [az mysql server-logs list](/cli/azure/mysql/server-logs#list).

È possibile elencare i file di log per il server **myserver4demo.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup** e indirizzarlo a un file di testo denominato **log\_files\_list.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Scaricare i log dal server
Il comando [az mysql server-logs download](/cli/azure/mysql/server-logs#download) consente di scaricare i singoli file di log per il server. 

Questo esempio scarica il file di log specifico per il server **myserver4demo.mysql.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [log del server in Database di Azure per MySQL](concepts-server-logs.md)
