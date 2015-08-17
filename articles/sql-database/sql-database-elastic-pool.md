<properties 
	pageTitle="Controllare la crescita esponenziale con i database elastici" 
	description="Un pool di database elastici del database SQL di Azure è una raccolta di risorse disponibili condivise da un gruppo di database elastici." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/25/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>



# Controllare la crescita esponenziale con i database elastici

Per gli sviluppatori SaaS con decine, centinaia o persino migliaia di database, un pool di database elastici semplifica il processo di creazione, manutenzione e gestione delle prestazioni tra database entro un budget da essi stessi controllato.

Un modello comune di applicazione SaaS consiste nel disporre, per ogni database, di un cliente diverso, ognuno con consumo di risorse vario e imprevedibile (CPU/IO/Memoria riepilogati con eDTU). Con questi picchi e fluttuazioni della domanda per ciascun database, può essere difficile prevedere e quindi eseguire il provisioning delle risorse. Ci si trova di fronte a due opzioni: un provisioning eccessivo delle risorse del database in base all'utilizzo di picco, e quindi costi eccessivi, oppure un provisioning insufficiente, per risparmiare sui costi, a scapito delle prestazioni e della soddisfazione del cliente durante i picchi.

Microsoft ha creato pool di database elastici specifici per risolvere questo problema.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Un pool di database elastico è una raccolta di risorse disponibili condivise dai database elastici nel pool. È possibile aggiungere database al pool o rimuoverli in qualsiasi momento. I database nel pool condividono le risorse (espresse in unità di velocità effettiva dei database elastici o eDTU) e la capacità di archiviazione del pool, ma ogni database usa solo le risorse necessarie nel momento in cui ne necessita, lasciando risorse disponibili per altri database che ne necessitano. Invece di fornire un provisioning eccessivo ai singoli database pagando per delle risorse inattive, si allocano risorse del pool in aggregazione, pagando un prezzo stimabile. In tal modo il costo viene spalmato, per cui è possibile ottenere un modello di business competitivo, e ogni database ottiene l’adattabilità delle prestazioni.

I database maggiormente candidati per i pool di database elastici in genere sono attivi per un periodo inferiore al 50% del tempo. Un tipico modello di attività consiste nel fatto che i database restano inattivi per un periodo, sono attivi in caso di piccole richieste di risorse e attivi in caso di richieste di risorse elevate. Non tutti i database seguono questo modello. Alcuni database hanno una richiesta di risorse più costante. Questi database sono più indicati per i livelli di servizio Basic, Standard e Premium, nei quali le risorse vengono assegnate singolarmente. Per determinare se i database possono costituire un vantaggio in un pool di database elastici, vedere [Considerazioni di prezzo e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).

È possibile creare un pool di database elastici in pochi minuti usando il portale di Microsoft Azure o PowerShell. Per i dettagli, vedere [Creare e gestire un pool elastico](sql-database-elastic-pool-portal.md). Per informazioni dettagliate sui pool di database elastici, inclusi i dettagli relativi ad API ed errori, vedere [Riferimento ai database elastici](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]I pool elastici sono attualmente in anteprima e sono disponibili unicamente con i server di database SQL V12.

## Gestire con facilità un numero elevato di database con strumenti di database elastici

Oltre a fornire un utilizzo più efficiente delle risorse e prestazioni prevedibili, i pool di database elastici semplificano lo sviluppo di applicazioni SaaS con strumenti che semplificano la creazione e la gestione del livello dati. L’esecuzione di attività di manutenzione e l'implementazione delle modifiche in set esteso di database, un processo da sempre lungo e complesso, è stato ridotto all’esecuzione di script nei processi elastici. La possibilità di creare ed eseguire un processo di database elastico elimina tutta la complessità del lavoro associata all'amministrazione di centinaia o migliaia di database. Per informazioni sul servizio processo di database elastico che consente l’esecuzione di script T-SQL tra tutti i database elastici di un pool, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).

È inoltre disponibile un set ricco e potente di strumenti di sviluppo per l'implementazione di modelli di applicazione di database elastici. Per i database partizionati, altri strumenti quali lo strumento di suddivisione e unione offrono la possibilità di suddividere i dati di una partizione e unirli a un’altra. Ciò riduce notevolmente il lavoro di gestione dei database partizionati su larga scala. Per ulteriori informazioni, vedere [Mappa degli argomenti degli strumenti dei database elastici](sql-database-elastic-scale-documentation-map.md).

## Funzionalità di continuità aziendale per i database in un pool

Attualmente in anteprima, i database in un pool di database elastici (nel livello standard elastico) supportano la maggior parte delle funzionalità disponibili per i database di livello Standard.

### Backup e ripristino dei database (ripristino temporizzato)

Il backup dei database in un pool di database elastici viene eseguito automaticamente dal sistema e i criteri di conservazione del backup sono identici ai database di livello Standard. Durante l'anteprima, i database in un pool verranno ripristinati in un nuovo database nello stesso pool; i database eliminati verranno sempre ripristinati come database autonomo all'esterno del pool come database S0 Standard. È possibile eseguire operazioni di ripristino dei database tramite il Portale di Azure o a livello di programmazione mediante l'API REST. Il supporto dei cmdlet PowerShell sarà disponibile a breve.

### Ripristino geografico

Il Ripristino geografico consente di ripristinare un database in un pool a un server in un'area diversa. Durante l'anteprima, per ripristinare un database in un pool su un server diverso, il server di destinazione deve disporre di un pool con lo stesso nome del pool di origine. Se necessario, creare un nuovo pool nel server di destinazione e assegnargli lo stesso nome prima di ripristinare il database. Se non esiste un pool con lo stesso nome nel server di destinazione, l'operazione Ripristino geografica avrà esito negativo. È possibile eseguire operazioni Ripristino geografico mediante il Portale di Azure o l'API REST. Il supporto dei cmdlet PowerShell sarà disponibile a breve.


### Replica geografica

I database per i quali è già abilitata la replica geografica possono essere spostati all’interno e all’esterno di un pool di database elastici e la replica continuerà a funzionare normalmente. È possibile attivare la replica geografica su un database che si trova già nel pool, se il server di destinazione specificato contiene un pool con lo stesso nome del pool di origine. Attualmente nell’anteprima, non è possibile attivare la replica geografica su un database che si trova già in un pool a un pool con un nome diverso o a un database singleton secondario.



 

<!---HONumber=August15_HO6-->