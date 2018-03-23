---
title: Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure tramite il Servizio Migrazione del database di Azure | Microsoft Docs
description: Informazioni sulle configurazioni di origine e di destinazione per il servizio Migrazione del database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/06/2018
ms.openlocfilehash: 892cff02b5b70f09236bb37ae786f180ddca9316
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure tramite il Servizio Migrazione del database di Azure
In questo articolo verranno introdotte diverse topologie di rete usate dal Servizio Migrazione del database di Azure per garantire un'esperienza avanzata di migrazione di Istanza gestita di database SQL di Azure dai server SQL locali.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Istanza gestita di database SQL di Azure configurata per carichi di lavoro ibridi 
Usare questa topologia se Istanza gestita di database SQL di Azure è connessa alla rete locale. Questo approccio fornisce il routing di rete più semplificato e garantisce la massima velocità effettiva dei dati durante la migrazione.

![Topologia di rete per i carichi di lavoro ibridi](media\resource-network-topologies\hybrid-workloads.png)

**Requisiti**
- In questo scenario Istanza gestita di database SQL di Azure e l'istanza del Servizio Migrazione del database di Azure vengono create nella stessa rete virtuale di Azure, ma usano subnet diverse.  
- La rete virtuale usata in questo scenario è inoltre connessa alla rete locale tramite ExpressRoute o VPN.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Istanza gestita di database SQL di Azure isolata dalla rete locale
Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:
- Istanza gestita di database SQL di Azure è isolata dalla connettività locale, mentre l'istanza del Servizio Migrazione del database di Azure è connessa alla rete locale.
- Vengono applicati i criteri di controllo degli accessi in base al ruolo. L'accesso utente viene limitato alla stessa sottoscrizione che ospita Istanza gestita di database SQL di Azure.
- Le reti virtuali usate per Istanza gestita di database SQL di Azure e per il Servizio Migrazione del database di Azure si trovano in sottoscrizioni diverse.

![Topologia di rete per Istanza gestita di database SQL di Azure isolata dalla rete locale](media\resource-network-topologies\mi-isolated-workload.png)

**Requisiti**
- Anche la rete virtuale usata dal Servizio Migrazione del database di Azure per questo scenario deve essere connessa alla rete locale tramite ExpressRoute o VPN.
- Creare un peering di rete virtuale tra la rete virtuale usata per Istanza gestita di database SQL di Azure e il Servizio Migrazione del database di Azure.


## <a name="cloud-to-cloud-migrations"></a>Migrazioni da cloud a cloud
Usare questa topologia se il server SQL di origine è ospitato in una macchina virtuale di Azure.

![Topologia di rete per migrazioni da cloud a cloud](media\resource-network-topologies\cloud-to-cloud.png)

**Requisiti**
- Creare un peering di rete virtuale tra la rete virtuale usata per Istanza gestita di database SQL di Azure e il Servizio Migrazione del database di Azure.

## <a name="see-also"></a>Vedere anche
- [Eseguire la migrazione di SQL Server a Istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Creare una rete virtuale usando il portale di Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica del Servizio Migrazione del database di Azure e la Disponibilità a livello di area durante l'anteprima pubblica, vedere l'articolo [Informazioni sull'anteprima del Servizio Migrazione del database di Azure](dms-overview.md). 