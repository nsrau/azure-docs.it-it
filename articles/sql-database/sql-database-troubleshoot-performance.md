<properties
	pageTitle="Suggerimenti sull'ottimizzazione delle prestazioni del database SQL | Microsoft Azure"
	description="Suggerimenti per l'ottimizzazione delle prestazioni nel database SQL di Azure tramite valutazione e miglioramento."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	keywords="ottimizzazione delle prestazioni di sql, ottimizzazione delle prestazioni del database, suggerimenti di ottimizzazione delle prestazioni di sql ottimizzazione delle prestazioni del database sql"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="v-shysun"/>  

# Suggerimenti sull'ottimizzazione delle prestazioni del database SQL
È possibile modificare il [livello di servizio](sql-database-service-tiers.md) di un singolo database o aumentare il numero di eDTU del pool di database elastici in qualsiasi momento per migliorare le prestazioni, ma è consigliabile innanzitutto identificare le opportunità per migliorare e ottimizzare le prestazioni delle query. Spesso le prestazioni non ottimali del database sono dovute a indici mancanti e query non ottimizzate correttamente. Questo articolo fornisce indicazioni per l'ottimizzazione delle prestazioni nel database SQL.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Passaggi per valutare e ottimizzare le prestazioni del database
1.	Nel [portale di Azure](https://portal.azure.com) fare clic su **Database SQL**, selezionare il database e quindi usare il grafico di monitoraggio per identificare le risorse che stanno per raggiungere i valori massimi. L'utilizzo di DTU viene visualizzato per impostazione predefinita. Fare clic su **Modifica** per modificare l'intervallo di tempo e i valori indicati.
2.	Usare [Informazioni dettagliate prestazioni query del database SQL di Azure](sql-database-query-performance.md) per valutare le query con le DTU e quindi usare [Advisor per database SQL](sql-database-advisor.md) per visualizzare raccomandazioni relative alla creazione e all'eliminazione di indici, alla parametrizzazione di query e alla correzione di problemi di schema.
3.	È possibile usare le viste a gestione dinamica (DMV), gli eventi estesi (XEvent) e l'archivio di query in SSMS per ottenere i parametri delle prestazioni in tempo reale. Per suggerimenti dettagliati su ottimizzazione e monitoraggio, vedere l'[argomento con le linee guida sulle prestazioni](sql-database-performance-guidance.md).


    > [AZURE.IMPORTANT] È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## Passaggi per migliorare le prestazioni del database con più risorse
1.	Per i singoli database, è possibile [modificare i livelli di servizio](sql-database-scale-up.md) su richiesta per migliorare le prestazioni del database.
2.	Per più database, è consigliabile usare [pool di database elastici](sql-database-elastic-pool-guidance.md) per la scalabilità automatica delle risorse.

<!---HONumber=AcomDC_0914_2016-->