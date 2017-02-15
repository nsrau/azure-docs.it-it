---
title: Spostare database tra server, tra sottoscrizioni e all&quot;interno e all&quot;esterno di Azure.
description: Azioni rapide per copiare, spostare ed eseguire la migrazione di dati e database nel database SQL di Azure.
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
ms.assetid: dea01868-5757-41e2-826c-ec26361a339c
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bc88bb49c601f525c62c4dac4716a3f77a30172e


---
# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Spostare database tra server, tra sottoscrizioni e all'interno e all'esterno di Azure
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Per spostare un database in un server diverso nella stessa sottoscrizione
* Nel [portale di Azure](https://portal.azure.com) fare clic su **Database SQL**, selezionare un database dall'elenco e quindi fare clic su **Copia**. Per altre informazioni, vedere [Copiare un database SQL di Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Per spostare un database tra sottoscrizioni
* Nel [portale di Azure](https://portal.azure.com)fare clic su **SQL Server** e quindi selezionare dall'elenco il server che ospita il database. Fare clic su **Sposta**, quindi selezionare le risorse da spostare e la sottoscrizione in cui spostarle.

## <a name="to-migrate-a-sql-database-into-azure"></a>Per eseguire la migrazione di un database SQL in Azure
* Determinare la compatibilità del database e quindi selezionare il metodo di migrazione appropriato in base alle esigenze. Seguire le linee guida e le opzioni disponibili in [Migrazione di un database SQL Server al database SQL nel cloud](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Per creare una copia di un database da usare all'esterno di Azure
* [Esportare un file BACPAC.](sql-database-export.md)




<!--HONumber=Nov16_HO3-->


