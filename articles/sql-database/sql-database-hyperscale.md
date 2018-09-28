---
title: Panoramica dell'iperscalabilità del database SQL di Azure | Documentazione Microsoft
description: In questo argomento viene descritto il livello di servizio con iperscalabilità nel modello di acquisto basato su vCore nel database SQL di Azure e viene illustrato in cosa differisce dai livelli di servizio per l'utilizzo generico e business critical.
services: sql-database
author: CarlRabeler
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 09/23/2018
ms.author: carlrab
ms.openlocfilehash: b50d049d051a57ba12239d4619572dc0dbd7e32f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962965"
---
# <a name="what-is-the-hyperscale-service-tier-preview-in-azure-sql-database"></a>Informazioni sul livello di servizio con iperscalabilità (anteprima) nel database SQL di Azure

Il livello di servizio con iperscalabilità nel database SQL di Azure è il livello di servizio più recente nel modello di acquisto basato su vCore. Questo livello di servizio è un altamente scalabile per le prestazioni di archiviazione e calcolo, e sfrutta l'architettura di Azure per scalare orizzontalmente le risorse di archiviazione e di calcolo per un database SQL di Azure sostanzialmente oltre i limiti disponibili per i livelli di utilizzo generico e business critical.

## <a name="what-are-the-capabilities-of-the-hyperscale-service-tier"></a>Funzionalità del livello di servizio con iperscalabilità

Il livello di servizio con iperscalabilità nel database SQL di Azure offre le seguenti funzionalità aggiuntive:

- Supporto per database di dimensioni massime di 100 TB 
- Backup di database di grandi dimensioni più veloce (basato su snapshot di file)
- Ripristino di database di grandi dimensioni più veloce (basato su snapshot di file) 
- Prestazioni complessive più elevate grazie alla maggiore velocità effettiva dei log e ai tempi di esecuzione di commit delle transazioni più veloci, indipendentemente dai volumi di dati 
- Rapida scalabilità orizzontale: è possibile effettuare il provisioning di uno o più nodi di sola lettura per l'offload del carico di lavoro di lettura e per l'uso come hot standby
- Rapida scalabilità orizzontale: in un tempo costante è possibile aumentare le risorse di calcolo per supportare ingenti carichi di lavoro secondo necessità, e quindi ridurre nuovamente le risorse di calcolo quando non necessarie. 

Il livello di servizio con iperscalabilità elimina molti dei limiti pratici che generalmente caratterizzano i database cloud. Se la maggior parte dei database sono limitati dalle risorse disponibili in un singolo nodo, i database nel livello di servizio con iperscalabilità non presentano limiti di questo tipo. Grazie all'architettura di archiviazione flessibile è possibile scalare orizzontalmente la capacità di archiviazione secondo necessità. Infatti, i database con iperscalabilità non vengono creati con un limite definito per la dimensione massima. Un database con iperscalabilità può espandersi in base alle esigenze, e la fatturazione avviene solo in base alla capacità in uso. Per i carichi di lavoro con intense attività di lettura, il livello di servizio con iperscalabilità consente la rapida scalabilità orizzontale effettuando il provisioning di repliche in lettura aggiuntive in base alle necessità per l'offload dei carichi di lavoro di lettura. 

Inoltre, il tempo necessario per creare i backup dei database oppure aumentare o diminuire le prestazioni non è più associato al volume dei dati presenti nel database. È possibile eseguire il backup dei database con iperscalabilità praticamente istantaneamente. È anche possibile ridimensionare un database aumentando o diminuendo la capacità di decine di terabyte in pochi minuti. Questa funzionalità consente di non essere vincolati alle scelte di configurazione iniziali. 

Per altre informazioni sulle dimensioni di calcolo per il livello di servizio con iperscalabilità, vedere [Scelta del livello di servizio vCore e delle risorse di calcolo, memoria, archiviazione e I/O](sql-database-service-tiers-vcore.md).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Destinazione d'uso del livello di servizio con iperscalabilità

Il livello di servizio con iperscalabilità è destinato principalmente ai clienti che dispongono di database di grandi dimensioni in sede e che vogliono modernizzare le proprie applicazioni passando al cloud, oppure per i clienti che giù dispongono del cloud ma sono limitati dalle dimensioni massime del database (1-4 TB). È destinato anche ai clienti che sono alla ricerca di alte prestazioni e scalabilità elevata per l'archiviazione e il calcolo.

Il livello di servizio con iperscalabilità supporta tutti i carichi di lavoro di Microsoft SQL Server ma è ottimizzato principalmente per OLTP. Il livello di servizio con iperscalabilità supporta anche carichi di lavoro ibridi e analitici (data mart). 

> [!IMPORTANT]
> I pool elastici non supportano il livello di servizio con iperscalabilità.

## <a name="architecture-distributing-functions-to-isolate-capabilities"></a>Architettura: distribuzione di funzioni per isolare le funzionalità

A differenza dei motori di database tradizionali che centralizzano tutte le funzioni di gestione dati in un unico percorso o processo (perfino i cosiddetti database distribuiti per la produzione dispongono attualmente di più copie di un motore dati monolitico), un database con iperscalabilità separa il motore di elaborazione query, in cui far divergere la semantica dei vari motori di dati, dai componenti che offrono archiviazione a lungo termine e la durabilità dei dati. In questo modo, la capacità di archiviazione può essere facilmente aumentata secondo necessità (l'obiettivo iniziale è 100 TB). Dato che le repliche di sola lettura condividono gli stessi componenti di calcolo, nessuna copia dei dati è necessaria per creare rapidamente una nuova replica leggibile.

Il diagramma seguente illustra i diversi tipi di nodi in un database con iperscalabilità:

![architettura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Un database con iperscalabilità contiene i seguenti tipi diversi di nodi:
 
### <a name="compute-node"></a>Nodo di calcolo

Il nodo di calcolo è dove si trova il motore relazionale e dove si verificano tutti i gli elementi del linguaggio, dell'elaborazione delle query e così via. Tutte le interazioni dell'utente con un database con iperscalabilità vengono eseguite tramite questi nodi di calcolo. I nodi di calcolo presentano cache basae su SSD (con etichetta RBPEX - estensione del pool di buffer resiliente nel diagramma precedente) per ridurre al minimo il numero di round trip di rete necessari per recuperare una pagina di dati. È presente un nodo di calcolo primario in cui vengono elaborati tutti i carichi di lavoro di lettura/scrittura e le transazioni. Sono presenti uno o più nodi di calcolo secondari che fungono da nodi di hot standby per finalità di failover, oltre a fungere da nodi di calcolo di sola lettura per l'offload di carichi di lavoro di lettura (se si desidera questa funzionalità).

### <a name="page-server-node"></a>Nodo server di pagina

I servers di pagina sono sistemi che rappresentano un motore di archiviazione con scalabilità orizzontale.  Ogni server di pagina è responsabile di un subset delle pagine nel database.  Nominalmente, ogni server di pagina controlla 1 terabyte di dati. Nessun dato è condiviso su più di un server di pagina (al di là delle repliche mantenute per ridondanza e disponibilità). Il compito di un server di pagina consiste nel fornire su richiesta le pagine del database ai nodi di calcolo e nel mantenere le pagine aggiornate man mano che le transazioni aggiornano i dati. I server di pagina vengono tenuti aggiornati riproducendo i record di log dal servizio di log. Per migliorare le prestazioni, i server di pagina gestiscono anche le cache basate su SSD. L'archiviazione a lungo termine delle pagine di dati viene mantenuta in Archiviazione di Azure per garantire maggiore affidabilità.

### <a name="log-service-node"></a>Nodo del servizio di log

Il nodo del servizio di log accetta i record di log dal nodo di calcolo primario, li rende persistenti in una cache durevole e inoltra i record del log ai restanti nodi di calcolo (in modo che possano aggiornare le cache) nonché ai server di pagina pertinenti, in modo che i dati possano essere qui aggiornati. In questo modo, tutte le modifiche ai dati dal nodo di calcolo primario vengono propagate tramite il servizio di log a tutti i nodi di calcolo secondari e ai server di pagina. Infine, i record di log vengono inviati all'archiviazione a lungo termine in Archiviazione di Azure, ovvero un repository di archiviazione infinito. Questo meccanismo elimina la necessità di troncamento frequente dei log. Il servizio di log include inoltre la cache locale per velocizzare l'accesso.

### <a name="azure-storage-node"></a>Nodo di archiviazione di Azure

Il nodo di archiviazione di Azure è la destinazione finale dei dati dal server di pagina. Questo archivio viene usato per scopi di backup così come per la replica tra aree di Azure. I backup sono costituiti da snapshot di file di dati. Le operazioni di ripristino sono veloci grazie a questi snapshot e i dati possono essere ripristinati in qualsiasi punto nel tempo. 

## <a name="backup-and-restore"></a>Backup e ripristino

I backup sono snapshot di file di base e di conseguenza sono quasi istantanei. La separazione tra calcolo e archiviazione consente di portare backup/operazione di ripristino a livello di archiviazione per ridurre il carico di elaborazione nel nodo di calcolo primario. Di conseguenza, il backup di un database di grandi dimensioni non compromette le prestazioni del nodo di calcolo primario. Analogamente, le operazioni di ripristino vengono eseguite tramite la copia di snapshot di file e di conseguenza non costituiscono un'operazione di dimensionamento dei dati. Per i ripristini nello stesso account di archiviazione, l'operazione di ripristino è veloce.

## <a name="scale-and-performance-advantages"></a>Vantaggi di scalabilità e prestazioni

Con la possibilità di accelerare/diminuore la velocità dei nodi di calcolo di sola lettura aggiuntivi, l'architettura con iperscalabilità offre significative funzionalità di scalabilità di lettura e consente inoltre di liberare il nodo di calcolo primario per la gestione di più richieste di scrittura. Inoltre, i nodi di calcolo possono essere aumentati o diminuiti rapidamente grazie all'architettura di archiviazione condivisa dell'architettura con iperscalabilità. 


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui livelli di servizio, vedere [Livelli di servizio](sql-database-service-tiers.md)
- Per informazioni dettagliate riguardanti i limiti delle risorse, vedere [Limiti delle risorse di vCore](sql-database-resource-limits.md)
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
