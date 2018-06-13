---
title: Ripristinare un'istanza di Azure SQL Data Warehouse - API REST | Microsoft Docs
description: Ripristinare un'istanza di Azure SQL Data Warehouse mediante le API REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2e1874fdf7c11d98d369072739c5937caffe6e96
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524425"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Ripristinare un'istanza di Azure SQL Data Warehouse con le API REST
Ripristinare un'istanza di Azure SQL Data Warehouse mediante le API REST.

## <a name="before-you-begin"></a>Prima di iniziare
**Verificare la capacità in DTU.** Ogni SQL Data Warehouse è ospitato in un server SQL logico (ad esempio mioserver.database.windows.net), che ha una [quota DTU](../sql-database/sql-database-what-is-a-dtu.md) predefinita.  Per poter ripristinare un SQL Data Warehouse, verificare che la quota DTU rimanente nell'istanza del server SQL sia sufficiente per il database da ripristinare. Per richiedere altre quote DTU, è possibile [creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Ripristinare un data warehouse attivo o sospeso
Per ripristinare un data warehouse:

1. Ottenere l'elenco dei punti di ripristino del database utilizzando l'operazione Get Database Restore Points.
2. Iniziare il ripristino utilizzando l'operazione [Create database restore request](https://msdn.microsoft.com/library/azure/dn509571.aspx)
3. Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status](https://msdn.microsoft.com/library/azure/dn720371.aspx) .

> [!NOTE]
> Al termine del ripristino sarà possibile configurare il data warehouse ripristinato seguendo le istruzioni disponibili in [Configurare il database dopo il ripristino](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Ripristinare un data warehouse eliminato
Per ripristinare un data warehouse eliminato:

1. Elencare tutti i data warehouse eliminati ripristinabili tramite l'operazione [List Restorable Dropped Databases](https://msdn.microsoft.com/library/azure/dn509562.aspx).
2. Ottenere i dettagli del data warehouse eliminato che si desidera ripristinare tramite l'operazione [Get restorable dropped database][Get restorable dropped database].
3. Iniziare il ripristino utilizzando l'operazione [Create database restore request](https://msdn.microsoft.com/library/azure/dn509571.aspx)
4. Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status](https://msdn.microsoft.com/library/azure/dn720371.aspx) .

> [!NOTE]
> Per configurare il data warehouse al termine del ripristino, vedere [Configurare il database dopo il ripristino](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, leggere [Panoramica sulla continuità aziendale del database SQL](../sql-database/sql-database-business-continuity.md).
