---
title: Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure tramite il Servizio Migrazione del database di Azure | Microsoft Docs
description: Informazioni sulle configurazioni di origine e di destinazione per il servizio Migrazione del database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: d12d6b1274a756bfb13761ab999a1539bcee3657
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58178119"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure tramite il Servizio Migrazione del database di Azure
Questo articolo illustra diverse topologie di rete usate dal Servizio Migrazione del database di Azure per garantire un'esperienza di migrazione completa dai server SQL locali a Istanza gestita di database SQL di Azure.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Istanza gestita di database SQL di Azure configurata per carichi di lavoro ibridi 
Usare questa topologia se Istanza gestita di database SQL di Azure è connessa alla rete locale. Questo approccio fornisce il routing di rete più semplificato e garantisce la massima velocità effettiva dei dati durante la migrazione.

![Topologia di rete per i carichi di lavoro ibridi](media/resource-network-topologies/hybrid-workloads.png)

**Requisiti**
- In questo scenario Istanza gestita di database SQL di Azure e l'istanza del Servizio Migrazione del database di Azure vengono create nella stessa rete virtuale di Azure, ma usano subnet diverse.  
- La rete virtuale usata in questo scenario è inoltre connessa alla rete locale tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Istanza gestita di database SQL di Azure isolata dalla rete locale
Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:
- Istanza gestita di database SQL di Azure è isolata dalla connettività locale, mentre l'istanza del Servizio Migrazione del database di Azure è connessa alla rete locale.
- Se vengono applicati i criteri di controllo degli accessi in base al ruolo, è necessario limitare l'accesso degli utenti alla stessa sottoscrizione che ospita Istanza gestita di database SQL di Azure.
- Le reti virtuali usate per Istanza gestita di database SQL di Azure e per il Servizio Migrazione del database di Azure si trovano in sottoscrizioni diverse.

![Topologia di rete per Istanza gestita di database SQL di Azure isolata dalla rete locale](media/resource-network-topologies/mi-isolated-workload.png)

**Requisiti**
- Anche la rete virtuale usata dal Servizio Migrazione del database di Azure per questo scenario deve essere connessa alla rete locale tramite (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configurare un [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) tra la rete virtuale usata per Istanza gestita di database SQL di Azure e il Servizio Migrazione del database di Azure.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migrazioni da cloud a cloud: rete virtuale condivisa

Usare questa topologia se l'SQL Server di origine è ospitato in una macchina virtuale di Azure e condivide la stessa rete virtuale con Istanza gestita di database SQL e il Servizio Migrazione del database di Azure.

![Topologia di rete per migrazioni da cloud a cloud con una rete virtuale condivisa](media/resource-network-topologies/cloud-to-cloud.png)

**Requisiti**
- Nessun requisito aggiuntivo.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migrazioni da cloud a cloud: rete virtuale isolata

Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:
- Il provisioning di Istanza gestita di database SQL di Azure viene eseguito in una rete virtuale isolata.
- Se vengono applicati i criteri di controllo degli accessi in base al ruolo, è necessario limitare l'accesso degli utenti alla stessa sottoscrizione che ospita Istanza gestita di database SQL di Azure.
- Le reti virtuali usate per Istanza gestita di database SQL di Azure e per il Servizio Migrazione del database di Azure si trovano in sottoscrizioni diverse.

![Topologia di rete per migrazioni da cloud a cloud con una rete virtuale isolata](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisiti**
- Configurare un [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) tra la rete virtuale usata per Istanza gestita di database SQL di Azure e il Servizio Migrazione del database di Azure.

## <a name="inbound-security-rules"></a>Regole di sicurezza in ingresso

| **NOME**   | **PORTA** | **PROTOCOLLO** | **SOURCE** | **DESTINAZIONE** | **AZIONE** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualsiasi      | Qualsiasi          | SUBNET DMS | Qualsiasi             | CONSENTI      |

## <a name="outbound-security-rules"></a>Regole di sicurezza in uscita

| **NOME**                  | **PORTA**                                              | **PROTOCOLLO** | **SOURCE** | **DESTINAZIONE**           | **AZIONE** | **Motivo della regola**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| management                | 443.9354                                              | TCP          | Qualsiasi        | Qualsiasi                       | CONSENTI      | Comunicazione del piano di gestione tramite il bus di servizio e l'archivio BLOB di Azure. <br/>Se il peering Microsoft è abilitato, questa regola potrebbe non essere necessaria.                                                             |
| Diagnostica               | 12000                                                 | TCP          | Qualsiasi        | Qualsiasi                       | CONSENTI      | Il servizio Migrazione del database usa questa regola per raccogliere informazioni di diagnostica ai fini della risoluzione dei problemi.                                                                                                                      |
| Server di origine SQL         | 1433 (o la porta TCP IP su cui SQL Server è in ascolto) | TCP          | Qualsiasi        | Spazio indirizzi locale | CONSENTI      | Connettività dell'origine di SQL Server dal servizio Migrazione del database <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria.                                                                                       |
| Istanza denominata di SQL Server | 1434                                                  | UDP          | Qualsiasi        | Spazio indirizzi locale | CONSENTI      | Connettività dell'origine dell'istanza denominata di SQL Server dal servizio Migrazione del database <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria.                                                                        |
| Condivisione SMB                 | 445                                                   | TCP          | Qualsiasi        | Spazio indirizzi locale | CONSENTI      | Condivisione di rete SMB in cui il servizio Migrazione del database può archiviare i file di backup dei database per le migrazioni nell'istanza gestita del database SQL di Azure e nei server SQL nella macchina virtuale di Azure <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria. |
| DMS_subnet                | Qualsiasi                                                   | Qualsiasi          | Qualsiasi        | DMS_Subnet                | CONSENTI      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Vedere anche
- [Eseguire la migrazione di SQL Server a Istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Creare una rete virtuale usando il portale di Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica del Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](dms-overview.md).
- Per informazioni aggiornate sulla disponibilità a livello di area del Servizio Migrazione del database di Azure, vedere la pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).