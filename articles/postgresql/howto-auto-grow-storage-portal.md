---
title: Archiviazione a crescita automatica - Portale di Azure - Database di Azure per PostgreSQL - Server singoloAuto grow storage - Azure portal - Azure Database for PostgreSQL - Single Server
description: Questo articolo descrive come configurare l'archiviazione aumento automatico usando il portale di Azure nel database di Azure per PostgreSQL - singolo server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769235"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Archiviazione a crescita automatica tramite il portale di Azure nel database di Azure per PostgreSQL - Server singoloAuto grow storage using the Azure portal in Azure Database for PostgreSQL - Single Server
Questo articolo descrive come configurare un database di Azure per l'archiviazione del server PostgreSQL per crescere senza influire sul carico di lavoro.

Quando un server raggiunge il limite di archiviazione allocato, il server viene contrassegnato come di sola lettura. Tuttavia, se si abilita l'aumento automatico dello spazio di archiviazione, lo spazio di archiviazione del server aumenta per supportare l'aumento dei dati. Per i server con meno di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, la dimensione dello spazio di archiviazione di cui è stato eseguito il provisioning viene aumentata di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dello spazio di archiviazione di cui è stato eseguito il provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, le dimensioni dello spazio di archiviazione di cui è stato eseguito il provisioning vengono aumentate del 5% quando lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione di cui è stato eseguito il provisioning. Si applicano i limiti di archiviazione massimi specificati [qui.](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- [Database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Abilitare l'aumento automatico automatico dello spazio di archiviazioneEnable storage 

Seguire questi passaggi per impostare PostgreSQL server storage auto grow:

1. Nel [portale](https://portal.azure.com/)di Azure selezionare il database di Azure esistente per il server PostgreSQL.In the Azure portal , select your existing Azure Database for PostgreSQL server.

2. Nella pagina Server PostgreSQL, in **Impostazioni**, fare clic su **Piano prezzi** per aprire la pagina del piano tariffario.

3. Nella sezione **Crescita automatica** selezionare **Sì** per abilitare l'aumento automatico dell'archiviazione.

    ![Azure Database for PostgreSQL - Settings_Pricing_tier - Auto-growth](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Fare clic su **OK** per salvare le modifiche.

5. Una notifica confermerà che l'aumento automatico è stato abilitato correttamente.

    ![Database di Azure per PostgreSQL - successo di crescita automaticaAzure Database for PostgreSQL - auto-growth success](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche.](howto-alert-on-metric.md)
