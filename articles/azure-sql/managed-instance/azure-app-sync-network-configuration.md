---
title: Sincronizzare la configurazione di rete per il servizio app Azure
titleSuffix: Azure SQL Managed Instance
description: Questo articolo illustra come sincronizzare la configurazione di rete per il piano di hosting del servizio app Azure con il Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695454"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Sincronizzare la configurazione di rete per app Azure piano di hosting del servizio con Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Potrebbe succedere che anche se [l'app è stata integrata con una rete virtuale di Azure](../../app-service/web-sites-integrate-with-vnet.md), non è possibile stabilire una connessione a SQL istanza gestita. Per risolvere questo problema, è possibile aggiornare o sincronizzare la configurazione di rete per il piano di servizio. 

## <a name="sync-network-configuration"></a>Sincronizzare la configurazione di rete 

A tale scopo, seguire questi passaggi:  

1. Passare al piano del servizio app per le app Web.

   ![Screenshot del piano di servizio app](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Selezionare **rete** e quindi **fare clic qui per gestire**.

   ![Screenshot della gestione del piano di servizio](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Selezionare la **Rete virtuale** e fare clic su **Sincronizza rete**.

   ![Screenshot della rete di sincronizzazione](./media/azure-app-sync-network-configuration/sync.png)

4. Attendere fino a quando la sincronizzazione non viene completata.
  
   ![Screenshot della sincronizzazione eseguita](./media/azure-app-sync-network-configuration/sync-done.png)

A questo punto è possibile provare a ristabilire la connessione al Istanza gestita SQL.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla configurazione di VNet per SQL Istanza gestita, vedere [sql istanza gestita VNet Architecture](connectivity-architecture-overview.md) e [How to configure existing VNet](vnet-existing-add-subnet.md).
