---
title: Archiviazione a crescita automatica - Interfaccia della riga di comando di Azure - Database di Azure per MariaDBAuto grow storage - Azure CLI - Azure Database for MariaDB
description: Questo articolo descrive come abilitare l'archiviazione di accresciuta automatica usando l'interfaccia della riga di comando di Azure nel database di Azure per MariaDB.This article describes how you can enable auto grow storage using the Azure CLI in Azure Database for MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4be84c750f6a3ca7a0d48aa2b98d75272c1cbadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529085"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Aumento automatico dell'archiviazione del database di Azure per MariaDB tramite l'interfaccia della riga di comando di AzureAuto-grow Azure Database for MariaDB storage using the Azure CLI
Questo articolo descrive come configurare un database di Azure per l'archiviazione del server MariaDB per crescere senza influire sul carico di lavoro.

Il server [che raggiunge il limite di archiviazione](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)è impostato su sola lettura. Se l'aumento automatico dello spazio di archiviazione è abilitato per i server con meno di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, la dimensione dello spazio di archiviazione di cui è stato eseguito il provisioning viene aumentata di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dello spazio di archiviazione di cui è stato eseguito il provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, le dimensioni dello spazio di archiviazione di cui è stato eseguito il provisioning vengono aumentate del 5% quando lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione di cui è stato eseguito il provisioning. Si applicano i limiti di archiviazione massimi specificati [qui.](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage)

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MariaDBAn Azure Database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Questa guida dettagliata richiede l'uso dell'interfaccia della riga di comando di Azure 2.0 o versioni successive. Per verificare la versione, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere `az --version`. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="enable-mariadb-server-storage-auto-grow"></a>Aumentare l'aumento automatico dello spazio di archiviazione del server MariaDBEnable MariaDB server storage auto-grow

Abilitare l'archiviazione di aumento automatico delle dimensioni del server in un server esistente con il comando seguente:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Abilitare l'archiviazione a crescita automatica del server durante la creazione di un nuovo server con il comando seguente:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche.](howto-alert-metric.md)