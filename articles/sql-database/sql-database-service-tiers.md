<properties
	pageTitle="Opzioni e prestazioni del database SQL: livelli di servizio | Microsoft Azure"
	description="Confrontare le prestazioni e le funzionalità di continuità aziendale dei vari livelli di servizio del database SQL per trovare il giusto compromesso tra costi e funzionalità a sostegno della scalabilità."
	keywords="opzioni di database,prestazioni del database"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="03/25/2016"
	ms.author="carlrab"/>

# Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL

[Il database SQL di Azure](sql-database-technical-overview.md) include più livelli di servizio per gestire diversi di carichi di lavoro. È possibile modificare i livelli di servizio in qualsiasi momento senza tempo di inattività per l'applicazione. È anche possibile [creare un database singolo](sql-database-get-started.md) con caratteristiche e prezzi definiti. Oppure è possibile gestire tutti i database [creando un pool di database elastici](sql-database-elastic-pool-create-portal.md). In entrambi i casi, tra le opzioni incluse vi sono le edizioni **Basic**, **Standard**, e **Premium**. Le opzioni di database in questi livelli sono simili per singoli database e pool elastici, ma esistono altre considerazioni per i pool elastici. Questo articolo fornisce i dettagli dei livelli di servizio per singoli database e database elastici.

## Livelli di servizio e opzioni di database
I livelli di servizio Basic, Standard e Premium garantiscono un tempo di attività previsto dal contratto di servizio del 99,99% e offrono prestazioni prevedibili, opzioni di continuità aziendale flessibili, funzionalità di sicurezza e fatturazione su base oraria. Nella tabella seguente sono riportati esempi dei livelli ottimali adatti ai carichi di lavoro di diverse applicazioni.

| Livello di servizio | Carichi di lavoro di destinazione |
|---|---|
| **Basic** | Più adatto per un database di piccole dimensioni, che supporta in genere una singola operazione attiva in un determinato momento. Ad esempio, database usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| **Standard** | Opzione più adatta per la maggior parte delle applicazioni cloud, che supporta più query contemporaneamente. Ad esempio, applicazioni web o per gruppi di lavoro. |
| **Premium** | Progettato per elevato volume di transazioni, che supporta un numero elevato di utenti simultanei e richiede il massimo livello di funzionalità di continuità aziendale. Ad esempio, database che supportano applicazioni mission-critical. |

>[AZURE.NOTE] Le edizioni Web e Business sono state ritirate. Se si prevede di continuare a usare le edizioni Web e Business, leggere le [domande frequenti sull'interruzione del servizio](https://azure.microsoft.com/pricing/details/sql-database/web-business/).

## Livelli di servizio e di prestazioni per database singoli
Per singoli database sono disponibili più livelli di prestazioni all'interno di ogni livello di servizio. È possibile scegliere il livello che meglio soddisfa le esigenze del carico di lavoro. Se è necessario aumentare o ridurre le prestazioni, è possibile modificare facilmente i livelli del database **senza tempo di inattività per l'applicazione**. Per informazioni dettagliate, vedere [Modifica dei livelli di servizio e dei livelli di prestazioni di un database](sql-database-scale-up.md).

Le caratteristiche delle prestazioni riportate di seguito si applicano a database creati con [SQL Database V12](sql-database-v12-whats-new.md). In situazioni in cui l'hardware sottostante in Azure ospita più database, il database ottiene comunque un set garantito di risorse e le caratteristiche relative alle prestazioni previste per il database non saranno interessate.

[AZURE.INCLUDE [Tabella dei livelli di servizio del database SQL](../../includes/sql-database-service-tiers-table.md)]

Per una migliore comprensione delle DTU, vedere la [sezione DTU](#understanding-dtus) in questo argomento.

>[AZURE.NOTE] Per una spiegazione dettagliata di tutte le altre righe in questa tabella livelli di servizio, vedere [Limiti e capacità a livello di servizio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## Livelli di servizio e di prestazioni per pool elastici in eDTU
Oltre a creare e a ridimensionare un database singolo, è anche possibile gestire più database all'interno di un [pool elastico](sql-database-elastic-pool.md). Tutti i database in un pool elastico condividono un set comune di risorse. Le caratteristiche delle prestazioni sono misurate dalle *unità di transazione del database elastico* (eDTU). Come con i database singoli, per i pool sono disponibili tre livelli di prestazioni: **Basic**, **Standard** e **Premium**. Per i pool questi tre livelli definiscono i limiti delle prestazioni complessive e diverse funzionalità.

I pool consentono ai database elastici di condividere e utilizzare risorse DTU senza dover assegnare un livello di prestazioni specifico ai database nel pool. Ad esempio, un database singolo in un pool Standard può passare dall'uso di 0 eDTU al valore eDTU massimo per il database impostato quando si configura il pool. In questo modo sono disponibili più database con diversi carichi di lavoro per un utilizzo efficiente delle risorse eDTU disponibili per l'intero pool. Per altre informazioni, vedere [Considerazioni su prezzi e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).

La tabella seguente descrive le caratteristiche dei livelli di servizio del pool.

[AZURE.INCLUDE [Tabella livelli di servizio di database SQL per database elastici](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Ciascun database all'interno di un pool è inoltre conforme alle caratteristiche di database singolo per il livello in questione. Ad esempio, il pool Basic ha un limite per numero massimo di sessioni per pool di 2400 – 28800, ma un singolo database all'interno di tale pool ha un limite di 300 sessioni (limite per un singolo database Basic come specificato nella sezione precedente).

## Informazioni sulle DTU

[AZURE.INCLUDE [Descrizione di DTU del database SQL](../../includes/sql-database-understanding-dtus.md)]

## Passaggi successivi
- Ulteriori informazioni sui prezzi di tali livelli sono disponibili in [Database SQL - Prezzi](https://azure.microsoft.com/pricing/details/sql-database/).
- Altre informazioni su [pool di database elastici](sql-database-elastic-pool-guidance.md) e [Considerazioni su prezzo e prestazioni per un pool database elastici](sql-database-elastic-pool-guidance.md).
- Informazioni su come [Monitorare, gestire e dimensionare un pool di database elastici](sql-database-elastic-pool-manage-portal.md) e [Monitorare le prestazioni di database singoli](sql-database-single-database-monitor.md).
- Dopo aver acquisito familiarità con i livelli del database SQL, provare con un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) per scoprire [come creare il primo database SQL](sql-database-get-started.md).

<!---HONumber=AcomDC_0413_2016-->