---
title: Livello di servizio Utilizzo generico
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Informazioni sul livello di servizio per utilizzo generico per il database SQL di Azure e Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986642"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Livello di servizio per utilizzo generico: database SQL di Azure e Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Il per utilizzo generico livello di servizio nel modello di acquisto basato su vCore è denominato livello di servizio standard nel modello di acquisto basato su DTU. Per un confronto tra il modello di acquisto basato su vCore e il modello di acquisto basato su DTU, vedere [acquisto di modelli e risorse](purchasing-models.md).

Il database SQL di Azure e Istanza gestita SQL di Azure si basano sull'architettura del motore di database SQL Server adattata per l'ambiente cloud allo scopo di garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. 

Sono disponibili due livelli di servizio usati dal database SQL di Azure e da SQL Istanza gestita: 

- Utilizzo generico
- Business Critical

Il database SQL di Azure dispone anche di un terzo livello di servizio, che attualmente non è disponibile per Istanza gestita SQL di Azure:

- Hyperscale

Il modello di architettura per il livello di servizio per utilizzo generico è basato sulla separazione tra calcolo e archiviazione. Questo modello di architettura si basa sull'elevata disponibilità e affidabilità di Archiviazione BLOB di Azure che replica i file di database in modo trasparente e impedisce la perdita di dati in caso di un errore dell'infrastruttura sottostante.

La figura seguente illustra quattro nodi nel modello dell'architettura standard con i livelli di calcolo e archiviazione separati.

![Separazione di calcolo e archiviazione](./media/service-tier-general-purpose/general-purpose-service-tier.png)

Nel modello di architettura per il livello di servizio per utilizzo generico sono disponibili due livelli:

- Un livello di calcolo senza stato che esegue il processo `sqlservr.exe` e contiene solo i dati temporanei e memorizzati nella cache, ad esempio cache dei piani, pool di buffer, pool dell'archivio colonne. Questo nodo senza stato viene gestito da Azure Service Fabric che inizializza il processo, controlla l'integrità del nodo ed esegue il failover in un'altra posizione, se necessario.
- Un livello di dati con stato con i file di database (con estensione mdf/ldf) archiviati in Archiviazione BLOB di Azure. Archiviazione BLOB di Azure garantisce che i dati dei record che si trovano in un file di database non vadano persi. Archiviazione di Azure dispone di disponibilità/ridondanza dei dati incorporata che garantisce che tutti i record nel file di log o nella pagina nel file di dati vengano conservati anche se il processo si arresta in modo anomalo.

Ogni volta che il motore di database o il sistema operativo viene aggiornato, una parte dell'infrastruttura sottostante ha esito negativo o se nel processo viene rilevato un problema critico `sqlservr.exe` , Azure Service Fabric sposterà il processo senza stato in un altro nodo di calcolo senza stato. È presente un set di nodi di riserva in attesa di eseguire un nuovo servizio di calcolo in caso di failover del nodo primario, per ridurre al minimo il tempo di failover. I dati nel livello di archiviazione di Azure non sono interessati e i file di dati/log sono collegati al processo appena inizializzato. Questo processo garantisce una disponibilità del 99,99%, ma potrebbe avere alcune conseguenze sulle prestazioni per carichi di lavoro pesanti in esecuzione a causa del tempo di transizione e del fatto che il nuovo nodo inizia con la cache a freddo.

## <a name="when-to-choose-this-service-tier"></a>Quando scegliere questo livello di servizio

Il livello di servizio per utilizzo generico è un livello di servizio predefinito nel database SQL di Azure e Istanza gestita SQL di Azure progettato per la maggior parte dei carichi di lavoro generici. Se è necessario un motore di database completamente gestito con contratto di assistenza 99,99% con latenza di archiviazione compresa tra 5 e 10 ms che corrispondono SQL Server in una macchina virtuale di Azure nella maggior parte dei casi, il livello di per utilizzo generico è l'opzione per l'utente.

## <a name="next-steps"></a>Passaggi successivi

- Trovare le caratteristiche delle risorse (numero di core, I/O, memoria) del livello per utilizzo generico/standard in [SQL istanza gestita](../managed-instance/resource-limits.md#service-tier-characteristics), database singolo nel modello [Vcore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) o [modello DTU](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)o pool elastico nel modello [vCore](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) e [DTU](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits).
- Informazioni sui livelli [Business Critical](service-tier-business-critical.md) e [Hyperscale](service-tier-hyperscale.md).
- Informazioni sulle [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Per altre opzioni per la disponibilità elevata e il ripristino di emergenza, vedere [continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md).
