---
title: Configurare e accedere ai log del server per PostgreSQL usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come configurare e accedere ai log del server in Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.openlocfilehash: 26f8e12c493904f722cad5191ee053feff20f7fc
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure
È possibile scaricare i log degli errori del server PostgreSQL usando l'interfaccia della riga di comando di Azure. Tuttavia, l'accesso ai log delle transazioni non è supportato. 

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Database di Azure per il server PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Installare l'utilità della riga di comando dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) o usare Azure Cloud Shell nel browser.

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configurare la registrazione per Database di Azure per PostgreSQL
È possibile configurare il server per l'accesso ai log di query e ai log degli errori. I log degli errori possono contenere informazioni su checkpoint, connessioni e vuoto automatico.
1. Abilitare la registrazione
2. Abilitare log\_statement e log\_min\_duration\_statement per abilitare la registrazione delle query
3. Abilitare il periodo di conservazione

Per altre informazioni, vedere [Personalizzazione dei parametri di configurazione](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Elencare i log per il database di Azure per il server PostgreSQL
Per elencare i file di log disponibili per il server, eseguire il comando [az postgres server-logs list](/cli/azure/postgres/server-logs#list).

È possibile elencare i file di log per il server **mypgserver-20170401.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup** e indirizzarlo a un file di testo denominato **log\_files\_list.txt.**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mypgserver-20170401 > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Scaricare i log dal server in locale
Il comando [az postgres server-logs download](/cli/azure/postgres/server-logs#download) consente di scaricare i singoli file di log per il server. 

Questo esempio scarica il file di log specifico per il server **mypgserver-20170401.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli-interactive
az postgres server-logs download --name 20170414-mypgserver-20170401-postgresql.log --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sui log del server, vedere [Server Logs in Azure Database for PostgreSQL](concepts-server-logs.md) (Log del server in Database di Azure per PostgreSQL)
- Per altre informazioni sui parametri del server, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md)
