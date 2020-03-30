---
title: Livello di servizio business critical
description: Informazioni sul livello Business critical del database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268678"
---
# <a name="business-critical-tier---azure-sql-database"></a>Livello Business critical - Servizio di database SQL di Azure

> [!NOTE]
> Il livello Business critical è chiamato Premium nel modello di acquisto basato su DTU. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli architetturali usati nel database SQL di Azure:
- Utilizzo generico/Standard 
- Business critical/Premium
- Hyperscale

Il modello di livello di servizio Premium/Business critical è basato su un cluster di processi del motore di database. Questo modello di architettura si basa sul fatto che c’è sempre un quorum di nodi di motore di database disponibili e ha un impatto minimo sulle prestazioni sul carico di lavoro anche durante le attività di manutenzione.

Azure aggiorna e applica le patch al sistema operativo, ai driver e al motore di database di SQL Server in modo trasparente con tempi di inattività minimi per gli utenti finali. 

Nei livelli di servizio Premium e Business critical del database SQL di Azure è abilitata la disponibilità Premium, progettata per carichi di lavoro elevati che non tollerano un impatto sulle prestazioni dovuto alle operazioni di manutenzione continua.

Nel modello Premium il database SQL di Azure integra calcolo e archiviazione nel singolo nodo. La disponibilità elevata in questo modello di architettura è ottenuta dalla replica di archiviazione (unità SSD collegata al computer locale) e calcolo (processo del motore di database SQL Server) distribuita in cluster con quattro nodi mediante una tecnologia simile ai [gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server.

![Cluster di nodi di motore di database](media/sql-database-managed-instance/business-critical-service-tier.png)

Sia il processo del motore di database SQL che i file mdf/ldf sottostanti vengono posizionati nello stesso nodo con una risorsa di archiviazione SSD collegata in locale che offre bassa latenza al carico di lavoro. La disponibilità elevata è implementata mediante una tecnologia simile a [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) di SQL Server. Ogni database è un cluster di nodi del database con un database primario accessibile per il carico di lavoro del cliente e tre processi secondari contenenti le copie dei dati. Il nodo primario esegue il push costante delle modifiche ai nodi secondari per garantire che i dati siano disponibili nelle repliche secondarie se il nodo primario si arresta per un qualsiasi motivo. Il failover viene gestito dal motore di database di SQL Server: una replica secondaria diventa il nodo primario e viene creata una nuova replica secondaria per garantire un numero sufficiente di nodi nel cluster. Il carico di lavoro viene reindirizzato automaticamente al nuovo nodo primario.

Inoltre, nel cluster business critical è integrata una funzionalità di [scalabilità in lettura](sql-database-read-scale-out.md) che fornisce un nodo di sola lettura integrato e gratuito che può essere usato per l'esecuzione di query di sola lettura (ad esempio report) che non dovrebbero incidere sulle prestazioni del carico di lavoro primario.

## <a name="when-to-choose-this-service-tier"></a>Quando scegliere questo livello di servizio

Il livello di servizio Business critical è progettato per le applicazioni che richiedono risposte a bassa latenza dalla risorsa di archiviazione SSD sottostante (in media 1-2 ms) e ripristino rapido in caso di errore dell'infrastruttura sottostante o che devono delegare report, analisi e query di sola lettura alla replica secondaria leggibile gratuita del database primario.

I motivi principali per cui è consigliabile scegliere il livello di servizio Business Critical anziché il livello Scopigenerico sono:The key reasons why you should choose Business Critical service tier instead of General Purpose tier are:
-   Requisiti di bassa latenza di I/O: il carico di lavoro che richiede la risposta rapida dal livello di archiviazione (1-2 millisecondi in media) deve usare il livello Business Critical.Low IO latency requirements – workload that needs the fast response from the storage layer (1-2 milliseconds in average) should use Business Critical tier. 
-   Comunicazione frequente tra applicazione e database. L'applicazione che non è in grado di sfruttare la memorizzazione nella cache a livello di applicazione o l'invio in batch di [richieste](sql-database-use-batching-to-improve-performance.md) ed è necessario inviare molte query SQL che devono essere elaborate rapidamente sono buoni candidati per il livello Business Critical.
-   Numero elevato di aggiornamenti: le operazioni di inserimento, aggiornamento ed eliminazione modificano le `CHECKPOINT` pagine di dati in memoria (pagina dirty) che devono essere salvate nei file di dati con il funzionamento. Il potenziale arresto anomalo del motore di database o un failover del database con un numero elevato di pagine dirty potrebbe aumentare il tempo di ripristino nel livello Scopo generale. Usare il livello Business Critical se si dispone di un carico di lavoro che causa numerose modifiche in memoria. 
-   Transazioni a esecuzione prolungata che modificano i dati. Le transazioni aperte per un periodo di tempo più lungo impediscono il troncamento del file di registro che potrebbe aumentare le dimensioni del registro e il numero di file di [registro virtuale (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Un numero elevato di VLF può rallentare il recupero del database dopo il failover.
-   Carico di lavoro con report e query analitiche che possono essere reindirizzate alla replica di sola lettura secondaria gratuita.
- Maggiore resilienza e ripristino più rapido dagli errori. In caso di errore di sistema, il database nell'istanza primaria verrà disabilitato e una delle repliche secondarie diventerà immediatamente un nuovo database primario di lettura/scrittura pronto per elaborare le query. Motore di databaseDatabase Engine non è necessario analizzare e rieseguire le transazioni dal file di log e caricare tutti i dati nel buffer di memoria.
- Protezione avanzata dal danneggiamento dei dati: il livello Business Critical sfrutta le repliche di database dietro le quinte per scopi di continuità aziendale e pertanto il servizio sfrutta anche il ripristino automatico delle pagine, che è la stessa tecnologia utilizzata per il mirroring del database di SQL Server e i gruppi di [disponibilità.](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) Nel caso in cui una replica non possa leggere una pagina a causa di un problema di integrità dei dati, verrà recuperata una nuova copia della pagina da un'altra replica, sostituendo la pagina illeggibile senza perdita di dati o tempi di inattività del cliente. Questa funzionalità è applicabile nel livello Scopo generale se il database dispone di replica geo-secondaria.
- Disponibilità più elevata: il livello Business Critical nella configurazione Multi-A garantisce una disponibilità del 99,995%, rispetto al 99,99% del livello Di uso generale.
- Ripristino geografico rapido: il livello Business Critical configurato con la replica geografica ha un obiettivo del punto di ripristino (RPO) garantito pari a 5 sec e un obiettivo del tempo di ripristino (RTO) di 30 secondi per il 100% delle ore distribuite.

## <a name="next-steps"></a>Passaggi successivi

- Trovare le caratteristiche delle risorse (numero di core, I/O, memoria) del livello Business Critical [nell'istanza gestita](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), database singolo nel [modello vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) o [modello DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)o Pool elastico nel [modello vCore](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) e [nel modello DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Informazioni sui livelli [Utilizzo generico](sql-database-service-tier-general-purpose.md) e [Hyperscale](sql-database-service-tier-hyperscale.md).
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md).
- Per altre opzioni per la disponibilità elevata e il ripristino di emergenza, vedere [Continuità aziendale](sql-database-business-continuity.md).
