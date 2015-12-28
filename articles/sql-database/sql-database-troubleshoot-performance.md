<properties
	pageTitle="Risoluzione dei problemi di prestazioni del database in database SQL di Azure."
	description="Azioni rapide per risolvere i problemi di prestazioni del database."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="v-shysun"/>

# Risoluzione dei problemi di prestazioni del database con database SQL di Azure
È possibile modificare il [livello di servizio](sql-database-service-tiers.md) di un singolo database o aumentare il numero di eDTU del pool di database elastici in qualsiasi momento per migliorare le prestazioni, ma è consigliabile innanzitutto identificare le opportunità per migliorare e ottimizzare le prestazioni delle query. Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente.

## Passaggi per valutare e ottimizzare le prestazioni del database
1.	Nel [portale di Azure](https://portal.azure.com) fare clic su **Database SQL**, selezionare il database e quindi usare il grafico di monitoraggio per identificare le risorse che stanno per raggiungere i valori massimi. L'utilizzo di DTU viene visualizzato per impostazione predefinita. Fare clic su **Modifica** per modificare l'intervallo di tempo e i valori indicati.
2.	Usare [Query Performance Insight](sql-database-query-performance.md) per valutare le query che usano le DTU e quindi usare [Index Advisor](sql-database-index-advisor.md) per consigli e per la creazione di indici.
3.	È possibile usare le viste a gestione dinamica (DMV), gli eventi estesi (XEvent) e l'archivio di query in SSMS per ottenere i parametri delle prestazioni in tempo reale. Per suggerimenti dettagliati su ottimizzazione e monitoraggio, vedere l'[argomento con le linee guida sulle prestazioni](sql-database-performance-guidance.md).

## Passaggi per migliorare le prestazioni del database con più risorse
1.	Per i singoli database, è possibile [modificare i livelli di servizio](sql-database-scale-up.md) su richiesta per migliorare le prestazioni del database.
2.	Per più database, è consigliabile usare [pool di database elastici](sql-database-elastic-pool-guidance.md) per la scalabilità automatica delle risorse.

Se i problemi di prestazioni persistono, contattare il supporto per aprire un caso di supporto.

<!---HONumber=AcomDC_1217_2015-->