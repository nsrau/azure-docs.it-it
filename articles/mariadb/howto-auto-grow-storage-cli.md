---
title: Espandere automaticamente il database di Azure per l'archiviazione MariaDB usando l'interfaccia della riga di comando
description: Questo articolo descrive come abilitare l'aumento automatico delle dimensioni di archiviazione usando l'interfaccia della riga di comando di Azure nel database di Azure per MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: 12ce525514535254637f8cd8f46ec40cc0bd74d0
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389862"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Espandere automaticamente il database di Azure per l'archiviazione MariaDB usando l'interfaccia della riga di comando di Azure
Questo articolo descrive come configurare un database di Azure per l'archiviazione del server MariaDB in modo che cresca senza compromettere il carico di lavoro.

Il server che [raggiunge il limite di archiviazione](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)è impostato su sola lettura. Se l'opzione di aumento automatico delle dimensioni di archiviazione è abilitata per i server con archiviazione con provisioning inferiore a 100 GB, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o 10% dell'archiviazione con provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione di archiviazione con provisioning viene aumentata del 5% quando lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione con provisioning. Si applicano i limiti di archiviazione massimi come specificato [qui](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) .

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Questa guida dettagliata richiede l'uso dell'interfaccia della riga di comando di Azure 2.0 o versioni successive. Per verificare la versione, al prompt dei comandi dell'interfaccia della riga di comando di Azure immettere `az --version`. Per installare o eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="enable-mariadb-server-storage-auto-grow"></a>Abilita aumento automatico archiviazione server MariaDB

Abilitare l'espansione automatica del server in un server esistente con il comando seguente:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Abilitare l'aumento di dimensioni dell'archiviazione automatica del server durante la creazione di un nuovo server con il comando seguente:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche](howto-alert-metric.md).