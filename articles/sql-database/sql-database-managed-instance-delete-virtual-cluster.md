---
title: Eliminare una subnet dopo l'eliminazione di un Database SQL di Azure di istanza gestita | Microsoft Docs
description: Informazioni su come eliminare una rete virtuale di Azure dopo l'eliminazione di un Database SQL di Azure di istanza gestita.
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
ms.date: 06/26/2019
ms.openlocfilehash: 4679ecda210fa78aad4315bc6602b67dd1795ce9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427982"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Eliminare una subnet dopo l'eliminazione di un Database SQL di Azure di istanza gestita

Questo articolo vengono fornite istruzioni su come eliminare manualmente una subnet dopo l'eliminazione di Database SQL di Azure ultima istanza che si trovano in esso gestita.

Il Database SQL Usa una [cluster virtuale](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) per contenere l'istanza gestita eliminato. Il cluster virtuale viene mantenuto per 12 ore dopo l'eliminazione di istanza, che consentono di creare rapidamente le istanze gestite nella stessa subnet. Non sono previsti addebiti per la conservazione di un cluster virtuale vuoto. Durante questo periodo, non è possibile eliminare la subnet associata al cluster virtuale.

Se non si desidera attendere 12 ore e si preferisce eliminare immediatamente il cluster virtuale e una subnet, è possibile farlo manualmente. Eliminare il cluster virtuale manualmente usando il portale di Azure o l'API di cluster virtuali.

> [!NOTE]
> Il cluster virtuale non deve contenere nessuna istanza gestita per l'eliminazione sia riuscita.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Eliminare il cluster virtuale dal portale di Azure

Per eliminare un cluster virtuale usando il portale di Azure, cercare le risorse del cluster virtuale.

![Screenshot del portale di Azure, con la casella di ricerca evidenziato](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Dopo aver individuato il cluster virtuale che si desidera eliminare, selezionare la risorsa e selezionare **Elimina**. Viene chiesto di confermare l'eliminazione del cluster virtuale.

![Screenshot del portale di Azure Virtual cluster dashboard, con l'opzione Elimina evidenziata](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

L'area delle notifiche del portale Azure illustra conferma che è stato eliminato il cluster virtuale. Completamento dell'eliminazione del cluster virtuale rilascia immediatamente le subnet per il riutilizzo.

> [!TIP]
> Se non sono presenti istanze gestite illustrate del cluster virtuale, e non si riesce a eliminare il cluster virtuale, assicurarsi che non è una distribuzione di istanze in corso in corso. Ciò include avviate ed è state annullate distribuzioni ancora in corso. Esaminare le distribuzioni della scheda del gruppo di risorse, l'istanza è stata distribuita in indicherà eventuali distribuzioni in corso. In questo caso, await per la distribuzione per il completamento, eliminare l'istanza gestita e quindi il cluster virtuale.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Eliminare il cluster virtuale usando l'API

Per eliminare un cluster virtuale tramite l'API, usare i parametri URI specificati nel [metodo di eliminazione cluster virtuali](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Informazioni su Istanza gestita](sql-database-managed-instance.md).
- Altre informazioni sull'[architettura della connettività per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Informazioni su come [modificare una rete virtuale esistente per Istanza gestita](sql-database-managed-instance-configure-vnet-subnet.md).
- Per un'esercitazione che mostra come creare una rete virtuale e un'istanza gestita e ripristinare un database da un backup, vedere [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-get-started.md).
- Per questioni relative al DNS, vedere [Configurazione di un DNS personalizzato](sql-database-managed-instance-custom-dns.md).
