---
title: Gestire i log-interfaccia della riga di comando di Azure-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come configurare e accedere ai log del server (file con estensione log) nel database di Azure per PostgreSQL-server singolo usando l'interfaccia della riga di comando di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763573"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurare e accedere ai log del server usando l'interfaccia della riga di comando di Azure
È possibile scaricare i log degli errori del server PostgreSQL usando l'interfaccia della riga di comando di Azure. L'accesso ai log delle transazioni non è tuttavia supportato. 

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida è necessario:
- [Un server di Database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md)
- L'utilità della riga di comando[Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o Azure Cloud Shell nel browser

## <a name="configure-logging"></a>Configurare la registrazione
È possibile configurare il server per l'accesso ai log di query e ai log degli errori. I log degli errori possono contenere informazioni su checkpoint, connessioni e vuoto automatico.
1. Abilitare la registrazione.
2. Per abilitare la registrazione delle query, aggiornare **log\_statement** e **log\_min\_duration\_statement**.
3. Abilitare il periodo di conservazione.

Per altre informazioni, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Elenca log
Per elencare i file di log disponibili per il server, eseguire il comando [az postgres server-logs list](/cli/azure/postgres/server-logs).

È possibile elencare i file di log per il server **mydemoserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup**. Quindi indirizzare l'elenco dei file di log a un file di testo denominato **log\_files\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Scaricare i log dal server in locale
Tramite il comando [az postgres server-logs download](/cli/azure/postgres/server-logs) è possibile scaricare i singoli file di log per il server. 

Usare l'esempio seguente per scaricare il file di log specifico per il server **mydemoserver.postgres.database.azure.com** nel gruppo di risorse **myresourcegroup** nell'ambiente locale.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sui log del server, vedere [Registri server nel database di Azure per PostgreSQL](concepts-server-logs.md).
- Per altre informazioni sui parametri del server, vedere [Personalizzare i parametri di configurazione server usando l'interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).
