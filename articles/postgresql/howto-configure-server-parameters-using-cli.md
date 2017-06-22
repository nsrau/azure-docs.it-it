---
title: Configurare i parametri del servizio in Database di Azure per PostgreSQL | Microsoft Docs
description: Descrive come configurare i parametri del servizio in Database di Azure per PostgreSQL.
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
ms.openlocfilehash: 7e46e3d588782ec0f2ef89c58001e997b8fc2910
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure
È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un server PostgreSQL di Azure usando l'interfaccia della riga di comando di Azure, ma solo un subset delle configurazioni del motore viene esposto a livello di server e può essere modificato. 

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un server e un database [Creare un database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Utilità della riga di comando dell'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) installata

## <a name="list-server-configuration-parameters-for-azure-postgresql-server"></a>Elencare i parametri di configurazione server per il server PostgreSQL di Azure
Per ottenere un elenco di tutti i parametri modificabili in un server PostgreSQL di Azure e dei relativi valori, usare il comando **az postgres server configuration** seguente come mostrato qui:
> az postgres server configuration list --resource-group <resource group name> --server <server name>

È ad esempio possibile elencare i parametri di configurazione server per il server PostgreSQL di Azure **mypgserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup**.
```azurecli
az postgres server configuration list --resource-group myresourcegroup --server mypgserver
```
## <a name="show-server-configuration-parameter-details"></a>Visualizzare i dettagli dei parametri di configurazione server
Per visualizzare i dettagli di un determinato parametro di configurazione per un server PostgreSQL di Azure, eseguire il comando **az postgres server configuration**.
```azurecli
az postgres server configuration show --name <configuration name>
   --resource-group <resource group name> --server <server name>
```
È ad esempio possibile visualizzare i dettagli del parametro di configurazione server **log\_min\_messages** per il server PostgreSQL di Azure **mypgserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages --resource-group myresourcegroup --server mypgserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificare il valore di un parametro di configurazione server
È possibile modificare il valore di determinati parametri di configurazione di un server PostgreSQL di Azure, aggiornando così il valore di configurazione sottostante del motore del server PostgreSQL. Per aggiornare il valore di configurazione, eseguire questo comando **az postgres server configuration**. 
```azurecli
az postgres server configuration update --name <configuration name>
   --resource-group <resource group name> --server <server name>[--value]
```
È ad esempio possibile aggiornare i dettagli del parametro di configurazione server **log\_min\_messages** del server PostgreSQL di Azure **mypgserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages 
   --resource-group myresourcegroup --server mypgserver --value INFO
```
Per reimpostare il valore di un parametro di configurazione, è sufficiente non inserire il parametro facoltativo --value in modo che il servizio applichi il valore predefinito. Nell'esempio precedente sarà simile a quanto segue:
```azurecli
az postgres server configuration show --name log\_min\_messages
   --resource-group myresourcegroup --server mypgserver
```
La configurazione di log\_min\_messages verrà reimpostata sul valore predefinito WARNING. Per altre informazioni sulla configurazione server e sui valori consentiti, vedere la documentazione di PostgreSQL in [Server Configuration](https://www.postgresql.org/docs/9.6/static/runtime-config.html) (Configurazione server).

## <a name="next-steps"></a>Passaggi successivi
- Per configurare e accedere ai log del server, vedere [Server Logs in Azure Database for PostgreSQL](concepts-server-logs.md) (Log del server in Database di Azure per PostgreSQL)
