---
title: Servizio app di Azure - sincronizzazione della configurazione di rete | Microsoft Docs
description: Questo articolo illustra come sincronizzare la configurazione di rete per il piano di hosting del servizio app di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: 0d7920080fd61389741fbe785f5141003bef5251
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797284"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Sincronizzare la configurazione di rete per il piano di hosting del servizio app di Azure

Può succedere che, anche se si [integra l'app con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md), non sia possibile stabilire una connessione all'istanza gestita. È possibile provare ad aggiornare la configurazione di rete per il piano di servizio.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Sincronizzare la configurazione di rete per il piano di hosting del servizio app

A tale scopo, seguire questi passaggi:  

1. Passare al piano del servizio app per le app Web.

   ![piano servizio app](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Fare clic su **Rete** e quindi fare clic su **Fare clic qui per la gestione**.

   ![gestire il piano del servizio](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Selezionare la **Rete virtuale** e fare clic su **Sincronizza rete**.

   ![sincronizza rete](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Attendere fino a quando la sincronizzazione non viene completata.
  
   ![sincronizzazione completata](./media/sql-database-managed-instance-sync-networking/sync-done.png)

A questo punto si è pronti per provare a ristabilire la connessione all'istanza gestita.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla configurazione della rete virtuale per l'istanza gestita, vedere [Architettura della rete virtuale di Istanza gestita](sql-database-managed-instance-connectivity-architecture.md) e [Come configurare la rete virtuale esistente](sql-database-managed-instance-configure-vnet-subnet.md).
