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
	ms.date="09/23/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Controllare la crescita esponenziale con i database elastici

Per gli sviluppatori SaaS con decine, centinaia o persino migliaia di database, un pool di database elastici semplifica il processo di creazione, manutenzione e gestione delle prestazioni tra database entro un budget da essi stessi controllato.

Un modello comune di applicazione SaaS consiste nel disporre, per ogni database, di un cliente diverso, ognuno con consumo di risorse vario e imprevedibile (CPU/IO/Memoria riepilogati con DTU). Con questi picchi e fluttuazioni della domanda per ciascun database, può essere difficile prevedere e quindi eseguire il provisioning delle risorse. Ci si trova di fronte a due opzioni: un provisioning eccessivo delle risorse del database in base all'utilizzo di picco, e quindi costi eccessivi, oppure un provisioning insufficiente, per risparmiare sui costi, a scapito delle prestazioni e della soddisfazione del cliente durante i picchi.

Microsoft ha creato pool di database elastici specifici per risolvere questo problema.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]


Pool elastica database forniscono una soluzione per i clienti che hanno bisogno per garantire che i relativi database Scarica tutte le risorse delle prestazioni che necessari quando ne hanno bisogno, ma offre anche un meccanismo di allocazione risorse semplice e un budget stimabile. La scalabilità delle prestazioni su richiesta dei singoli database all'interno di un pool database elastici infatti ogni database all'interno di un pool utilizza eDTUs da un set condiviso associato al pool. In questo modo i database con carichi di utilizzare altri per soddisfare la domanda, mentre i database con carico ridotto utilizzano minore e i database in alcun carico non consumano qualsiasi eDTUs. Effettuando il provisioning per il pool di risorse anziché per singoli database è non solo semplificare la gestione di più database, è anche un budget prevedibile per un carico di lavoro in caso contrario imprevedibile.

Se più eDTUs sono necessari per soddisfare le esigenze di un pool (database aggiuntivi vengono aggiunti a un pool o l'avvio di database esistenti utilizzando più eDTUs), è possibile aggiungere ulteriori eDTUs a un pool esistente senza tempi di inattività del database o un impatto negativo sui database. Analogamente, se eDTUs aggiuntivi non sono più necessari e possono essere rimossi da un pool esistente in qualsiasi punto nel tempo.

![database che condividono eDTUs][1]

Database che sono ottimi candidati per il pool di database flessibile, in genere dispongono di periodi di attività e altri periodi di inattività. Si consideri l'esempio precedente in cui è possibile visualizzare l'attività di un singolo database, 4 database e infine un pool di database elastici con 20 database. Questi database con diverse attività nel tempo sono ottimi candidati per il pool di database flessibile perché non sono attive contemporaneamente e può condividere eDTUs. Non tutti i database seguono questo modello. Alcuni database hanno una richiesta di risorse più costante. Questi database sono più indicati per i livelli di servizio Basic, Standard e Premium, nei quali le risorse vengono assegnate singolarmente. Per determinare se i database possono costituire un vantaggio in un pool di database elastici, vedere [Considerazioni di prezzo e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).

È possibile creare un pool di database elastici in pochi minuti usando il portale di Microsoft Azure, PowerShell, C#. Per i dettagli, vedere [Creare e gestire un pool di database elastici](sql-database-elastic-pool-portal.md). Per informazioni dettagliate sui pool di database elastici, inclusi i dettagli relativi ad API ed errori, vedere [Riferimento ai pool di database elastici](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12.

## Gestire con facilità un numero elevato di database con strumenti di database elastici

Oltre a fornire un utilizzo più efficiente delle risorse e prestazioni prevedibili, i pool di database elastici semplificano lo sviluppo di applicazioni SaaS con strumenti che semplificano la creazione e la gestione del livello dati. L’esecuzione di attività di manutenzione e l'implementazione delle modifiche in set esteso di database, un processo da sempre lungo e complesso, è stato ridotto all’esecuzione di script nei processi elastici. La possibilità di creare ed eseguire un processo di database elastico elimina tutta la complessità del lavoro associata all'amministrazione di centinaia o migliaia di database. Per informazioni sul servizio processo di database elastico che consente l’esecuzione di script T-SQL tra tutti i database elastici di un pool, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).

È inoltre disponibile un set ricco e potente di strumenti di sviluppo per l'implementazione di modelli di applicazione di database elastici. Per i database partizionati, altri strumenti quali lo strumento di suddivisione e unione offrono la possibilità di suddividere i dati di una partizione e unirli a un’altra. Ciò riduce notevolmente il lavoro di gestione dei database partizionati su larga scala. Per ulteriori informazioni, vedere [Mappa degli argomenti degli strumenti dei database elastici](sql-database-elastic-scale-documentation-map.md).

## Funzionalità di continuità aziendale per i database in un pool

Attualmente in anteprima, elastici database supportano la maggior parte [funzionalità di continuità aziendale](https://msdn.microsoft.com/library/azure/hh852669.aspx) che sono disponibili per singoli database su server V12.

### Backup e ripristino dei database ([ripristino temporizzato](https://msdn.microsoft.com/library/azure/hh852669.aspx#BKMK_PITR))

Il backup dei database in un pool di database elastici viene eseguito automaticamente dal sistema e i criteri di conservazione del backup corrispondono al relativo livello di servizio per singoli database. In particolare, un database elastico in un pool di base può essere ripristinato in qualsiasi punto di ripristino negli ultimi 7 giorni, è possibile ripristinare un database elastico in un pool Standard a qualsiasi punto di ripristino negli ultimi 14 giorni e un database elastico in un pool Premium può essere ripristinato in qualsiasi punto di ripristino negli ultimi 35 giorni. Durante l'anteprima, verranno ripristinati i database in un pool in un nuovo database nello stesso pool. Database eliminati verranno sempre ripristinati come database autonomi all'esterno del pool nel livello di prestazioni più basso per tale livello di servizio. Ad esempio, verrà ripristinato un database in un pool Standard che viene eliminato elastico come database S0. È possibile eseguire operazioni di ripristino dei database tramite il Portale di Azure o a livello di programmazione mediante l'API REST. Il supporto dei cmdlet PowerShell sarà disponibile a breve.

### [Ripristino geografico](https://msdn.microsoft.com/library/azure/hh852669.aspx#BKMK_GEO)

Il Ripristino geografico consente di ripristinare un database in un pool a un server in un'area diversa. Durante l'anteprima, per ripristinare un database in un pool su un server diverso, il server di destinazione deve disporre di un pool con lo stesso nome del pool di origine. Se necessario, creare un nuovo pool nel server di destinazione e assegnargli lo stesso nome prima di ripristinare il database. Se non esiste un pool con lo stesso nome nel server di destinazione, l'operazione Ripristino geografica avrà esito negativo. È possibile eseguire operazioni Ripristino geografico mediante il Portale di Azure o l'API REST. Il supporto dei cmdlet PowerShell sarà disponibile a breve.


### [Replica geografica](https://msdn.microsoft.com/library/azure/dn783447.aspx)

I database per i quali è già abilitata la replica geografica possono essere spostati all’interno e all’esterno di un pool di database elastici e la replica continuerà a funzionare normalmente. È possibile attivare la replica geografica su un database che si trova già nel pool, se il server di destinazione specificato contiene un pool con lo stesso nome del pool di origine.

### Importazione ed esportazione

L’esportazione di un database dall’interno di un pool è supportata. Attualmente, non è supportata l'importazione di un database direttamente in un pool, ma è possibile importare in un singolo database e quindi spostare il database in un pool.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=Oct15_HO1-->