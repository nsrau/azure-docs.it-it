<properties
	pageTitle="Limiti delle risorse del database SQL di Azure"
	description="In questa pagina vengono descritti alcuni limiti di risorse comuni per il Database SQL Azure."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="03/02/2016"
	ms.author="jroth" />


# Limiti delle risorse del database SQL di Azure

## Panoramica

Il database SQL di Azure gestisce le risorse disponibili per un database usando due meccanismi diversi: la **governance delle risorse** e l'**imposizione di limiti**. Questo argomento illustra queste due aree principali relative alla gestione delle risorse.

## Governance delle risorse
Uno degli obiettivi di progettazione dei piani tariffari Basic, Standard e Premium prevede che database SQL di Azure si comporti come se il database fosse in esecuzione in un computer dedicato, completamente indipendente dagli altri database. La governance delle risorse emula questo comportamento. Se l'utilizzo delle risorse aggregato raggiunge il numero massimo di risorse disponibili di CPU, memoria, I/O di log e I/O di dati assegnate al database, la governance delle risorse inserisce nella coda le query in esecuzione e assegna le risorse alle query accodate man mano che si liberano.

Come in un computer dedicato, l'utilizzo di tutte le risorse disponibili determina un allungamento dei tempi per le query attualmente in esecuzione che può comportare timeout dei comandi nel client. Nelle applicazioni con una logica di ripetizione dei tentativi aggressiva e nelle applicazioni che eseguono query sul database con frequenza elevata possono essere restituiti messaggi di errore durante il tentativo di eseguire nuove query quando è stato raggiunto il limite di richieste simultanee.

### Consigli:
Monitorare l'utilizzo delle risorse e i tempi di risposta medi delle query quando si sta per raggiungere l'utilizzo massimo di un database. Quando si verificano latenze di query più elevate, sono disponibili tre opzioni:

1.	Ridurre la quantità di richieste in entrata al database per evitare timeout e l'accumulo delle richieste.

2.	Assegnare al database un livello di prestazioni più elevato.

3.	Ottimizzare le query per ridurre l'utilizzo delle risorse di ogni query. Per altre informazioni, vedere la sezione Hint/ottimizzazione di query in Linee guida per le prestazioni del database SQL di Azure.

## Applicazione dei limiti
Per le risorse diverse da CPU, memoria, I/O di log e I/O di dati, al raggiungimento dei limiti le nuove richieste vengono negate. I client visualizzeranno un [messaggio di errore](sql-database-develop-error-messages.md) a seconda del limite che è stato raggiunto.

Ad esempio, il numero di connessioni a un database SQL e il numero di richieste simultanee che possono essere elaborate viene limitato. Nel database SQL, il numero di connessioni al database può essere maggiore del numero di richieste simultanee per supportare i pool di connessioni. Mentre la quantità di connessioni disponibili può essere facilmente controllata dall'applicazione, la quantità di richieste parallele è più difficile da stimare e da controllare. In particolare, durante i carichi di picco quando l'applicazione invia troppe richieste o il database raggiunge i relativi limiti e inizia ad accumulare thread di lavoro a causa dei tempi di esecuzione delle query prolungati, è possibile che si verifichino errori.

## Livelli di servizio e livelli di prestazioni

Per un singolo database, i limiti di un database sono definiti dal livello del servizio del database e dal livello delle prestazioni.. La tabella seguente descrive le caratteristiche dei database Basic, Standard e Premium a diversi livelli di prestazioni.

[AZURE.INCLUDE [Tabella dei livelli di servizio del database SQL](../../includes/sql-database-service-tiers-table.md)]

I [Pool di database elastici](sql-database-elastic-pool.md) condividono le risorse tra i database nel pool. La tabella seguente descrive le caratteristiche dei pool di database elastici Basic, Standard e Premium.

[AZURE.INCLUDE [Tabella livelli di servizio di database SQL per database elastici](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Per una definizione espansa di ogni risorsa elencata nelle tabelle precedenti, vedere le descrizioni in [Limiti e capacità a livello di servizio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Per una panoramica dei livelli di servizio, vedere [Livelli di servizio e livelli di prestazioni del database SQL di Azure](sql-database-service-tiers.md).

## Altri limiti relativi al database SQL

| Area | Limite | Descrizione |
|---|---|---|
| Database che utilizzano l’esportazione automatizzata per ogni sottoscrizione | 10 | L’esportazione automatizzata consente di creare una pianificazione personalizzata per il backup dei database SQL. Per ulteriori informazioni, vedere [Database SQL: supporto per le esportazioni automatizzate di Database SQL](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|

## Risorse

[Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[Livelli di servizio e livelli di prestazioni del database SQL di Azure](sql-database-service-tiers.md)

[Messaggi di errore per programmi client di Database SQL](sql-database-develop-error-messages.md)

<!---HONumber=AcomDC_0309_2016-->