---
title: Livello di servizio business critical
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Informazioni sul livello di servizio business critical per database SQL di Azure e Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 0067811316a8afd26828050d81215ecb5748c841
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986692"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Livello business critical: database SQL di Azure e Azure SQL Istanza gestita 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Il livello business critical è denominato Premium nel modello di acquisto DTU. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](purchasing-models.md).

Il database SQL di Azure e Istanza gestita SQL di Azure sono entrambi basati su SQL Server architettura del motore di database che viene adattata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Sono disponibili tre modelli di architettura:
- Utilizzo generico/Standard 
- Business critical/Premium
- Hyperscale

Il modello di livello di servizio Premium/Business critical è basato su un cluster di processi del motore di database. Questo modello di architettura si basa sul fatto che c’è sempre un quorum di nodi di motore di database disponibili e ha un impatto minimo sulle prestazioni sul carico di lavoro anche durante le attività di manutenzione. Il livello di servizio iperscalabile è attualmente disponibile solo per il database SQL di Azure (non per SQL Istanza gestita) ed è un livello di prestazioni di archiviazione e calcolo altamente scalabile che sfrutta l'architettura di Azure per scalare le risorse di archiviazione e di calcolo per un database nel database SQL di Azure in modo sostanziale oltre i limiti disponibili per i livelli di servizio per utilizzo generico e business critical.

Aggiornamenti e patch di Azure sottostanti sistema operativo, driver e SQL Server motore di database in modo trasparente con il tempo di inattività minimo per gli utenti finali. 

La disponibilità Premium è abilitata nei livelli di servizio Premium e business critical ed è progettata per carichi di lavoro intensivi che non possono tollerare alcun effetto sulle prestazioni a causa delle operazioni di manutenzione in corso.

Il calcolo e l'archiviazione sono integrati nel singolo nodo del modello Premium. La disponibilità elevata in questo modello di architettura viene eseguita tramite la replica di calcolo (SQL Server processo del motore di database) e l'archiviazione (SSD collegata localmente) distribuita in un cluster a quattro nodi, usando una tecnologia simile a SQL Server [gruppi di disponibilità always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster di nodi di motore di database](./media/service-tier-business-critical/business-critical-service-tier.png)

Sia il processo del motore di database SQL Server che i file con estensione MDF/ldf sottostanti sono posizionati nello stesso nodo con archiviazione SSD collegata localmente che fornisce bassa latenza al carico di lavoro. La disponibilità elevata viene implementata utilizzando una tecnologia simile a SQL Server [gruppi di disponibilità always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Ogni database è un cluster di nodi di database con un database primario accessibile per i carichi di lavoro dei clienti e tre processi secondari contenenti copie di dati. Il nodo primario esegue costantemente il push delle modifiche nei nodi secondari per assicurarsi che i dati siano disponibili sulle repliche secondarie in caso di errore del nodo primario per qualsiasi motivo. Il failover viene gestito dal motore di database SQL Server: una replica secondaria diventa il nodo primario e viene creata una nuova replica secondaria per verificare che nel cluster siano presenti nodi sufficienti. Il carico di lavoro viene reindirizzato automaticamente al nuovo nodo primario.

Inoltre, nel cluster business critical è integrata una funzionalità di [scalabilità in lettura](read-scale-out.md) che fornisce un nodo di sola lettura integrato e gratuito che può essere usato per l'esecuzione di query di sola lettura (ad esempio report) che non dovrebbero incidere sulle prestazioni del carico di lavoro primario.

## <a name="when-to-choose-this-service-tier"></a>Quando scegliere questo livello di servizio

Business critical livello di servizio è progettato per le applicazioni che richiedono risposte a bassa latenza dalla risorsa di archiviazione SSD sottostante (1-2 ms in media), un ripristino rapido in caso di errore dell'infrastruttura sottostante o la necessità di non caricare report, analisi e query di sola lettura nella replica secondaria leggibile gratuita del database primario.

I motivi principali per cui è consigliabile scegliere business critical livello di servizio anziché per utilizzo generico livello sono:
-   **Requisiti di latenza di I/O Bassi** : i carichi di lavoro che necessitano di una risposta rapida dal livello di archiviazione (1-2 millisecondi in media) devono usare business critical livello. 
-   **Comunicazione frequente tra l'applicazione e il database**. Le applicazioni che non possono sfruttare la memorizzazione nella cache a livello di applicazione o la richiesta di invio in [batch](../performance-improve-use-batching.md) e devono inviare molte query SQL che devono essere elaborate rapidamente sono candidati ottimali per il livello business critical.
-   Un **numero elevato di aggiornamenti** : le operazioni di inserimento, aggiornamento ed eliminazione consentono di modificare le pagine di dati in memoria (pagina dirty) che devono essere salvate nei file di dati con l' `CHECKPOINT` operazione. Un potenziale arresto anomalo del processo del motore di database o un failover del database con un numero elevato di pagine dirty potrebbe aumentare il tempo di recupero nel livello per utilizzo generico. Usare business critical livello se si ha un carico di lavoro che causa molte modifiche in memoria. 
-   **Transazioni con esecuzione prolungata che modificano i dati**. Le transazioni aperte per un periodo di tempo più lungo impediscono il troncamento dei file di log, che potrebbero aumentare le dimensioni del log e il numero di [file di log virtuali (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Un numero elevato di VLF può rallentare il recupero del database dopo il failover.
-   **Carico di lavoro con query di Reporting e analisi** che possono essere reindirizzate alla replica secondaria di sola lettura gratuita.
- **Maggiore resilienza e ripristino più rapido dagli errori**. In caso di errore di sistema, il database nell'istanza primaria verrà disabilitato e una delle repliche secondarie diventerà immediatamente un nuovo database primario di lettura/scrittura pronto per l'elaborazione delle query. Il motore di database non deve analizzare e ripristinare le transazioni dal file di log e caricare tutti i dati nel buffer di memoria.
- **Protezione avanzata per il danneggiamento dei dati**. Business critical livello sfrutta le repliche di database in background per la continuità aziendale, quindi il servizio utilizza anche la correzione automatica della pagina, che è la stessa tecnologia utilizzata per SQL Server il [mirroring](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)del database e i gruppi di disponibilità. Se una replica non è in grado di leggere una pagina a causa di un problema di integrità dei dati, una copia aggiornata della pagina verrà recuperata da un'altra replica, sostituendo la pagina illeggibile senza perdita di dati o tempi di inattività del cliente. Questa funzionalità è applicabile nel livello per utilizzo generico se il database ha una replica geografica secondaria.
- **Maggiore disponibilità** : il livello business critical nella configurazione multiaz garantisce una disponibilità del 99,995% rispetto al 99,99% del livello per utilizzo generico.
- **Ripristino geografico rapido** : il livello di business critical configurato con la replica geografica ha un obiettivo del punto di ripristino (RPO) garantito di 5 secondi e obiettivo del tempo di ripristino (RTO) di 30 secondi per il 100% delle ore distribuite.

## <a name="next-steps"></a>Passaggi successivi

- Trovare le caratteristiche delle risorse (numero di core, I/O, memoria) del livello business critical in [SQL istanza gestita](../managed-instance/resource-limits.md#service-tier-characteristics), database singolo nel modello [Vcore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) o [modello DTU](resource-limits-dtu-single-databases.md#premium-service-tier)o pool elastico nel modello [vCore](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) e [modello DTU](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits).
- Informazioni sui livelli [Utilizzo generico](service-tier-general-purpose.md) e [Hyperscale](service-tier-hyperscale.md).
- Informazioni sulle [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Per altre opzioni per la disponibilità elevata e il ripristino di emergenza, vedere [continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md).
