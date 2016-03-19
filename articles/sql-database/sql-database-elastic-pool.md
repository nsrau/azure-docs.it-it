<properties
	pageTitle="Pool di database elastici per database SQL | Microsoft Azure"
	description="Scopri come è possibile controllare la crescita esponenziale nei database SQL con dei pool di database elastici, una modalità di condivisione delle risorse disponibili in molti database."
	keywords="database elastici, database SQL"	
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/11/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Controllare la crescita esponenziale nei database SQL tramite i pool di database elastici per condividere le risorse

Uno sviluppatore SaaS deve creare e gestire decine, centinaia o addirittura migliaia di database SQL. I pool elastici semplificano la creazione e la manutenzione dei database nonché la gestione delle relative prestazioni, il tutto nell'ambito di un budget controllabile. È possibile aggiungere e rimuovere database dal pool in base alle proprie esigenze. È possibile [creare un pool di database elastici](sql-database-elastic-pool-portal.md) per database SQL in pochi minuti usando il portale di Microsoft Azure, [PowerShell](sql-database-elastic-pool-powershell.md) o [C#](sql-database-elastic-pool-csharp.md).

Per informazioni dettagliate sulle API e sugli errori, vedere [Riferimento ai pool di database elastici di database SQL](sql-database-elastic-pool-reference.md).

## Funzionamento

Un modello comune di applicazione SaaS prevede l'assegnazione di un database a ogni cliente. Ciascun cliente (database) ha requisiti di risorse imprevedibili per memoria, IO e CPU. Questi picchi e stagnazioni della domanda rendono difficile l'allocazione delle risorse. In genere, si dispone di due opzioni: eseguire un provisioning eccessivo delle risorse in base all'utilizzo di picco a un costo maggiore oppure eseguire un provisioning insufficiente per risparmiare sui costi, a scapito delle prestazioni e della soddisfazione del cliente durante i picchi. I pool di database elastici risolvono il problema assicurando ai database prestazioni adeguate in ogni momento e situazione, fornendo al tempo stesso un semplice meccanismo di allocazione delle risorse con un budget prevedibile.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Nel database SQL, la misura relativa della capacità del database di gestire le richieste delle risorse è espressa in unità di transazione del database (DTU) per i singoli database e in DTU elastiche (eDTU) per i pool di database elastici. Per altre informazioni su DTU e eDTU, vedere [Introduzione al database SQL](sql-database-technical-overview.md#understand-dtus).

A un pool viene assegnato un numero definito di eDTU per un prezzo prestabilito. All'interno del pool i singoli database sono sufficientemente flessibili da assicurare una scalabilità automatica nell'ambito di parametri prefissati. Se il carico di lavoro è importante, un database può utilizzare più eDTU per soddisfare la domanda. Se invece il carico di lavoro è più leggero, i database utilizzano meno eDTU e in assenza di carico non ne utilizzano affatto. La possibilità di effettuare il provisioning delle risorse per l'intero pool e non per i singoli database semplifica le attività di gestione. Il budget del pool, inoltre, è facilmente prevedibile.

È possibile aggiungere ulteriori eDTU a un pool esistente senza tempi di inattività del database o impatti negativi sui database. Analogamente, se gli eDTU aggiuntivi non sono più necessari, è possibile rimuoverli da un pool esistente in qualsiasi momento.

È possibile aggiungere e rimuovere database dal pool. Se si prevede che un database sottoutilizzerà le proprie risorse, è possibile rimuoverlo.

## Scelta dei database da inserire in un pool

![Database SQL che condividono eDTU in un pool di database elastici.][1]

I database che sono ottimi candidati per i pool di database elastici sono in genere caratterizzati da periodi di attività e da periodi di inattività. Nell'esempio sopra riportato è possibile visualizzare l'attività di un singolo database, di 4 database e infine di un pool di database elastici con 20 database. I database con livelli di attività variabili nel corso del tempo sono ottimi candidati per i pool elastici in quanto non sono tutti attivi nello stesso momento e possono condividere gli eDTU. Non tutti i database seguono questo modello. I database che hanno una richiesta di risorse più costante sono più indicati per i livelli di servizio Basic, Standard e Premium, nei quali le risorse vengono assegnate singolarmente.

[Considerazioni di prezzo e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).


> [AZURE.NOTE] I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12.

## Processi di database elastici

Se si usa un pool, le attività di gestione sono semplificate dall'esecuzione di script in **[processi elastici](sql-database-elastic-jobs-overview.md)**. Un processo di database elastico elimina la maggior parte delle attività ripetitive associate a un elevato numero di database. Per iniziare, vedere [Introduzione ai processi di database elastici](sql-database-elastic-jobs-getting-started.md).

Per informazioni sugli altri strumenti, vedere [Database SQL - Usare funzionalità e strumenti di database elastici](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/).

## Funzionalità di continuità aziendale per i database in un pool

Attualmente in anteprima, elastici database supportano la maggior parte [funzionalità di continuità aziendale](sql-database-business-continuity.md) che sono disponibili per singoli database su server V12.

### Ripristino temporizzato

Il backup dei database in un pool di database elastico viene eseguito automaticamente dal sistema e i criteri di conservazione del backup corrispondono al relativo livello di servizio per singoli database. In breve, i database di ogni livello hanno un intervallo di ripristino diverso:

* **Pool Basic**: ripristinabile in qualsiasi momento entro gli ultimi 7 giorni. 
* **Pool Standard**: ripristinabile in qualsiasi momento entro gli ultimi 14 giorni.
* **Pool Premium**: ripristinabile in qualsiasi momento entro gli ultimi 35 giorni. 

Durante l'anteprima, i database di un pool verranno ripristinati in un nuovo database dello stesso pool. Database eliminati verranno sempre ripristinati come database autonomi all'esterno del pool nel livello di prestazioni più basso per tale livello di servizio. Ad esempio, verrà ripristinato un database in un pool Standard che viene eliminato elastico come database S0. È possibile eseguire operazioni di ripristino dei database tramite il Portale di Azure o a livello di programmazione mediante l'API REST. Il supporto dei cmdlet PowerShell sarà disponibile a breve.

### Ripristino geografico

Il Ripristino geografico consente di ripristinare un database in un pool a un server in un'area diversa. Durante l'anteprima, per ripristinare un database in un pool su un server diverso, il server di destinazione deve disporre di un pool con lo stesso nome del pool di origine. Se necessario, creare un nuovo pool nel server di destinazione e assegnargli lo stesso nome prima di ripristinare il database. Se non esiste un pool con lo stesso nome nel server di destinazione, l'operazione Ripristino geografica avrà esito negativo. Per informazioni dettagliate, vedere [Ripristino tramite il ripristino geografico](sql-database-disaster-recovery.md#recover-using-geo-restore).


### Replica geografica

La replica geografica è disponibile per qualsiasi database in un pool di database elastici Standard o Premium. Uno o tutti i database in una relazione di replica geografica possono essere in un pool di database elastici, a condizione che i livelli di servizio siano uguali. È possibile configurare la replica geografica per i pool di database elastici usando il [portale di Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) o [Transact-SQL](sql-database-geo-replication-transact-sql.md).

### Importazione ed esportazione

L’esportazione di un database dall’interno di un pool è supportata. Attualmente, non è supportata l'importazione di un database direttamente in un pool, ma è possibile importare in un singolo database e quindi spostare il database in un pool.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0302_2016-->