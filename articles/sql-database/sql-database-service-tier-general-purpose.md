---
title: Livello di servizio per utilizzo generico-database SQL di Azure | Microsoft Docs
description: Informazioni sul livello di utilizzo generico per il database SQL di Azure
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
ms.openlocfilehash: 2573adcb199ab32467493729842b6c47e3add64b
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515305"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Livello di servizio di utilizzo generico - Database SQL di Azure

> [!NOTE]
> Il livello di servizio per utilizzo generico nel modello di acquisto basato su vCore è denominato livello di servizio standard nel modello di acquisto basato su DTU. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

Il database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud al fine di garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Vengono usati tre livelli di servizio nel database SQL di Azure, ognuno con diversi modelli di architettura. Questi livelli di servizio sono:

- Utilizzo generico
- Business Critical
- Hyperscale

Il modello di architettura per il livello di servizio per utilizzo generico si basa su una separazione tra calcolo e archiviazione. Questo modello di architettura si basa sull'elevata disponibilità e affidabilità di Archiviazione BLOB di Azure che replica i file di database in modo trasparente e impedisce la perdita di dati in caso di un errore dell'infrastruttura sottostante.

La figura seguente illustra quattro nodi nel modello dell'architettura standard con i livelli di calcolo e archiviazione separati.

![Separazione di calcolo e archiviazione](media/sql-database-managed-instance/general-purpose-service-tier.png)

Nel modello di architettura per il livello di servizio per utilizzo generico sono disponibili due livelli:

- Un livello di calcolo senza stato che esegue il processo `sqlserver.exe` e contiene solo i dati temporanei e memorizzati nella cache, ad esempio cache dei piani, pool di buffer, pool dell'archivio colonne. Il nodo di SQL Server senza stato è gestito da Azure Service Fabric che inizializza il processo, controlla l'integrità del nodo e, se necessario, esegue il failover in un'altra posizione.
- Un livello di dati con stato con i file di database (con estensione mdf/ldf) archiviati in Archiviazione BLOB di Azure. Archiviazione BLOB di Azure garantisce che i dati dei record che si trovano in un file di database non vadano perduti. Archiviazione di Azure è dotato di disponibilità/ridondanza dei dati incorporata che assicura che ogni record nel file di log o pagina nel file di dati verrà conservato anche se si blocca il processo di SQL Server.

Ogni volta che viene aggiornato il motore di database o il sistema operativo, ogni volta che una parte dell'infrastruttura sottostante non funziona oppure viene rilevato un problema critico nel processo di SQL Server, Azure Service Fabric sposta il processo di SQL Server senza stato in un altro nodo di calcolo senza stato. È presente un set di nodi di riserva in attesa di eseguire un nuovo servizio di calcolo in caso di failover del nodo primario, per ridurre al minimo il tempo di failover. I dati nel livello di archiviazione di Azure non sono interessati e i dati e i file di log vengono associati al processo di SQL Server appena inizializzato. Questo processo garantisce il 99,99% della disponibilità, ma potrebbe influire sulle prestazioni di un carico di lavoro importante in esecuzione a causa del tempo di transizione e del fatto che il nuovo nodo di SQL Server inizia con la cache a freddo.

## <a name="when-to-choose-this-service-tier"></a>Quando scegliere questo livello di servizio

Il livello di servizio per utilizzo generico è un livello di servizio predefinito nel database SQL di Azure progettato per la maggior parte dei carichi di lavoro generici. Se è necessario un motore di database completamente gestito con contratto di assistenza del 99,99% con latenza di archiviazione compresa tra 5 e 10 ms che corrisponde a IaaS SQL di Azure nella maggior parte dei casi, per utilizzo generico livello è l'opzione per l'utente.

## <a name="next-steps"></a>Passaggi successivi

- Trovare le caratteristiche delle risorse (numero di core, i/o, memoria) del livello per utilizzo generico/standard in [istanza gestita](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), database singolo nel modello [Vcore](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-for-provisioned-compute) o [modello DTU](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)o pool elastico nel modello [vCore](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes) e [DTU](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Informazioni sui livelli [Business Critical](sql-database-service-tier-business-critical.md) e [Hyperscale](sql-database-service-tier-hyperscale.md).
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md).
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
