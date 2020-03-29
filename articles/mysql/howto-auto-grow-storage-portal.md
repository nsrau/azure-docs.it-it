---
title: Archiviazione a crescita automatica - Portale di Azure - Database di Azure per MySQLAuto grow storage - Azure portal - Azure Database for MySQL
description: Questo articolo descrive come abilitare l'archiviazione di crescita automatica per il database di Azure per MySQL usando il portale di AzureThis article describes how you can enable auto grow storage for Azure Database for MySQL using Azure portal using Azure portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ddbcb0143a4f84d9e0f3dc2fedf780fca6637692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062508"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Archiviazione in sufficiente crescita automatica nel database di Azure per MySQL usando il portale di AzureAuto grow storage in Azure Database for MySQL using the Azure portal
Questo articolo descrive come configurare un database di Azure per l'archiviazione del server MySQL in modo che cresca senza influire sul carico di lavoro.

Quando un server raggiunge il limite di archiviazione allocato, il server viene contrassegnato come di sola lettura. Tuttavia, se si abilita l'aumento automatico dello spazio di archiviazione, lo spazio di archiviazione del server aumenta per supportare l'aumento dei dati. Per i server con meno di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, la dimensione dello spazio di archiviazione di cui è stato eseguito il provisioning viene aumentata di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dello spazio di archiviazione di cui è stato eseguito il provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato eseguito il provisioning, le dimensioni dello spazio di archiviazione di cui è stato eseguito il provisioning vengono aumentate del 5% quando lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione di cui è stato eseguito il provisioning. Si applicano i limiti di archiviazione massimi specificati [qui.](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage)

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server MySQLAn Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Abilitare l'aumento automatico automatico dello spazio di archiviazioneEnable storage 

Seguire questi passaggi per impostare l'aumento automatico delle dimensioni dell'archiviazione del server MySQL:Follow these steps to set MySQL server storage auto grow:

1. Nel [portale](https://portal.azure.com/)di Azure selezionare il database di Azure esistente per il server MySQL.In the Azure portal , select your existing Azure Database for MySQL server.

2. Nella pagina Server MySQL, sotto l'intestazione **Impostazioni,** fare clic su **Livello prezzi** per aprire la pagina Livello prezzi.

3. Nella sezione Crescita automatica selezionare **Sì** per abilitare l'aumento automatico dell'archiviazione.

    ![Azure Database for MySQL - Settings_Pricing_tier - Auto-growth](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Fare clic su **OK** per salvare le modifiche.

5. Una notifica confermerà che l'aumento automatico è stato abilitato correttamente.

    ![Database di Azure per MySQL - successo di crescita automaticaAzure Database for MySQL - auto-growth success](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche.](howto-alert-on-metric.md)
