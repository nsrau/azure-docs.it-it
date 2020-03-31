---
title: Archiviazione a crescita automatica - Portale di Azure - Database di Azure per MariaDBAuto grow storage - Azure portal - Azure Database for MariaDB
description: Questo articolo descrive come abilitare l'archiviazione di aumento automatico per il database di Azure per MariaDB usando il portale di AzureThis article describes how you can enable auto grow storage for Azure Database for MariaDB using Azure portal using Azure portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7ec4c7a61725fa02f00bf38048182d640666ae8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528898"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Archiviazione insufficiente nel database di Azure per MariaDB tramite il portale di AzureAuto grow storage in Azure Database for MariaDB using the Azure portal
Questo articolo descrive come configurare un database di Azure per l'archiviazione del server MariaDB per crescere senza influire sul carico di lavoro.

Quando un server raggiunge il limite di archiviazione allocato, il server viene contrassegnato come di sola lettura. Tuttavia, se si abilita l'aumento automatico dello spazio di archiviazione, lo spazio di archiviazione del server aumenta per supportare l'aumento dei dati. Per i server con meno di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, la dimensione dello spazio di archiviazione di cui è stato eseguito il provisioning viene aumentata di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dello spazio di archiviazione di cui è stato eseguito il provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, le dimensioni dello spazio di archiviazione di cui è stato eseguito il provisioning vengono aumentate del 5% quando lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione di cui è stato eseguito il provisioning. Si applicano i limiti di archiviazione massimi specificati [qui.](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage)

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MariaDBAn Azure Database for MariaDB server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Abilitare l'aumento automatico automatico dello spazio di archiviazioneEnable storage 

Seguire questi passaggi per impostare l'aumento automatico delle dimensioni dello spazio di archiviazione del server MariaDB:Follow these steps to set MariaDB server storage auto grow:

1. Nel [portale](https://portal.azure.com/)di Azure selezionare il database di Azure esistente per il server MariaDB.In the Azure portal , select your existing Azure Database for MariaDB server.

2. Nella pagina Server MariaDB, sotto l'intestazione **Impostazioni,** fare clic su **Piano prezzi** per aprire la pagina del piano tariffario.

3. Nella sezione Crescita automatica selezionare **Sì** per abilitare l'aumento automatico dell'archiviazione.

    ![Azure Database for MariaDB - Settings_Pricing_tier - Auto-growth](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Fare clic su **OK** per salvare le modifiche.

5. Una notifica confermerà che l'aumento automatico è stato abilitato correttamente.

    ![Database di Azure per MariaDB - successo di crescita automaticaAzure Database for MariaDB - auto-growth success](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche.](howto-alert-metric.md)
