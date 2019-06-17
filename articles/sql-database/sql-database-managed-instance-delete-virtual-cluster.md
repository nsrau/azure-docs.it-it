---
title: Elimina rete virtuale dopo che l'istanza gestita di eliminazione del Database SQL di Azure | Microsoft Docs
description: Informazioni su come eliminare della rete virtuale dopo l'eliminazione di Database SQL di Azure di istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 61f6c25031c4906e65c2f75a7679600741e8311a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791382"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Eliminare la subnet dopo l'eliminazione di Database SQL di Azure di istanza gestita

Questo articolo vengono fornite istruzioni su come eliminare manualmente la subnet dopo l'eliminazione di Database SQL di Azure ultima istanza che si trovano in esso gestita.

Il [cluster virtuale](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) che dispone di contenuto eliminato istanza gestita verrà conservata per 12 ore dall'eliminazione dell'istanza. Il cluster virtuale viene mantenuto attivo per impostazione predefinita per consentire la creazione più rapida di istanze gestite nella stessa subnet. Mantenendo un cluster virtuale vuoto è gratuito. Durante questo periodo, non è possibile eliminare la subnet associata al cluster virtuale.

Rilascio immediato della subnet usata da un cluster virtuale vuoto viene possibile tramite l'eliminazione manuale del cluster virtuale. L'eliminazione del cluster virtuale può essere ottenuta tramite il portale di Azure o API di cluster virtuali.

> [!NOTE]
> Il cluster virtuale non deve contenere nessuna istanza gestita per l'eliminazione sia riuscita.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Eliminare il cluster virtuale dal portale di Azure

Per eliminare un cluster virtuale usando il portale di Azure, cercare le risorse del cluster virtuale con la ricerca incorporata.

![Ricerca per il cluster virtuale.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Dopo aver individuato il cluster virtuale che si desidera eliminare, selezionare la risorsa e selezionare l'opzione di eliminazione. Verrà chiesto di confermare l'eliminazione del cluster virtuale.

![Eliminare il cluster virtuale.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Conferma che è stato eliminato il cluster virtuale viene fornito nelle notifiche del portale di Azure. Completamento dell'eliminazione del cluster virtuale rilascia immediatamente le subnet per poter essere riutilizzato.

## <a name="delete-virtual-cluster-using-api"></a>Eliminare cluster virtuale usando le API

Per eliminare un virtuale del cluster tramite l'uso di API i parametri URI specificati nel [metodo di eliminazione cluster virtuali](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Altre informazioni sull'[architettura della connettività per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Informazioni su come [modificare una rete virtuale esistente per Istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md).
- Per un'esercitazione che mostra come creare una rete virtuale e un'istanza gestita e ripristinare un database da un backup, vedere [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per questioni relative al DNS, vedere [Configurazione di un DNS personalizzato](sql-database-managed-instance-custom-dns.md).
