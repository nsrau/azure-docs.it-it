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
ms.openlocfilehash: 5904864ffba656dab17e1549ed9832be4258a67f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure tramite il Servizio Migrazione del database di Azure
In questo articolo verranno introdotte diverse topologie di rete usate dal Servizio Migrazione del database di Azure per garantire un'esperienza avanzata di migrazione dai server SQL locali a Istanza gestita di database SQL di Azure.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Istanza gestita di database SQL di Azure configurata per carichi di lavoro ibridi 
Usare questa topologia se Istanza gestita di database SQL di Azure è connessa alla rete locale. Questo approccio fornisce il routing di rete più semplificato e garantisce la massima velocità effettiva dei dati durante la migrazione.

![Topologia di rete per i carichi di lavoro ibridi](media\resource-network-topologies\hybrid-workloads.png)

**Requisiti**
- In questo scenario Istanza gestita di database SQL di Azure e l'istanza del Servizio Migrazione del database di Azure vengono create nella stessa rete virtuale di Azure, ma usano subnet diverse.  
- La rete virtuale usata in questo scenario è inoltre connessa alla rete locale tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Istanza gestita di database SQL di Azure isolata dalla rete locale
Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:
- Istanza gestita di database SQL di Azure è isolata dalla connettività locale, mentre l'istanza del Servizio Migrazione del database di Azure è connessa alla rete locale.
- Se vengono applicati i criteri di controllo degli accessi in base al ruolo, è necessario limitare l'accesso degli utenti alla stessa sottoscrizione che ospita Istanza gestita di database SQL di Azure.
- Le reti virtuali usate per Istanza gestita di database SQL di Azure e per il Servizio Migrazione del database di Azure si trovano in sottoscrizioni diverse.

![Topologia di rete per Istanza gestita di database SQL di Azure isolata dalla rete locale](media\resource-network-topologies\mi-isolated-workload.png)

**Requisiti**
- Anche la rete virtuale usata dal Servizio Migrazione del database di Azure per questo scenario deve essere connessa alla rete locale tramite (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- È necessario configurare un [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) tra la rete virtuale usata per Istanza gestita di database SQL di Azure e il Servizio Migrazione del database di Azure.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migrazioni da cloud a cloud: rete virtuale condivisa

Usare questa topologia se l'SQL Server di origine è ospitato in una macchina virtuale di Azure e condivide la stessa rete virtuale con Istanza gestita di database SQL e il Servizio Migrazione del database di Azure.

![Topologia di rete per migrazioni da cloud a cloud con una rete virtuale condivisa](media\resource-network-topologies\cloud-to-cloud.png)

**Requisiti**
- Nessun requisito aggiuntivo.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migrazioni da cloud a cloud: rete virtuale isolata

Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:
- Il provisioning di Istanza gestita di database SQL di Azure viene eseguito in una rete virtuale isolata.
- Se vengono applicati i criteri di controllo degli accessi in base al ruolo, è necessario limitare l'accesso degli utenti alla stessa sottoscrizione che ospita Istanza gestita di database SQL di Azure.
- Le reti virtuali usate per Istanza gestita di database SQL di Azure e per il Servizio Migrazione del database di Azure si trovano in sottoscrizioni diverse.

![Topologia di rete per migrazioni da cloud a cloud con una rete virtuale isolata](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**Requisiti**
- È necessario configurare un [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) tra la rete virtuale usata per Istanza gestita di database SQL di Azure e il Servizio Migrazione del database di Azure.


## <a name="see-also"></a>Vedere anche
- [Eseguire la migrazione di SQL Server a Istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Creare una rete virtuale usando il portale di Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica del Servizio Migrazione del database di Azure e la Disponibilità a livello di area durante l'anteprima pubblica, vedere l'articolo [Informazioni sull'anteprima del Servizio Migrazione del database di Azure](dms-overview.md). 