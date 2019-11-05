---
title: Livello Business critical - Servizio di database SQL di Azure | Microsoft Docs
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
ms.openlocfilehash: ef1f420e4c4dbd38ad703eb0573fae36af078edb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496226"
---
# <a name="business-critical-tier---azure-sql-database"></a>Livello Business critical - Servizio di database SQL di Azure

> [!NOTE]
> Il livello Business critical è chiamato Premium nel modello di acquisto basato su DTU. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-purchase-models.md).

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli di architettura usati nel database SQL di Azure:
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

I motivi principali per cui è consigliabile scegliere business critical livello di servizio anziché per utilizzo generico livello sono:
-   Requisiti di latenza IO Bassi: il carico di lavoro che richiede la risposta rapida dal livello di archiviazione (1-2 millisecondi in media) deve usare business critical livello. 
-   Comunicazione frequente tra l'applicazione e il database. Un'applicazione che non può sfruttare la memorizzazione nella cache a livello di applicazione o la richiesta di invio in [batch](sql-database-use-batching-to-improve-performance.md) e la necessità di inviare molte query SQL che devono essere elaborate rapidamente sono candidati ottimali per business critical livello.
-   Un numero elevato di aggiornamenti: le operazioni di inserimento, aggiornamento ed eliminazione consentono di modificare le pagine di dati in memoria (pagina dirty) che devono essere salvate nei file di dati con `CHECKPOINT` operazione. Un potenziale arresto anomalo del processo del motore di database o un failover del database con un numero elevato di pagine dirty potrebbe aumentare il tempo di recupero nel livello per utilizzo generico. Usare business critical livello se si ha un carico di lavoro che causa molte modifiche in memoria. 
-   Transazioni con esecuzione prolungata che modificano i dati. Le transazioni aperte per un periodo di tempo più lungo impediscono il troncamento del file di log che potrebbe aumentare le dimensioni del log e il numero di [file di log virtuali (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Un numero elevato di VLF può rallentare il recupero del database dopo il failover.
-   Carico di lavoro con query di Reporting e analisi che possono essere reindirizzate alla replica secondaria di sola lettura gratuita.
- Maggiore resilienza e ripristino più rapido dagli errori. In caso di errore di sistema, il database nell'istanza primaria verrà disabilitato e una delle repliche secondarie diventerà immediatamente un nuovo database primario di lettura/scrittura pronto per l'elaborazione delle query. Il motore di database non deve analizzare e ripristinare le transazioni dal file di log e caricare tutti i dati nel buffer di memoria.
- Protezione avanzata per il danneggiamento dei dati: il livello business critical sfrutta le repliche di database in background per la continuità aziendale, quindi il servizio utilizza anche la correzione automatica della pagina, che è la stessa tecnologia utilizzata per SQL Server database [mirroring e gruppi di disponibilità](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). Se una replica non è in grado di leggere una pagina a causa di un problema di integrità dei dati, una copia aggiornata della pagina verrà recuperata da un'altra replica, sostituendo la pagina illeggibile senza perdita di dati o tempi di inattività del cliente. Questa funzionalità è applicabile nel livello per utilizzo generico se il database ha una replica geografica secondaria.
- Maggiore disponibilità: il livello business critical nella configurazione multiaz garantisce una disponibilità del 99,995% rispetto al 99,99% del livello per utilizzo generico.
- Ripristino geografico rapido: il livello di business critical configurato con la replica geografica ha un obiettivo del punto di ripristino (RPO) garantito di 5 secondi e obiettivo del tempo di ripristino (RTO) di 30 secondi per il 100% delle ore distribuite.

## <a name="next-steps"></a>Passaggi successivi

- Trovare le caratteristiche delle risorse (numero di core, i/o, memoria) del livello business critical in [istanza gestita](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), database singolo nel modello [Vcore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) o [modello DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)o pool elastico nel modello [vCore](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) e [modello DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Informazioni sui livelli [Utilizzo generico](sql-database-service-tier-general-purpose.md) e [Hyperscale](sql-database-service-tier-hyperscale.md).
- Informazioni su [Service Fabric](../service-fabric/service-fabric-overview.md).
- Per altre opzioni relative a disponibilità elevata e ripristino di emergenza, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
