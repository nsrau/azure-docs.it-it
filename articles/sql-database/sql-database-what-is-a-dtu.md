<properties
	pageTitle="Database SQL: Informazioni sulle DTU | Microsoft Azure"
	description="Informazioni sulle unità di transazione di database SQL di Azure."
	keywords="opzioni di database,prestazioni del database"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor="CarlRabeler"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="NA"
	ms.date="09/06/2016"
	ms.author="carlrab"/>  

# Unità di transazione di database (DTU) e unità di transazione di database elastico (eDTU)

Questo articolo illustra le unità di transazione di database (DTU) e le unità di transazione di database elastico (eDTU) e cosa accade quando si raggiunge il numero massimo di DTU o eDTU.

## Informazioni sulle unità di transazione di database (DTU)

Una DTU è un'unità di misura delle risorse garantite come disponibili per un database SQL di Azure autonomo a un livello di prestazioni specifico all'interno di un [livello di servizio per database autonomi](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels). Una DTU è una misura combinata di CPU, memoria e I/O dei dati e del log delle transazioni in un rapporto determinato da un carico di lavoro di benchmark OLTP progettato per essere tipico di carichi di lavoro OLTP reali. Raddoppiare le DTU aumentando il livello di prestazioni di un database equivale a raddoppiare il set di risorse disponibili per quel database. Ad esempio, un database Premium P11 con 1750 DTU fornisce 350 volte più potenza di calcolo DTU di un database Basic con 5 DTU. Per comprendere la metodologia alla base del carico di lavoro di benchmark OLTP usato per determinare la combinazione di valori per la DTU, vedere [Informazioni generali sul benchmark del database SQL di Azure](sql-database-benchmark-overview.md).

![Introduzione al database SQL: DTU di database singolo in base al livello](./media/sql-database-what-is-a-dtu/single_db_dtus.png)  

È possibile [modificare i livelli di servizio](sql-database-scale-up.md) in qualsiasi momento con tempi di inattività minimi per l'applicazione, in genere meno di 4 secondi. Per molte aziende e app, la possibilità di creare database e connettere o disconnettere prestazioni del database singolo su richiesta è sufficiente, specialmente se i modelli di utilizzo sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. Per questo scenario verrà usato un pool elastico con un determinato numero di eDTU.

## Informazioni sulle unità di transazione di database elastico (eDTU)

Una eDTU è un'unità di misura del set di risorse (DTU) che può essere condiviso all'interno di un set di database in un server SQL di Azure, denominato [pool elastico](sql-database-elastic-pool.png). I pool elastici offrono una soluzione semplice e conveniente per gestire gli obiettivi di prestazioni per più database con modelli di utilizzo estremamente mutevoli e imprevedibili. Vedere [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) per altre informazioni.

![Introduzione al database SQL: eDTU in base al livello](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)  

A un pool viene assegnato un numero definito di eDTU per un prezzo prestabilito. All'interno del pool i singoli database sono sufficientemente flessibili da assicurare una scalabilità automatica nell'ambito di parametri prefissati. Se il carico di lavoro è importante, un database può utilizzare più eDTU per soddisfare la domanda. Se invece il carico di lavoro è più leggero, i database in assenza di carico non utilizzano gli eDTU. La possibilità di effettuare il provisioning delle risorse per l'intero pool e non per i singoli database semplifica le attività di gestione. Il budget del pool, inoltre, è facilmente prevedibile.

È possibile aggiungere ulteriori eDTU a un pool esistente senza causare tempi di inattività del database o effetti negativi sui database nel pool elastico. Analogamente, se gli eDTU aggiuntivi non sono più necessari, è possibile rimuoverli da un pool esistente in qualsiasi momento. È possibile aggiungere e rimuovere database dal pool. Se si prevede che un database sottoutilizzerà le proprie risorse, è possibile rimuoverlo.

## Come si determina il numero di DTU necessarie per il carico di lavoro?

Se si intende eseguire la migrazione di un carico di lavoro locale o di un carico di lavoro di macchine virtuali di SQL Server a un database SQL di Azure, è possibile usare lo [strumento di calcolo DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessarie. Per un carico di lavoro esistente del database SQL di Azure, è possibile usare [Informazioni dettagliate prestazioni query del database SQL](sql-database-query-performance.md) per comprendere il consumo di risorse di database (DTU) e ottenere altri dati per l'ottimizzazione del carico di lavoro. È anche possibile usare la DMV [sys.dm\_db\_ resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) per ottenere informazioni sul consumo di risorse per l'ultima ora. In alternativa è possibile eseguire query sulla vista del catalogo [sys.resource\_stats](http://msdn.microsoft.com/library/dn269979.aspx) per ottenere gli stessi dati per gli ultimi 14 giorni, anche se a una fedeltà inferiore di medie di cinque minuti.

## Come è possibile sapere se un pool elastico di risorse può essere utile?

I pool sono adatti per un numero elevato di database con modelli di utilizzo specifici. Per un determinato database, questo modello è caratterizzato da un utilizzo medio ridotto con picchi di utilizzo relativamente poco frequenti. Database SQL valuta automaticamente la cronologia d’utilizzo delle risorse dei database in un server di database SQL esistente e consiglia una configurazione appropriata del pool nel portale di Azure. Per altre informazioni, vedere [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md).

## Cosa succede se si raggiunge il numero massimo di DTU

I livelli di prestazioni vengono calibrati e gestiti per offrire le risorse necessarie per eseguire il carico di lavoro del database fino ai limiti massimi consentiti per il livello di servizio/livello di prestazioni selezionato. Se il carico di lavoro raggiunge uno dei limiti di CPU/IO dati/IO di log, si continuerà a ricevere le risorse al massimo livello consentito, ma probabilmente si noterà un aumento delle latenze per le query. Con questi limiti non si verificheranno errori, ma solo un rallentamento nel carico di lavoro, a meno che il rallentamento non diventi così grave da provocare il timeout delle query. Se si raggiungono i limiti relativi al numero massimo di richieste o di sessioni utente simultanee (thread di lavoro), verranno visualizzati errori espliciti. Vedere [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md) per informazioni sui limiti di risorse diverse da CPU, memoria, I/O di dati e I/O del log delle transazioni.

## Passaggi successivi

- Vedere [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md) per informazioni sulle DTU ed eDTU disponibili per database autonomi e pool elastici.
- Vedere [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md) per informazioni sui limiti di risorse diverse da CPU, memoria, I/O di dati e I/O del log delle transazioni.
- Vedere [Informazioni dettagliate prestazioni query del database SQL di Azure](sql-database-query-performance.md) per informazioni sul consumo di DTU.
- Vedere [Informazioni generali sul benchmark del database SQL di Azure](sql-database-benchmark-overview.md) per comprendere la metodologia alla base del carico di lavoro di benchmark OLTP usato per determinare la combinazione di valori per la DTU.

<!---HONumber=AcomDC_0914_2016-->