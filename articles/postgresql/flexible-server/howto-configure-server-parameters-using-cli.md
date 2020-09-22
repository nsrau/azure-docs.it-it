---
title: Configurare i parametri-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive come configurare i parametri Postgres nel database di Azure per PostgreSQL-server flessibile usando l'interfaccia della riga di comando di Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b14858cc99cac41e277b03171fd4cac4d6eafa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940734"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Personalizzare i parametri del server per database di Azure per PostgreSQL-server flessibile con l'interfaccia della riga di comando

È possibile elencare, visualizzare e aggiornare i parametri di configurazione per un server PostgreSQL di Azure usando l'interfaccia della riga di comando di Azure. Un subset di parametri del motore viene esposto a livello di server e può essere modificato. 

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida, si richiedono:
- Creare un server e un database di Database di Azure per PostgreSQL seguendo le istruzioni riportate nell'articolo [Creare un database di Azure per PostgreSQL](quickstart-create-server-cli.md)
- Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) nel computer oppure usare [Azure Cloud Shell](../../cloud-shell/overview.md) nel portale di Azure tramite il browser.

## <a name="list-server-parameters-for-a-flexible-server"></a>Elenca i parametri del server per un server flessibile

Per elencare tutti i parametri modificabili in un server e i relativi valori, eseguire il comando [AZ Postgres flexible-server parameter list](/cli/azure/postgres/flexible-server/parameter) .

È possibile elencare i parametri del server per il server **mydemoserver.postgres.database.Azure.com** nel gruppo di risorse **myresourcegroup**.

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>Mostra dettagli parametro Server

Per visualizzare i dettagli di un determinato parametro per un server, eseguire il comando [AZ Postgres flexible server Parameter Show](/cli/azure/postgres/flexible-server/parameter)  .

Questo esempio mostra i dettagli del parametro del server **log \_ Min \_ messages** per il server **mydemoserver.postgres.database.Azure.com** nel gruppo di risorse **myresourcegroup.**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>Modificare il valore del parametro Server

È anche possibile modificare il valore di un determinato parametro del server, che aggiorna il valore di configurazione sottostante per il motore del server PostgreSQL. Per aggiornare il parametro, usare il comando [AZ Postgres flexibt-server Parameter Set](/cli/azure/postgres/flexible-server/parameter) . 

Per aggiornare il parametro del server **log \_ Min \_ messages** del server **mydemoserver.postgres.database.Azure.com** nel gruppo di risorse **myresourcegroup.**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

Se si vuole reimpostare il valore di un parametro, è sufficiente scegliere di omettere il `--value` parametro facoltativo e il servizio applica il valore predefinito. Nell'esempio precedente sarà simile a quanto segue:

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

Questo comando Reimposta il parametro **log \_ Min \_ messages** sul valore predefinito **warning**. Per altre informazioni sui parametri del server e sui valori consentiti, vedere la documentazione di PostgreSQL sull' [impostazione dei parametri](https://www.postgresql.org/docs/12/config-setting.html).

## <a name="next-steps"></a>Passaggi successivi

- Per configurare e accedere ai log del server, vedere [Server Logs in Azure Database for PostgreSQL](concepts-logging.md) (Log del server in Database di Azure per PostgreSQL)
