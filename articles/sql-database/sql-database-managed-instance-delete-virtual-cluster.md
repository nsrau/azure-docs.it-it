---
title: Eliminare una subnet dopo l'eliminazione di un'istanza gestita
description: Informazioni su come eliminare una rete virtuale di Azure dopo l'eliminazione di un'istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820472"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Eliminare una subnet dopo l'eliminazione di un'istanza gestita di database SQL di Azure

Questo articolo fornisce linee guida per l'eliminazione manuale di una subnet dopo l'eliminazione dell'ultima istanza gestita di database SQL di Azure che si trova al suo interno.

Le istanze gestite vengono distribuite in [cluster virtuali](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture). Ogni cluster virtuale è associato a una subnet. Il cluster virtuale viene mantenuto in base alla progettazione per 12 ore dopo l'eliminazione dell'ultima istanza, per consentire di creare più rapidamente istanze gestite nella stessa subnet. Non è previsto alcun addebito per mantenere un cluster virtuale vuoto. Durante questo periodo, non è possibile eliminare la subnet associata al cluster virtuale.

Se non si vuole attendere 12 ore e si preferisce eliminare il cluster virtuale e la subnet prima, è possibile farlo manualmente. Eliminare manualmente il cluster virtuale usando il portale di Azure o l'API cluster virtuali.

> [!IMPORTANT]
> - Per il corretto completamento dell'eliminazione, il cluster virtuale non deve contenere istanze gestite. 
> - L'eliminazione di un cluster virtuale è un'operazione a esecuzione prolungata che dura circa 1,5 ore (vedere [operazioni di gestione di istanze gestite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) per l'ora di eliminazione del cluster virtuale aggiornata) durante le quali il cluster virtuale sarà ancora visibile nel portale fino a quando non processo completato.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Elimina cluster virtuale dal portale di Azure

Per eliminare un cluster virtuale usando il portale di Azure, cercare le risorse del cluster virtuale.

![Screenshot del portale di Azure, con la casella di ricerca evidenziata](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Dopo aver individuato il cluster virtuale che si desidera eliminare, selezionare questa risorsa e selezionare **Elimina**. Viene richiesto di confermare l'eliminazione del cluster virtuale.

![Screenshot del dashboard portale di Azure cluster virtuali con l'opzione di eliminazione evidenziata](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Portale di Azure notifiche visualizzano una conferma che la richiesta di eliminazione del cluster virtuale è stata inviata correttamente. L'operazione di eliminazione durerà per circa 1,5 ore durante le quali il cluster virtuale sarà ancora visibile nel portale. Al termine del processo, il cluster virtuale non sarà più visibile e la subnet associata verrà rilasciata per il riutilizzo.

> [!TIP]
> Se non sono presenti istanze gestite visualizzate nel cluster virtuale e non è possibile eliminare il cluster virtuale, assicurarsi che non sia in corso una distribuzione dell'istanza in corso. Sono incluse le distribuzioni avviate e annullate ancora in corso. Questo perché queste operazioni utilizzeranno comunque il cluster virtuale che lo blocca dall'eliminazione. Esaminando la scheda distribuzioni del gruppo di risorse in cui l'istanza è stata distribuita indicherà le distribuzioni in corso. In questo caso, attendere il completamento della distribuzione, eliminare l'istanza gestita e quindi il cluster virtuale.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Eliminare il cluster virtuale usando l'API

Per eliminare un cluster virtuale tramite l'API, usare i parametri URI specificati nel [metodo Delete dei cluster virtuali](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Altre informazioni sull'[architettura della connettività per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Informazioni su come [modificare una rete virtuale esistente per Istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md).
- Per un'esercitazione che mostra come creare una rete virtuale e un'istanza gestita e ripristinare un database da un backup, vedere [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per questioni relative al DNS, vedere [Configurazione di un DNS personalizzato](sql-database-managed-instance-custom-dns.md).
