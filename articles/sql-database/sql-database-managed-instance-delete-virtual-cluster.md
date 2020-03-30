---
title: Eliminare una subnet dopo l'eliminazione di un'istanza gestitaDelete a subnet after deleting a managed instance
description: Informazioni su come eliminare una rete virtuale di Azure dopo l'eliminazione di un'istanza gestita del database SQL di Azure.Learn how to delete an Azure virtual network after deleting an Azure SQL Database managed instance.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820472"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Eliminare una subnet dopo l'eliminazione di un'istanza gestita del database SQL di AzureDelete a subnet after deleting an Azure SQL Database managed instance

Questo articolo fornisce linee guida su come eliminare manualmente una subnet dopo l'eliminazione dell'ultima istanza gestita del database SQL di Azure che vi risieda.

Le istanze [gestite](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)vengono distribuite in cluster virtuali. Ogni cluster virtuale è associato a una subnet. Il cluster virtuale persiste in base alla progettazione per 12 ore dopo l'eliminazione dell'ultima istanza per consentire di creare più rapidamente istanze gestite nella stessa subnet. Non è previsto alcun costo per mantenere un cluster virtuale vuoto. Durante questo periodo, non è possibile eliminare la subnet associata al cluster virtuale.

Se non si desidera attendere 12 ore e si preferisce eliminare prima il cluster virtuale e la relativa subnet, è possibile farlo manualmente. Eliminare manualmente il cluster virtuale usando il portale di Azure o l'API dei cluster virtuali.

> [!IMPORTANT]
> - Il cluster virtuale non deve contenere istanze gestite affinché l'eliminazione abbia esito positivo. 
> - L'eliminazione di un cluster virtuale è un'operazione a esecuzione prolungata della durata di circa 1,5 ore (vedere Operazioni di gestione delle [istanze gestite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) per il tempo di eliminazione del cluster virtuale aggiornato) durante le quali il cluster virtuale sarà ancora visibile nel portale fino al completamento del processo.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Eliminare il cluster virtuale dal portale di AzureDelete virtual cluster from the Azure portal

Per eliminare un cluster virtuale tramite il portale di Azure, cercare le risorse cluster virtuali.

![Screenshot del portale di Azure con la casella di ricerca evidenziata](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Dopo aver individuato il cluster virtuale che si desidera eliminare, selezionare questa risorsa e scegliere **Elimina**. Viene richiesto di confermare l'eliminazione del cluster virtuale.

![Screenshot del dashboard Cluster virtuali del portale di Azure con l'opzione Elimina evidenziata](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Le notifiche del portale di Azure mostreranno una conferma che la richiesta di eliminazione del cluster virtuale è stata inviata correttamente. L'operazione di eliminazione stessa durerà circa 1,5 ore durante le quali il cluster virtuale sarà ancora visibile nel portale. Una volta completato il processo, il cluster virtuale non sarà più visibile e la subnet ad esso associata verrà rilasciata per il riutilizzo.

> [!TIP]
> Se nel cluster virtuale non sono presenti istanze gestite visualizzate e non è possibile eliminare il cluster virtuale, assicurarsi di non disporre di una distribuzione dell'istanza in corso in corso. Sono incluse le distribuzioni avviate e annullate che sono ancora in corso. Ciò è dovuto al fatto che queste operazioni utilizzeranno comunque il cluster virtuale bloccandolo dall'eliminazione. La revisione delle distribuzioni del gruppo di risorse in cui è stata distribuita l'istanza indicherà eventuali distribuzioni in corso. In questo caso, attendere il completamento della distribuzione, eliminare l'istanza gestita e quindi il cluster virtuale.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Eliminare il cluster virtuale tramite l'APIDelete virtual cluster by using the API

Per eliminare un cluster virtuale tramite l'API, utilizzare i parametri URI specificati nel [metodo delete dei cluster virtuali.](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Che cos'è un'istanza gestita?](sql-database-managed-instance.md).
- Altre informazioni sull'[architettura della connettività per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Informazioni su come [modificare una rete virtuale esistente per Istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md).
- Per un'esercitazione che mostra come creare una rete virtuale e un'istanza gestita e ripristinare un database da un backup, vedere [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per problemi relativi al DNS, vedere [Configurazione di un DNS personalizzato.](sql-database-managed-instance-custom-dns.md)
