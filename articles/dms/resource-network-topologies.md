---
title: Topologie di rete per le migrazioni di istanze gestite SQLNetwork topologies for SQL managed instance migrations
titleSuffix: Azure Database Migration Service
description: Informazioni sulle configurazioni di origine e di destinazione per le migrazioni di istanze gestite del database SQL di Azure usando il servizio Migrazione del database di Azure.Learn the source and target configurations for Azure SQL Database managed instance migrations using the Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254951"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure usando il servizio di migrazione del database di AzureNetwork topologies for Azure SQL DB Managed Instance migrations using Azure Database Migration Service

Questo articolo illustra varie topologie di rete che il servizio Migrazione del database di Azure può usare per fornire un'esperienza di migrazione completa dai server SQL locali all'istanza gestita del database SQL di Azure.This article discusses various network topologies that Azure Database Migration Service can work with to provide a comprehensive migration experience from on-premises SQL Servers to Azure SQL Database Managed Instance.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Istanza gestita di database SQL di Azure configurata per carichi di lavoro ibridi 

Usare questa topologia se Istanza gestita di database SQL di Azure è connessa alla rete locale. Questo approccio fornisce il routing di rete più semplificato e garantisce la massima velocità effettiva dei dati durante la migrazione.

![Topologia di rete per i carichi di lavoro ibridi](media/resource-network-topologies/hybrid-workloads.png)

**Requisiti**

- In questo scenario, l'istanza gestita del database SQL di Azure e l'istanza del servizio di migrazione del database di Azure vengono create nella stessa rete virtuale di Microsoft Azure, ma usano subnet diverse.  
- La rete virtuale utilizzata in questo scenario è anche connessa alla rete locale tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Istanza gestita di database SQL di Azure isolata dalla rete locale

Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:

- L'istanza gestita del database SQL di Azure è isolata dalla connettività locale, ma l'istanza del servizio di migrazione del database di Azure è connessa alla rete locale.
- Se sono presenti criteri di controllo degli accessi in base al ruolo ed è necessario limitare gli utenti all'accesso alla stessa sottoscrizione che ospita l'istanza gestita del database SQL di Azure.If Role Based Access Control (RBAC) policies are in place and you need to limit users to accessing the same subscription that is hosting the Azure SQL Database managed instance.
- Le reti virtuali usate per l'istanza gestita del database SQL di Azure e il servizio di migrazione del database di Azure si trovano in sottoscrizioni diverse.

![Topologia di rete per Istanza gestita di database SQL di Azure isolata dalla rete locale](media/resource-network-topologies/mi-isolated-workload.png)

**Requisiti**

- Anche la rete virtuale usata dal servizio di migrazione del database di Azurehttps://docs.microsoft.com/azure/expressroute/expressroute-introduction) per questo scenario deve essere connessa alla rete locale tramite ( o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configurare il peering della rete vNet tra la rete virtuale usata per l'istanza gestita del database SQL di Azure e il servizio di migrazione del database di Azure.Set up [VNet network peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) between the virtual network used for Azure SQL Database managed instance and Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrazioni da cloud a cloud: rete virtuale condivisa

Usare questa topologia se l'istanza di origine di SQL Server è ospitata in una macchina virtuale di Azure e condivide la stessa rete virtuale con l'istanza gestita del database SQL di Azure e il servizio di migrazione del database di Azure.Use this topology if the source SQL Server is hosted in an Azure VM and shares the same virtual network with Azure SQL Database managed instance and Azure Database Migration Service.

![Topologia di rete per le migrazioni cloud-cloud con una rete virtuale condivisaNetwork Topology for Cloud-to-Cloud migrations with a shared VNet](media/resource-network-topologies/cloud-to-cloud.png)

**Requisiti**

- Nessun requisito aggiuntivo.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migrazioni da cloud a cloud: rete virtuale isolata

Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:

- Viene eseguito il provisioning dell'istanza gestita del database SQL di Azure in una rete virtuale isolata.
- Se sono presenti criteri di controllo degli accessi in base al ruolo ed è necessario limitare gli utenti all'accesso alla stessa sottoscrizione che ospita l'istanza gestita del database SQL di Azure.If Role Based Access Control (RBAC) policies are in place and you need to limit users to accessing the same subscription that is hosting the Azure SQL Database managed instance.
- Le reti virtuali usate per l'istanza gestita del database SQL di Azure e il servizio di migrazione del database di Azure si trovano in sottoscrizioni diverse.

![Topologia di rete per le migrazioni cloud-cloud con una rete virtuale isolataNetwork Topology for Cloud-to-Cloud migrations with an isolated VNet](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisiti**

- Configurare il peering della rete vNet tra la rete virtuale usata per l'istanza gestita del database SQL di Azure e il servizio di migrazione del database di Azure.Set up [VNet network peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) between the virtual network used for Azure SQL Database managed instance and Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Regole di sicurezza in ingresso

| **NOME**   | **Porta** | **Protocollo** | **fonte** | **Destinazione** | **Azione** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualsiasi      | Qualsiasi          | SUBNET DMS | Qualsiasi             | Allow      |

## <a name="outbound-security-rules"></a>Regole di sicurezza in uscita

| **NOME**                  | **Porta**                                              | **Protocollo** | **fonte** | **Destinazione**           | **Azione** | **Motivo della regola**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| management                | 443.9354                                              | TCP          | Qualsiasi        | Qualsiasi                       | Allow      | Comunicazione del piano di gestione tramite il bus di servizio e l'archiviazione BLOB di Azure.Management plane communication through Service Bus and Azure blob storage. <br/>Se il peering Microsoft è abilitato, questa regola potrebbe non essere necessaria.                                                             |
| Diagnostica               | 12000                                                 | TCP          | Qualsiasi        | Qualsiasi                       | Allow      | Il servizio Migrazione del database usa questa regola per raccogliere informazioni di diagnostica ai fini della risoluzione dei problemi.                                                                                                                      |
| Server di origine SQL         | 1433 (o la porta TCP IP su cui SQL Server è in ascolto) | TCP          | Qualsiasi        | Spazio indirizzi locale | Allow      | Connettività dell'origine di SQL Server dal servizio Migrazione del database <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria.                                                                                       |
| Istanza denominata di SQL Server | 1434                                                  | UDP          | Qualsiasi        | Spazio indirizzi locale | Allow      | Connettività dell'origine dell'istanza denominata di SQL Server dal servizio Migrazione del database <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria.                                                                        |
| Condivisione SMB                 | 445                                                   | TCP          | Qualsiasi        | Spazio indirizzi locale | Allow      | Condivisione di rete SMB in cui il servizio Migrazione del database può archiviare i file di backup dei database per le migrazioni nell'istanza gestita del database SQL di Azure e nei server SQL nella macchina virtuale di Azure <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria. |
| DMS_subnet                | Qualsiasi                                                   | Qualsiasi          | Qualsiasi        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Vedere anche

- [Eseguire la migrazione di SQL Server all'istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Panoramica dei prerequisiti per l'uso del servizio di migrazione del database di AzureOverview of prerequisites for using Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Creare una rete virtuale usando il portale di AzureCreate a virtual network using the Azure portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del servizio di migrazione del database di Azure, vedere l'articolo [Che cos'è](dms-overview.md)il servizio di migrazione del database di Azure? .
- Per informazioni aggiornate sulla disponibilità regionale del servizio migrazione del database di Azure, vedere la pagina [Prodotti disponibili per area.](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)
