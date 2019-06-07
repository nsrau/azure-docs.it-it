---
title: Aumento automatico delle dimensioni archiviazione nel Database di Azure per MySQL usando il portale di Azure
description: Questo articolo descrive come abilitare l'auto aumento delle dimensioni di archiviazione per Database di Azure per MySQL usando il portale di Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5343475f38dd5389d6b0e266ff7167925cd38d71
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676789"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Aumento automatico delle dimensioni archiviazione nel Database di Azure per MySQL usando il portale di Azure
Questo articolo descrive come configurare un Database di Azure per l'archiviazione di server MySQL a crescere senza conseguenze per il carico di lavoro.

Quando un server raggiunge il limite di memoria allocata, il server viene contrassegnato come sola lettura. Tuttavia, se si abilita aumento automatico di archiviazione, aumenta l'archiviazione del server per contenere i dati in continua crescita. Per i server con meno di 100 GB effettuato il provisioning di archiviazione, le dimensioni di archiviazione sottoposte a provisioning vengano aumentata da 5 GB, non appena la memoria disponibile è di sotto di maggiore di 1 GB o 10% dello spazio di archiviazione con provisioning. Per i server con più di 100 GB di spazio di archiviazione, le dimensioni di archiviazione con provisioning aumenta del 5% quando lo spazio di archiviazione disponibile è inferiore al 5% le dimensioni di archiviazione con provisioning. Spazio di archiviazione massimo limita come specificato [qui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) applicare.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [Database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Abilitare automaticamente archiviazione aumento delle dimensioni 

Seguire questi passaggi per impostare l'aumento automatico di archiviazione di server MySQL:

1. Nel [portale di Azure](https://portal.azure.com/), selezionare il Database di Azure per il server MySQL.

2. Nella pagina del server MySQL, sotto **le impostazioni** , fare clic su **tariffario** per aprire la pagina del piano tariffario.

3. Nella sezione aumento automatico, selezionare **Sì** per abilitare l'aumento automatico di archiviazione.

    ![Database di Azure per MySQL - Settings_Pricing_tier - aumento automatico](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Fare clic su **OK** per salvare le modifiche.

5. Una notifica viene visualizzata la conferma l'aumento automatico che è stata abilitata.

    ![Database di Azure per MySQL - operazione riuscita di aumento automatico](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Passaggi successivi

Scopri [come creare avvisi sulle metriche](howto-alert-on-metric.md).
