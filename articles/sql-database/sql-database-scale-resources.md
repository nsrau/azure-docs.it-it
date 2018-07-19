---
title: Ridimensionare le risorse del Database SQL di Azure | Microsoft Docs
description: Questo articolo spiega come ridimensionare il database aggiungendo o eliminando le risorse allocate.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/07/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: f55ce511f6ba90c27e149ac90bbd2c8aa0b3c742
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2018
ms.locfileid: "37923439"
---
# <a name="scale-database-resources"></a>Ridimensionare le risorse di database

Database SQL di Azure consente di aggiungere dinamicamente altre risorse al database con tempi di inattività minimi.

## <a name="overview"></a>Panoramica

Quando la domanda per la tua app cresce da qualche dispositivo e qualche cliente al milione di richieste, il database Azure SQL viene ridimensionato immediatamente, con tempi di inattività minimi. La scalabilità è una delle caratteristiche più importanti del modello PaaS che consente di aggiungere in modo dinamico altre risorse al servizio quando necessario. Database SQL di Azure consente di modificare facilmente le risorse allocate per i database (potenza della CPU, memoria, velocità effettiva IO e storage).  
È possibile limitare i problemi di prestazione, causati da un maggiore utilizzo dell'applicazione, che non possono essere risolti utilizzando l'indicizzazione o con i metodi di query. L'aggiunta di altre risorse consente di una reazione rapida quando il database raggiunge i limiti delle risorse correnti e richiede più energia per gestire il carico di lavoro in ingresso. Database SQL di Azure consente inoltre di ridurre le risorse non necessarie all'abbassamento dei costi.
Non è necessario acquistare un hardware o modificare o l'infrastruttura di base. Il ridimensionamento del database può essere eseguito con facilità tramite il portale di Azure usando un dispositivo di scorrimento.

![Prestazioni del ridimensionamento del database](media/sql-database-scalability/scale-performance.svg)

Il database Azure SQL offre un [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o il [modello di acquisto basato su vCore (anteprima)](sql-database-service-tiers-vcore.md). 
-   Il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) offre un insieme di risorse di calcolo, memoria e risorse IO in tre livelli di servizio per supportare carichi di lavoro di database da leggeri a pesanti: Basic, Standard e Premium. I livelli delle prestazioni di ogni livello forniscono una diversa combinazione di queste risorse, a cui è possibile aggiungere altre risorse di archiviazione.
-   Il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md) (anteprima) consente di scegliere il numero di vCore, la capacità di memoria, la velocità e capacità di archiviazione.
È possibile creare la prima app in un singolo database di piccole dimensioni per un costo mensile conveniente e quindi modificare il livello di servizio manualmente o a livello di codice in qualsiasi momento per soddisfare le esigenze della soluzione. È possibile regolare le prestazioni senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispettare i requisiti in continua evoluzione relativi alle risorse e di pagare solo le risorse necessarie quando necessario.


> [!NOTE]
> La scalabilità dinamica è diversa dalla scalabilità automatica. Con la scalabilità automatica, un servizio viene ridimensionato automaticamente in base a vari criteri, mentre la scalabilità dinamica consente di gestire manualmente il ridimensionamento senza tempi di inattività.
>


Un singolo database SQL di Azure supporta la scalabilità dinamica manuale, ma non la scalabilità automatica. Per un'esperienza più *automatica*, valutare la possibilità di usare i pool elastici, che consentono ai database di condividere le risorse in un pool in base alle esigenze dei singoli database.
Esistono comunque script utili per automatizzare la scalabilità per un singolo database SQL di Azure. Per un esempio, vedere [Usare PowerShell per monitorare e ridimensionare un singolo database SQL](scripts/sql-database-monitor-and-scale-database-powershell.md).


Tutte le tre versioni di Database SQL di Azure offrono la capacità di ridimensionare in modo dinamico i database:
-   Nel [Database singolo di Azure SQL](sql-database-single-database-scale.md) è possibile usare sia i modelli [DTU](sql-database-dtu-resource-limits-single-databases.md) o [vCore](sql-database-vcore-resource-limits-single-databases.md) per definire la quantità massima di risorse che verranno assegnate a ogni database.
-   [Istanza gestita di Azure SQL](sql-database-managed-instance.md) usa la modalità [Vcore](/azure/sql-database/sql-database-managed-instance#vcore-based-purchasing-model-preview) e consente di definire il massimo di core CPU e il massimo della memoria allocata per l'istanza. Tutti i database all'interno dell'istanza condivideranno le risorse allocate per l'istanza.
-   [Pool elastici di SQL Azure](sql-database-elastic-pool-scale.md) consentono di definire il limite massimo di risorse per ogni gruppo di database nel pool.

## <a name="alternative-scale-methods"></a>Metodi alternativi di scalabilità
Le risorse di ridimensionamento sono il modo più semplice ed efficace per migliorare le prestazioni del database senza modificare il codice del database o dell'applicazione.
In alcuni casi, anche i livelli di prestazioni più elevati e l'ottimizzazione delle prestazioni potrebbero non gestire il carico di lavoro in modo efficace e conveniente. In questi casi sono disponibili altre opzioni per ridimensionare il database:
-   La [Scalabilità in lettura](sql-database-read-scale-out.md) è una funzionalità che consente di ottenere una replica di sola lettura dei dati e per la quale è possibile eseguire query di sola lettura, come ad esempio report complessi. Replica di sola lettura gestirà il carico di lavoro di sola lettura senza influire sull'utilizzo delle risorse nel database primario.
-   Il [Partizionamento di database](sql-database-elastic-scale-introduction.md) è un set di tecniche che consente di dividere i dati in diversi database e di ridimensionarli in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come migliorare le prestazioni del database tramite la modifica del codice del database, vedere [Trovare e applicare raccomandazioni per le prestazioni](sql-database-advisor-portal.md).
- Per informazioni su come consentire alle intelligence integrate nel database di ottimizzare il database, vedere [Ottimizzazione automatica](sql-database-automatic-tuning.md).
- Per informazioni sulla Scalabilità in lettura nel servizio Database SQL di Azure, vedere come [Usare le repliche di sola lettura per bilanciare i carichi di lavoro di query di sola lettura](sql-database-read-scale-out.md).
- Per informazioni sul Partizionamento del database, vedere [Ridimensionamento con Azure SQL Database](sql-database-elastic-scale-introduction.md).

