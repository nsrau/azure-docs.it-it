---
title: Topologie di rete per migrazioni a istanza gestita di Database di Azure SQL con servizio migrazione del Database di Azure | Microsoft Docs
description: Descrive le configurazioni di origine e di destinazione per il servizio migrazione del Database di Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/07/2019
ms.openlocfilehash: 74613599903f7cde606295a1e2d9eaaa0924cf50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808413"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Topologie di rete per migrazioni a istanza gestita di Azure SQL DB con servizio migrazione del Database di Azure

Questo articolo descrive diverse topologie di rete che possono usare servizio migrazione del Database di Azure per offrire un'esperienza completa migrazione dai server SQL locale a istanza gestita di Azure SQL Database.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Istanza gestita di database SQL di Azure configurata per carichi di lavoro ibridi 

Usare questa topologia se Istanza gestita di database SQL di Azure è connessa alla rete locale. Questo approccio fornisce il routing di rete più semplificato e garantisce la massima velocità effettiva dei dati durante la migrazione.

![Topologia di rete per i carichi di lavoro ibridi](media/resource-network-topologies/hybrid-workloads.png)

**Requisiti**

- In questo scenario, l'istanza di Database SQL di Azure gestiti e l'istanza del servizio migrazione del Database vengono creati nella stessa rete virtuale di Azure, ma usano subnet diverse.  
- La rete virtuale usata in questo scenario è inoltre connessa alla rete locale tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oppure [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Istanza gestita di database SQL di Azure isolata dalla rete locale

Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:

- L'istanza gestita di Database SQL di Azure è isolata dalla connettività locale, ma l'istanza del servizio migrazione del Database è connesso alla rete locale.
- Se sono presenti criteri basati su accesso controllo ruoli (RBAC) ed è necessario limitare l'accesso degli utenti alla stessa sottoscrizione che ospita l'istanza gestita di Database SQL di Azure.
- Le reti virtuali usate per l'istanza gestita di Azure SQL Database e il servizio migrazione del Database si trovano in sottoscrizioni diverse.

![Topologia di rete per Istanza gestita di database SQL di Azure isolata dalla rete locale](media/resource-network-topologies/mi-isolated-workload.png)

**Requisiti**

- La rete virtuale usata dal servizio migrazione del Database per questo scenario deve anche essere connessa alla rete locale tramite (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oppure [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configurare [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) tra la rete virtuale usata per il Database SQL di Azure gestiti istanza e servizio migrazione del Database.

## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Le migrazioni da cloud a cloud: rete virtuale condivisa

Usare questa topologia se SQL Server di origine è ospitato in una VM di Azure e condivide la stessa rete virtuale con istanza gestita di Azure SQL Database e il servizio migrazione del Database.

![Topologia di rete per migrazioni da Cloud a Cloud con una rete virtuale condivisa](media/resource-network-topologies/cloud-to-cloud.png)

**Requisiti**

- Nessun requisito aggiuntivo.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migrazioni da cloud a cloud: rete virtuale isolata

Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:

- L'istanza gestita di Database SQL di Azure viene eseguito il provisioning in una rete virtuale isolata.
- Se sono presenti criteri basati su accesso controllo ruoli (RBAC) ed è necessario limitare l'accesso degli utenti alla stessa sottoscrizione che ospita l'istanza gestita di Database SQL di Azure.
- Le reti virtuali usate per istanza gestita di Azure SQL Database e servizio migrazione del Database si trovano in sottoscrizioni diverse.

![Topologia di rete per migrazioni da Cloud a Cloud con una rete virtuale isolata](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisiti**

- Configurare [peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) tra la rete virtuale usata per il Database SQL di Azure gestiti istanza e servizio migrazione del Database.

## <a name="inbound-security-rules"></a>Regole di sicurezza in ingresso

| **NAME**   | **PORTA** | **PROTOCOLLO** | **SOURCE** | **DESTINAZIONE** | **AZIONE** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualsiasi      | Qualsiasi          | SUBNET DMS | Qualsiasi             | CONSENTI      |

## <a name="outbound-security-rules"></a>Regole di sicurezza in uscita

| **NAME**                  | **PORTA**                                              | **PROTOCOLLO** | **SOURCE** | **DESTINAZIONE**           | **AZIONE** | **Motivo della regola**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| management                | 443\.9354                                              | TCP          | Qualsiasi        | Qualsiasi                       | CONSENTI      | Comunicazione del piano di gestione tramite il bus di servizio e l'archivio BLOB di Azure. <br/>Se il peering Microsoft è abilitato, questa regola potrebbe non essere necessaria.                                                             |
| Diagnostica               | 12000                                                 | TCP          | Qualsiasi        | Qualsiasi                       | CONSENTI      | Il servizio Migrazione del database usa questa regola per raccogliere informazioni di diagnostica ai fini della risoluzione dei problemi.                                                                                                                      |
| Server di origine SQL         | 1433 (o la porta TCP IP su cui SQL Server è in ascolto) | TCP          | Qualsiasi        | Spazio indirizzi locale | CONSENTI      | Connettività dell'origine di SQL Server dal servizio Migrazione del database <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria.                                                                                       |
| Istanza denominata di SQL Server | 1434                                                  | UDP          | Qualsiasi        | Spazio indirizzi locale | CONSENTI      | Connettività dell'origine dell'istanza denominata di SQL Server dal servizio Migrazione del database <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria.                                                                        |
| Condivisione SMB                 | 445                                                   | TCP          | Qualsiasi        | Spazio indirizzi locale | CONSENTI      | Condivisione di rete SMB in cui il servizio Migrazione del database può archiviare i file di backup dei database per le migrazioni nell'istanza gestita del database SQL di Azure e nei server SQL nella macchina virtuale di Azure <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria. |
| DMS_subnet                | Qualsiasi                                                   | Qualsiasi          | Qualsiasi        | DMS_Subnet                | CONSENTI      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Vedere anche

- [Eseguire la migrazione di SQL Server a Istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Panoramica dei prerequisiti per usare servizio migrazione del Database di Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Creare una rete virtuale usando il portale di Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del servizio migrazione del Database di Azure, vedere l'articolo [What ' s Azure Database Migration Service?](dms-overview.md).
- Per informazioni aggiornate sulla disponibilità a livello di area del servizio migrazione del Database di Azure, vedere la [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) pagina.
