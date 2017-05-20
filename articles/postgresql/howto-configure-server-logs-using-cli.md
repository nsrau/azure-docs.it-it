---
title: Configurare e accedere ai log del server per PostgreSQL usando l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: Descrive come configurare e accedere ai log del server in Database di Azure per PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: dc047e6d5194035586931b3cacc4d5cd07f6cfc2
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure
È possibile elencare e scaricare i log degli errori del server PostgreSQL di Azure usando l'interfaccia della riga di comando di Azure. Tuttavia, l'accesso ai log delle transazioni non è supportato. 

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- [Database di Azure per il server PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Utilità della riga di comando dell'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) installata

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configurare la registrazione per Database di Azure per PostgreSQL
È possibile configurare il server per l'accesso ai log di query e ai log degli errori. I log degli errori possono contenere informazioni su checkpoint, connessioni e vuoto automatico.
1. Abilitare la registrazione
2. Abilitare log\_statement e log\_min\_duration\_statement per abilitare la registrazione delle query
3. Abilitare il periodo di conservazione

Per altre informazioni, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-postgresql-server"></a>Elencare il log per il server PostgreSQL di Azure
Per elencare i file di log disponibili per il server, eseguire il comando **az postgres server-logs** come illustrato nell'esempio seguente:
```azurecli
az postgres server-logs list --resource-group <resource group name> --server <server name> [ --file-last-written --filename-contains --max-file-size ]
```
È ad esempio possibile elencare i file di log per il server PostgreSQL di Azure **mypgserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup** e indirizzarlo a un file di testo denominato **log\_files\_list.txt. **
```azurecli
az postgres server-logs list --resource-group **myresourcegroup** --server **mypgserver** > log\_files\_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Scaricare i log dal server in locale
È anche possibile scaricare singoli file di log per il server PostgreSQL di Azure. 
```azurecli
az postgres server-logs download –name <log file name> --resource-group <resource group name> --server <server name>
```
Questo esempio permette di scaricare il file di log specifico per il server PostgreSQL di Azure **mypgserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli
az postgres server-logs download --name 20170414-mypgserver-postgresql.log --resource-group **myresourcegroup** --server **mypgserver**
```
## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sui log del server, vedere [Server Logs in Azure Database for PostgreSQL](concepts-server-logs.md) (Log del server in Database di Azure per PostgreSQL)
- Per altre informazioni sui parametri del server, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md)
