---
title: Livello di servizio generico
description: Informazioni sul livello generico del database SQL di AzureLearn about the Azure SQL Database general-purpose tier
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255886"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Livello di servizio di utilizzo generico - Database SQL di Azure

> [!NOTE]
> Il livello di servizio generico nel modello di acquisto basato su vCore è denominato livello di servizio standard nel modello di acquisto basato su DTU. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

Il database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud al fine di garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Vengono usati tre livelli di servizio nel database SQL di Azure, ognuno con diversi modelli di architettura. Questi livelli di servizio sono:

- Scopo generico
- Business Critical
- Hyperscale

Il modello architetturale per il livello di servizio generico si basa su una separazione tra elaborazione e archiviazione. Questo modello di architettura si basa sull'elevata disponibilità e affidabilità di Archiviazione BLOB di Azure che replica i file di database in modo trasparente e impedisce la perdita di dati in caso di un errore dell'infrastruttura sottostante.

La figura seguente illustra quattro nodi nel modello dell'architettura standard con i livelli di calcolo e archiviazione separati.

![Separazione di calcolo e archiviazione](media/sql-database-managed-instance/general-purpose-service-tier.png)

Nel modello architettonico per il livello di servizio generico sono disponibili due livelli:In the architectural model for the general-purpose service tier, there are two layers:

- Un livello di calcolo senza stato che esegue il processo `sqlservr.exe` e contiene solo i dati temporanei e memorizzati nella cache, ad esempio cache dei piani, pool di buffer, pool dell'archivio colonne. Il nodo di SQL Server senza stato è gestito da Azure Service Fabric che inizializza il processo, controlla l'integrità del nodo e, se necessario, esegue il failover in un'altra posizione.
- Un livello di dati con stato con i file di database (con estensione mdf/ldf) archiviati in Archiviazione BLOB di Azure. Archiviazione BLOB di Azure garantisce che i dati dei record che si trovano in un file di database non vadano persi. Archiviazione di Azure è dotato di disponibilità/ridondanza dei dati incorporata che assicura che ogni record nel file di log o pagina nel file di dati verrà conservato anche se si blocca il processo di SQL Server.

Ogni volta che viene aggiornato il motore di database o il sistema operativo, ogni volta che una parte dell'infrastruttura sottostante non funziona oppure viene rilevato un problema critico nel processo di SQL Server, Azure Service Fabric sposta il processo di SQL Server senza stato in un altro nodo di calcolo senza stato. È presente un set di nodi di riserva in attesa di eseguire un nuovo servizio di calcolo in caso di failover del nodo primario, per ridurre al minimo il tempo di failover. I dati nel livello di archiviazione di Azure non sono interessati e i dati e i file di log vengono associati al processo di SQL Server appena inizializzato. Questo processo garantisce il 99,99% della disponibilità, ma potrebbe influire sulle prestazioni di un carico di lavoro importante in esecuzione a causa del tempo di transizione e del fatto che il nuovo nodo di SQL Server inizia con la cache a freddo.

## <a name="when-to-choose-this-service-tier"></a>Quando scegliere questo livello di servizio

Il livello di servizio per utilizzo generico è un livello di servizio predefinito nel database SQL di Azure progettato per la maggior parte dei carichi di lavoro generici. Se è necessario un motore di database completamente gestito con un SLA del 99,99% con latenza di archiviazione compresa tra 5 e 10 ms che corrispondono a Azure SQL IaaS nella maggior parte dei casi, il livello General Purpose è l'opzione che fa per te.

## <a name="next-steps"></a>Passaggi successivi

- Trova le caratteristiche delle risorse (numero di core, I/O, memoria) del livello Scopo generale/Standard [nell'istanza gestita](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), Database singolo nel [modello vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) o [modello DTU](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)o Pool elastico nel [modello vCore](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) e [nel modello DTU](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Informazioni sui livelli [Business Critical](sql-database-service-tier-business-critical.md) e [Hyperscale](sql-database-service-tier-hyperscale.md).
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md).
- Per altre opzioni per la disponibilità elevata e il ripristino di emergenza, vedere [Continuità aziendale](sql-database-business-continuity.md).
