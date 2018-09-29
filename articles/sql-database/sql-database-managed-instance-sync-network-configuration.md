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
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 03/07/2018
ms.openlocfilehash: d5de908166e8de1d45a36f97aee8934653e59623
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163169"
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
