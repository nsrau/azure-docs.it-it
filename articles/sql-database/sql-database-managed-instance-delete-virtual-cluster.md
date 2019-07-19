---
title: Eliminare una subnet dopo l'eliminazione di un'istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni su come eliminare una rete virtuale di Azure dopo l'eliminazione di un'istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: ead7ea91e172f608c5364e4d5164d2a71dbf2f5f
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297634"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Eliminare una subnet dopo l'eliminazione di un'istanza gestita di database SQL di Azure

Questo articolo fornisce linee guida per l'eliminazione manuale di una subnet dopo l'eliminazione dell'ultima istanza gestita di database SQL di Azure che si trova al suo interno.

Il database SQL usa un [cluster virtuale](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) per contenere l'istanza gestita eliminata. Il cluster virtuale viene mantenuto per 12 ore dopo l'eliminazione dell'istanza, per consentire di creare rapidamente istanze gestite nella stessa subnet. Non è previsto alcun addebito per mantenere un cluster virtuale vuoto. Durante questo periodo, non è possibile eliminare la subnet associata al cluster virtuale.

Se non si vuole attendere 12 ore e si preferisce eliminare immediatamente il cluster virtuale e la relativa subnet, è possibile farlo manualmente. Eliminare manualmente il cluster virtuale usando il portale di Azure o l'API cluster virtuali.

> [!NOTE]
> Per il corretto completamento dell'eliminazione, il cluster virtuale non deve contenere istanze gestite.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Elimina cluster virtuale dal portale di Azure

Per eliminare un cluster virtuale usando il portale di Azure, cercare le risorse del cluster virtuale.

![Screenshot del portale di Azure, con la casella di ricerca evidenziata](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Dopo aver individuato il cluster virtuale che si desidera eliminare, selezionare questa risorsa e selezionare **Elimina**. Viene richiesto di confermare l'eliminazione del cluster virtuale.

![Screenshot del dashboard portale di Azure cluster virtuali con l'opzione di eliminazione evidenziata](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

L'area notifiche portale di Azure indica che il cluster virtuale è stato eliminato. L'eliminazione corretta del cluster virtuale rilascia immediatamente la subnet per il riutilizzo.

> [!TIP]
> Se non sono presenti istanze gestite visualizzate nel cluster virtuale e non è possibile eliminare il cluster virtuale, assicurarsi che non sia in corso una distribuzione dell'istanza in corso. Sono incluse le distribuzioni avviate e annullate ancora in corso. Esaminando la scheda distribuzioni del gruppo di risorse in cui l'istanza è stata distribuita indicherà le distribuzioni in corso. In questo caso, attendere il completamento della distribuzione, eliminare l'istanza gestita e quindi il cluster virtuale.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Eliminare il cluster virtuale usando l'API

Per eliminare un cluster virtuale tramite l'API, usare i parametri URI specificati nel [metodo Delete dei cluster virtuali](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Altre informazioni sull'[architettura della connettività per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Informazioni su come [modificare una rete virtuale esistente per Istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md).
- Per un'esercitazione che mostra come creare una rete virtuale e un'istanza gestita e ripristinare un database da un backup, vedere [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per questioni relative al DNS, vedere [Configurazione di un DNS personalizzato](sql-database-managed-instance-custom-dns.md).
