---
title: Ridimensionare le risorse del Database SQL di Azure | Microsoft Docs
description: Questo articolo spiega come ridimensionare il database aggiungendo o eliminando le risorse allocate.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: cd0653cf1920bd62621b89410b8cd2de2570fae3
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162918"
---
# <a name="scale-database-resources"></a>Ridimensionare le risorse di database

Database SQL di Azure consente di aggiungere dinamicamente altre risorse al database con tempi di inattività minimi.

## <a name="overview"></a>Panoramica

Quando la domanda per la tua app cresce da qualche dispositivo e qualche cliente al milione di richieste, il database Azure SQL viene ridimensionato immediatamente, con tempi di inattività minimi. La scalabilità è una delle caratteristiche più importanti del modello PaaS che consente di aggiungere in modo dinamico altre risorse al servizio quando necessario. Database SQL di Azure consente di modificare facilmente le risorse allocate per i database (potenza della CPU, memoria, velocità effettiva IO e storage).  
È possibile limitare i problemi di prestazione, causati da un maggiore utilizzo dell'applicazione, che non possono essere risolti utilizzando l'indicizzazione o con i metodi di query. L'aggiunta di altre risorse consente di una reazione rapida quando il database raggiunge i limiti delle risorse correnti e richiede più energia per gestire il carico di lavoro in ingresso. Database SQL di Azure consente inoltre di ridurre le risorse non necessarie all'abbassamento dei costi.
Non è necessario acquistare un hardware o modificare o l'infrastruttura di base. Il ridimensionamento del database può essere eseguito con facilità tramite il portale di Azure usando un dispositivo di scorrimento.

![Prestazioni del ridimensionamento del database](media/sql-database-scalability/scale-performance.svg)

Il database Azure SQL offre un [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md). 
-   Il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) offre un insieme di risorse di calcolo, memoria e risorse IO in tre livelli di servizio per supportare carichi di lavoro di database da leggeri a pesanti: Basic, Standard e Premium. I livelli delle prestazioni di ogni livello forniscono una diversa combinazione di queste risorse, a cui è possibile aggiungere altre risorse di archiviazione.
-   Il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md) consente di scegliere il numero di vCore, la quantità di memoria e la quantità e la velocità della risorsa di archiviazione. Questo modello di acquisto offre tre livelli di servizio: per utilizzo generico, business critical e con iperscalabilità (anteprima).
È possibile creare la prima app in un singolo database di piccole dimensioni a un costo mensile contenuto nel livello di servizio per utilizzo generico e quindi modificare il livello di servizio manualmente o a livello di codice in qualsiasi momento passando al livello di servizio business critical per soddisfare le proprie esigenze. È possibile regolare le prestazioni senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispettare i requisiti in continua evoluzione relativi alle risorse e di pagare solo le risorse necessarie quando necessario.

> [!IMPORTANT]
> Non è possibile passare da un livello di servizio per utilizzo generico o business critical a un livello di servizio con iperscalabilità. È tuttavia possibile modificare i livelli di prestazioni all'interno del livello di servizio con iperscalabilità.

> [!NOTE]
> La scalabilità dinamica è diversa dalla scalabilità automatica. Con la scalabilità automatica, un servizio viene ridimensionato automaticamente in base a vari criteri, mentre la scalabilità dinamica consente di gestire manualmente il ridimensionamento senza tempi di inattività.

Un singolo database SQL di Azure supporta la scalabilità dinamica manuale, ma non la scalabilità automatica. Per un'esperienza più *automatica*, valutare la possibilità di usare i pool elastici, che consentono ai database di condividere le risorse in un pool in base alle esigenze dei singoli database.
Esistono comunque script utili per automatizzare la scalabilità per un singolo database SQL di Azure. Per un esempio, vedere [Usare PowerShell per monitorare e ridimensionare un singolo database SQL](scripts/sql-database-monitor-and-scale-database-powershell.md).

È possibile modificare i [livelli di servizio delle DTU](sql-database-service-tiers-dtu.md) o le [caratteristiche vCore](sql-database-vcore-resource-limits-single-databases.md) in qualsiasi momento con tempi di inattività minimi per l'applicazione, in genere meno di 4 secondi. Per molte aziende e app, la possibilità di creare database e connettere o disconnettere prestazioni su richiesta è sufficiente, specialmente se i modelli d'uso sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. Per questo scenario usare un pool elastico con un determinato numero di eDTU condivise tra più database nel pool.

![Introduzione al database SQL: DTU di database singolo in base al livello](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Tutte le tre versioni di Database SQL di Azure offrono la capacità di ridimensionare in modo dinamico i database:
-   Nel [Database singolo di Azure SQL](sql-database-single-database-scale.md) è possibile usare sia i modelli [DTU](sql-database-dtu-resource-limits-single-databases.md) o [vCore](sql-database-vcore-resource-limits-single-databases.md) per definire la quantità massima di risorse che verranno assegnate a ogni database.
-   [Istanza gestita di Azure SQL](sql-database-managed-instance.md) usa la modalità [Vcore](sql-database-managed-instance.md#vcore-based-purchasing-model) e consente di definire il massimo di core CPU e il massimo della memoria allocata per l'istanza. Tutti i database all'interno dell'istanza condivideranno le risorse allocate per l'istanza.
-   [Pool elastici di SQL Azure](sql-database-elastic-pool-scale.md) consentono di definire il limite massimo di risorse per ogni gruppo di database nel pool.

## <a name="alternative-scale-methods"></a>Metodi alternativi di scalabilità
Le risorse di ridimensionamento sono il modo più semplice ed efficace per migliorare le prestazioni del database senza modificare il codice del database o dell'applicazione. In alcuni casi, anche i livelli di servizio, le dimensioni di calcolo e le ottimizzazioni delle prestazioni più elevati potrebbero non riuscire a gestire il carico di lavoro in modo corretto e conveniente. In questi casi si dispone delle seguenti opzioni aggiuntive per ridimensionare il database:
-   La [Scalabilità in lettura](sql-database-read-scale-out.md) è una funzionalità che consente di ottenere una replica di sola lettura dei dati e per la quale è possibile eseguire query di sola lettura, come ad esempio report complessi. Replica di sola lettura gestirà il carico di lavoro di sola lettura senza influire sull'utilizzo delle risorse nel database primario.
-   Il [Partizionamento di database](sql-database-elastic-scale-introduction.md) è un set di tecniche che consente di dividere i dati in diversi database e di ridimensionarli in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come migliorare le prestazioni del database tramite la modifica del codice del database, vedere [Trovare e applicare raccomandazioni per le prestazioni](sql-database-advisor-portal.md).
- Per informazioni su come consentire alle intelligence integrate nel database di ottimizzare il database, vedere [Ottimizzazione automatica](sql-database-automatic-tuning.md).
- Per informazioni sulla Scalabilità in lettura nel servizio Database SQL di Azure, vedere come [Usare le repliche di sola lettura per bilanciare i carichi di lavoro di query di sola lettura](sql-database-read-scale-out.md).
- Per informazioni sul Partizionamento del database, vedere [Ridimensionamento con Azure SQL Database](sql-database-elastic-scale-introduction.md).

