---
title: Ridimensionare le risorse
description: Questo articolo illustra come ridimensionare il database nel database SQL di Azure e in SQL Istanza gestita mediante l'aggiunta o la rimozione di risorse allocate.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 180fca9ae40bc07be762665a3d16270e905d2e02
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984148"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Ridimensionare in modo dinamico le risorse di database con tempo di inattività minimo
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Il database SQL di Azure e il Istanza gestita SQL consentono di aggiungere in modo dinamico altre risorse al database con [tempi di inattività](https://azure.microsoft.com/support/legal/sla/sql-database)minimi. Tuttavia, esiste un passaggio rispetto al periodo in cui la connettività viene persa nel database per un breve intervallo di tempo, che può essere mitigato usando la logica di ripetizione dei tentativi.

## <a name="overview"></a>Panoramica

Quando la domanda per la tua app cresce da pochi dispositivi e clienti a milioni, il database SQL di Azure e SQL Istanza gestita scalabilità immediata con tempi di inattività minimi. La scalabilità è una delle caratteristiche più importanti della piattaforma distribuita come servizio (PaaS) che consente di aggiungere in modo dinamico più risorse al servizio quando necessario. Database SQL di Azure consente di modificare facilmente le risorse allocate per i database (potenza della CPU, memoria, velocità effettiva IO e storage).

È possibile limitare i problemi di prestazione, causati da un maggiore utilizzo dell'applicazione, che non possono essere risolti utilizzando l'indicizzazione o con i metodi di query. L'aggiunta di altre risorse consente di una reazione rapida quando il database raggiunge i limiti delle risorse correnti e richiede più energia per gestire il carico di lavoro in ingresso. Database SQL di Azure consente inoltre di ridurre le risorse non necessarie all'abbassamento dei costi.

Non è necessario acquistare un hardware o modificare o l'infrastruttura di base. Il ridimensionamento di un database può essere eseguito facilmente tramite il portale di Azure usando un dispositivo di scorrimento.

![Prestazioni del ridimensionamento del database](./media/scale-resources/scale-performance.svg)

Il database SQL di Azure offre il modello di acquisto [basato su DTU](service-tiers-dtu.md) e il [modello di acquisto basato su Vcore](service-tiers-vcore.md), mentre Azure SQL istanza gestita offre solo il [modello di acquisto basato su vCore](service-tiers-vcore.md). 

- Il [modello di acquisto basato su DTU](service-tiers-dtu.md) offre una combinazione di risorse di calcolo, memoria e I/O in tre livelli di servizio per supportare carichi di lavoro di database leggeri e pesanti: Basic, standard e Premium. I livelli delle prestazioni di ogni livello forniscono una diversa combinazione di queste risorse, a cui è possibile aggiungere altre risorse di archiviazione.
- Il [modello di acquisto basato su vCore](service-tiers-vcore.md) consente di scegliere il numero di vCore, la quantità di memoria e la quantità e la velocità della risorsa di archiviazione. Questo modello di acquisto offre tre livelli di servizio: per utilizzo generico, business critical e iperscalabilità.

È possibile creare la prima app in un singolo database di piccole dimensioni a un costo mensile contenuto nel livello di servizio Basic, Standard o Utilizzo generico e quindi modificare il livello di servizio manualmente o tramite codice in qualsiasi momento passando al livello di servizio Premium o Business Critical in base alle esigenze della soluzione. È possibile regolare le prestazioni senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispettare i requisiti in continua evoluzione relativi alle risorse e di pagare solo le risorse necessarie quando necessario.

> [!NOTE]
> La scalabilità dinamica è diversa dalla scalabilità automatica. La scalabilità automatica è quando un servizio viene ridimensionato automaticamente in base ai criteri, mentre la scalabilità dinamica consente la scalabilità manuale con tempi di inattività minimi.

I database singoli nel database SQL di Azure supportano la scalabilità dinamica manuale, ma non la scalabilità automatica. Per un'esperienza più *automatica*, valutare la possibilità di usare i pool elastici, che consentono ai database di condividere le risorse in un pool in base alle esigenze dei singoli database.
Sono tuttavia disponibili script che consentono di automatizzare la scalabilità per un singolo database nel database SQL di Azure. Per un esempio, vedere [Usare PowerShell per monitorare e ridimensionare un singolo database SQL](scripts/monitor-and-scale-database-powershell.md).

È possibile modificare i [livelli di servizio delle DTU](service-tiers-dtu.md) o le [caratteristiche vCore](resource-limits-vcore-single-databases.md) in qualsiasi momento con tempi di inattività minimi per l'applicazione, in genere meno di 4 secondi. Per molte aziende e app, la possibilità di creare database e connettere o disconnettere prestazioni su richiesta è sufficiente, specialmente se i modelli d'uso sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. Per questo scenario usare un pool elastico con un determinato numero di eDTU condivise tra più database nel pool.

![Introduzione al database SQL: DTU di database singolo in base al livello](./media/scale-resources/single_db_dtus.png)

Il database SQL di Azure offre la possibilità di ridimensionare in modo dinamico i database:

- Con un [Database singolo](single-database-scale.md) è possibile usare sia i modelli [DTU](resource-limits-dtu-single-databases.md) o [vCore](resource-limits-vcore-single-databases.md) per definire la quantità massima di risorse che verranno assegnate a ogni database.
- I [pool elastici](elastic-pool-scale.md) consentono di definire il limite massimo di risorse per ogni gruppo di database nel pool.

Il Istanza gestita SQL di Azure consente di scalare anche: 

- [SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md) usa la modalità [Vcore](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) e consente di definire i core CPU massimi e la quantità massima di spazio di archiviazione allocato per l'istanza. Tutti i database all'interno dell'istanza gestita condividono le risorse allocate all'istanza.

Quando si avvia l'azione di aumento o riduzione delle prestazioni in una qualsiasi delle varianti, il processo del motore di database viene riavviato e spostato in una macchina virtuale diversa, se necessario. Lo sviluppo del processo del motore di database in una nuova macchina virtuale è un **processo online** in cui è possibile continuare a usare il servizio database SQL di Azure esistente mentre il processo è in corso. Una volta che il motore di database di destinazione è stato completamente inizializzato ed è pronto per l'elaborazione delle query, le connessioni verranno [passate dal motore di database di origine a quello di destinazione](single-database-scale.md#impact).

> [!NOTE]
> È possibile che si verifichi un breve intervallo di connessione al termine del processo di scalabilità verticale/orizzontale. Se è stata implementata la [logica di ripetizione dei tentativi per gli errori temporanei standard](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), il failover non sarà noto.

## <a name="alternative-scale-methods"></a>Metodi alternativi di scalabilità

Il ridimensionamento delle risorse è il modo più semplice ed efficace per migliorare le prestazioni del database senza modificare il codice del database o dell'applicazione. In alcuni casi, anche i livelli di servizio più elevati, le dimensioni di calcolo e le ottimizzazioni delle prestazioni potrebbero non gestire il carico di lavoro in modo efficace ed economicamente conveniente. In tal caso sono disponibili opzioni aggiuntive per la scalabilità del database:

- La [scalabilità in lettura](read-scale-out.md) è una funzionalità disponibile che consente di ottenere una replica di sola lettura dei dati in cui è possibile eseguire query di sola lettura complesse, ad esempio i report. Una replica di sola lettura gestirà il carico di lavoro di sola lettura senza influire sull'utilizzo delle risorse nel database primario.
- Il [Partizionamento di database](elastic-scale-introduction.md) è un set di tecniche che consente di dividere i dati in diversi database e di ridimensionarli in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come migliorare le prestazioni del database tramite la modifica del codice del database, vedere [Trovare e applicare raccomandazioni per le prestazioni](database-advisor-find-recommendations-portal.md).
- Per informazioni su come consentire alle intelligence integrate nel database di ottimizzare il database, vedere [Ottimizzazione automatica](automatic-tuning-overview.md).
- Per informazioni sulla scalabilità in lettura nel database SQL di Azure, vedere come [usare le repliche di sola lettura per bilanciare il carico dei carichi di lavoro di query di sola lettura](read-scale-out.md).
- Per informazioni sul Partizionamento del database, vedere [Aumento del numero di istanze con il database SQL di Azure](elastic-scale-introduction.md).
