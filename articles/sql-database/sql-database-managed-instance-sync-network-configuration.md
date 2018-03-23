---
title: Servizio app di Azure - sincronizzazione della configurazione di rete | Microsoft Docs
description: Questo articolo illustra come sincronizzare la configurazione di rete per il piano di hosting del servizio app di Azure.
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 5b139b1279776acfca63def25a9fdae0f627a727
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
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

- Per informazioni sulla configurazione della rete virtuale per l'istanza gestita, vedere la [configurazione della rete virtuale dell'istanza gestita](sql-database-managed-instance-vnet-configuration.md).
