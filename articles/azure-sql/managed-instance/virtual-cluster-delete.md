---
title: Eliminare una subnet dopo l'eliminazione di un'istanza gestita di SQL Istanza gestita
description: Informazioni su come eliminare una rete virtuale di Azure dopo l'eliminazione di un'istanza gestita di Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 87e73ed25ac33777c19c561223f0361d589282b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84697001"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>Eliminare una subnet dopo l'eliminazione di un'istanza gestita di SQL Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo fornisce linee guida su come eliminare manualmente una subnet dopo aver eliminato l'ultima istanza gestita di Azure SQL Istanza gestita risiedere in tale subnet.

Le istanze gestite vengono distribuite in [cluster virtuali](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Ogni cluster virtuale è associato a una subnet. Il cluster virtuale viene mantenuto in base alla progettazione per 12 ore dopo l'eliminazione dell'ultima istanza, per consentire di creare più rapidamente istanze gestite nella stessa subnet. Non è previsto alcun addebito per mantenere un cluster virtuale vuoto. Durante questo periodo, non è possibile eliminare la subnet associata al cluster virtuale.

Se non si vuole attendere 12 ore e si preferisce eliminare il cluster virtuale e la subnet prima, è possibile farlo manualmente. Eliminare manualmente il cluster virtuale usando il portale di Azure o l'API cluster virtuali.

> [!IMPORTANT]
> - Per il corretto completamento dell'eliminazione, il cluster virtuale non deve contenere istanze gestite. 
> - L'eliminazione di un cluster virtuale è un'operazione a esecuzione prolungata che dura circa 1,5 ore (vedere [operazioni di gestione di istanze gestite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) per l'ora di eliminazione del cluster virtuale aggiornata). Il cluster virtuale sarà ancora visibile nel portale fino al completamento del processo.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Eliminare un cluster virtuale dalla portale di Azure

Per eliminare un cluster virtuale usando il portale di Azure, cercare le risorse del cluster virtuale.

![Screenshot del portale di Azure, con la casella di ricerca evidenziata](./media/virtual-cluster-delete/virtual-clusters-search.png)

Dopo aver individuato il cluster virtuale che si desidera eliminare, selezionare questa risorsa e selezionare **Elimina**. Viene richiesto di confermare l'eliminazione del cluster virtuale.

![Screenshot del dashboard portale di Azure cluster virtuali, con l'opzione Elimina evidenziata](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Portale di Azure notifiche indicheranno che la richiesta di eliminazione del cluster virtuale è stata inviata correttamente. L'operazione di eliminazione durerà per circa 1,5 ore, durante le quali il cluster virtuale sarà ancora visibile nel portale. Al termine del processo, il cluster virtuale non sarà più visibile e la subnet associata verrà rilasciata per il riutilizzo.

> [!TIP]
> Se non sono presenti istanze gestite visualizzate nel cluster virtuale e non è possibile eliminare il cluster virtuale, assicurarsi che non sia in corso una distribuzione dell'istanza in corso. Sono incluse le distribuzioni avviate e annullate ancora in corso. Questo perché queste operazioni utilizzeranno comunque il cluster virtuale, bloccando l'eliminazione. Esaminando la scheda **distribuzioni** del gruppo di risorse in cui l'istanza è stata distribuita indicherà le distribuzioni in corso. In questo caso, attendere il completamento della distribuzione, eliminare l'istanza gestita e quindi eliminare il cluster virtuale.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Eliminare un cluster virtuale usando l'API

Per eliminare un cluster virtuale tramite l'API, usare i parametri URI specificati nel [metodo Delete dei cluster virtuali](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere informazioni su [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md).
- Informazioni sull' [architettura di connettività in SQL istanza gestita](connectivity-architecture-overview.md).
- Informazioni su come [modificare una rete virtuale esistente per SQL istanza gestita](vnet-existing-add-subnet.md).
- Per un'esercitazione che illustra come creare una rete virtuale, creare un'istanza gestita e ripristinare un database da un backup del database, vedere [creare un'istanza gestita](instance-create-quickstart.md).
- Per problemi relativi al DNS, vedere [configurare un DNS personalizzato](custom-dns-configure.md).
