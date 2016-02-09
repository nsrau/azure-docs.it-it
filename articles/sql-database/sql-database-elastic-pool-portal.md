<properties
	pageTitle="Creare pool di database elastici scalabili | Microsoft Azure"
	description="Come aggiungere un pool di database elastici scalabile alla configurazione del database SQL per semplificare l'amministrazione e la condivisione delle risorse tra più database."
	keywords="database scalabile,configurazione del database"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Creare un pool scalabile di database elastici per database SQL nel portale di Azure

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Questo articolo illustra come creare un [pool di database elastici](sql-database-elastic-pool.md) scalabile usando il portale di Azure. Una configurazione del database SQL con pool di database elastici semplifica l'amministrazione e la condivisione delle risorse tra più database.

> [AZURE.NOTE] I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12. Se si usa un server di database SQL V11 è possibile [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-powershell.md) in un unico passaggio.


Prima di iniziare, è necessario avere un database in un server di database SQL V12. Se non è disponibile, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md) per crearne uno in meno di cinque minuti. In alternativa, se si ha già un server di database SQL V11 è possibile [eseguire l'aggiornamento a V12 nel portale](sql-database-v12-upgrade.md) e tornare a seguire queste istruzioni per creare un pool.


## Passaggio 1: Creare un nuovo pool

Creare un pool di database elastici aggiungendo un nuovo pool a un server. È possibile aggiungere più pool a un server, ma non aggiungere database da diversi server nello stesso pool.


1. Nel [portale di Azure](http://portal.azure.com/) fare clic su **SQL Server**.
2. Selezionare un server che contiene i database da aggiungere al pool.
3. Fare clic su **Nuovo pool** oppure, se viene visualizzato un messaggio che informa che esiste un pool consigliato, selezionarlo per esaminare e creare facilmente un pool ottimizzato per i database del server. Altri dettagli sulle scelte consigliate sono disponibili di seguito.


    ![Aggiungere un pool a un server](./media/sql-database-elastic-pool-portal/new-pool.png)


4. Nel pannello **Pool di database elastici** è possibile lasciare il nome predefinito o digitarne uno per il nuovo pool.

    ![Configurare un pool elastico](./media/sql-database-elastic-pool-portal/configure-elastic-pool.png)


### Pool di database elastici consigliati

Passare a un server di database SQL. È possibile che venga visualizzato un messaggio che informa che sono disponibili pool di database elastici consigliati per il server (solo V12).

### Perché viene visualizzato un suggerimento

Il servizio database SQL valuta la cronologia di utilizzo e suggerisce uno o più pool di database elastici quando questo approccio è più conveniente rispetto all'uso di singoli database.

![pool consigliato](./media/sql-database-elastic-pool-portal/recommended-pool.png)

Ogni indicazione viene configurata con un unico sottoinsieme di database del server che meglio si adatta al pool.

Ogni indicazione relativa al pool include le informazioni seguenti:

- Livello di prezzo per il pool (Basic, Standard o Premium).
- Quantità di eDTU del pool appropriata.
- Impostazioni di eDTU min/max dei database elastici.
- Elenco di database consigliati.

Il servizio prende in considerazione la telemetria degli ultimi 30 giorni quando vengono consigliati pool di database elastici. Affinché un database possa essere considerato un candidato per un pool di database elastici, deve esistere per almeno 7 giorni. I database che sono già in un pool di database elastici non vengono considerati come candidati dai consigli relativi ai pool di database elastici.

Il servizio valuta le risorse necessarie e l'efficienza dei costi dello spostamento dei singoli database in ogni livello di servizio in pool di database elastici dello stesso livello. Ad esempio, vengono valutati tutti i database Standard in un server per l’utilizzo in un pool elastico Standard. Ciò significa che il servizio non effettua consigli relativi a livelli diversi, ad esempio lo spostamento di un database Standard in un pool Premium.


### Creare un pool consigliato

1. Fare clic sul messaggio per visualizzare un elenco di pool consigliati.
1. Fare clic su un pool per visualizzare indicazioni dettagliate.
2. Esaminare tutte le impostazioni incluse nell'indicazione e accettarla così com'è oppure modificarla in base alle esigenze aziendali specifiche.


## Passaggio 2: scegliere un piano tariffario

Il piano tariffario del pool determina le funzionalità disponibili per i database elastici nel pool e il numero massimo di eDTU (MAX eDTU) e la memoria (GB) disponibili per ciascun database. Per altre informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools).

>[AZURE.NOTE] Attualmente in anteprima, non è possibile modificare il livello di prezzo di un pool di database elastici dopo averlo creato. Per modificare il livello di prezzo per un pool di database elastici esistente creare un nuovo pool elastico nel livello di prezzo desiderato e migrare i database elastici in questo nuovo pool.

   ![Scegliere un piano tariffario](./media/sql-database-elastic-pool-portal/pricing-tier.png)

### Configurare il pool

Dopo avere impostato il piano tariffario, fare clic su Configura pool dove è possibile aggiungere i database, impostare le eDTU e lo spazio di archiviazione (in GB) del pool, nonché il numero minimo e massimo di eDTU per i database elastici nel pool.

## Passaggio 3: aggiungere database al pool

È possibile aggiungere o rimuovere database in un pool in qualsiasi momento.

1. Durante la creazione del pool, fare clic su **Aggiungi database** nel pannello **Configura pool**.
2. Selezionare i database da aggiungere al pool:

    ![Aggiungere database](./media/sql-database-elastic-pool-portal/add-databases.png)

    Quando si seleziona un database da aggiungere a un pool, è necessario soddisfare le condizioni seguenti:

    - Il pool deve disporre di spazio sufficiente per il database (non può già contenere il numero massimo di database). In particolare, il pool deve disporre di un numero di eDTU sufficiente a coprire la garanzia eDTU per ogni database (ad esempio, se la garanzia di eDTU per il gruppo è 400, e la garanzia di eDTU per ciascun database è 10, il numero massimo di database consentiti nel pool è 40 (400 eDTU/10 eDTU garantite per database = massimo 40 database).
    - Le funzionalità correnti utilizzate dal database devono essere disponibili nel pool.

### Indicazioni dinamiche

Dopo aver aggiunto i database al pool, le indicazioni verranno dinamicamente generate in base all'utilizzo storico dei database selezionati. Queste indicazioni saranno visualizzate nel grafico relativo all'utilizzo di eDTU e GB, oltre che come banner nella parte superiore del pannello **Configura pool**. Queste indicazioni sono concepite per facilitare la creazione di un pool ottimizzato per database specifici.


 ![indicazioni dinamiche](./media/sql-database-elastic-pool-portal/dynamic-recommendation.png)


## Passaggio 4: impostare le caratteristiche delle prestazioni del pool

È possibile configurare le prestazioni del pool impostando i parametri delle prestazioni sia per il pool e che per i database elastici nel pool. Tenere presente che le **impostazioni dei database elastici** si applicano a tutti i database nel pool.

 ![Configurare un pool elastico](./media/sql-database-elastic-pool-portal/configure-performance.png)

È possibile impostare tre parametri che definiscono le prestazioni per il pool: la garanzia di eDTU per il pool e il numero MIN e MAX di eDTU per i database elastici nel pool. Nella tabella seguente vengono descritti singolarmente e vengono fornite alcune indicazioni su come impostarli. Per le specifiche impostazioni dei valori disponibili, vedere [Riferimento ai pool di database elastici](sql-database-elastic-pool-reference.md).

| Parametro di prestazioni | Descrizione |
| :--- | :--- |
| **eDTU POOL**: garanzia eDTU per il pool | La garanzia eDTU per il pool è il numero garantito di eDTU disponibili e condivise da tutti i database nel pool. <br> È necessario eseguire il provisioning della dimensione della garanzia eDTU specificata per un gruppo considerando l'utilizzo di eDTU cronologico del gruppo. In alternativa, è possibile impostare questa dimensione mediante la garanzia eDTU desiderata per database e l'utilizzo dei database attivi contemporaneamente. La garanzia eDTU per il pool, inoltre, è correlata alla quantità di spazio di archiviazione disponibile per il pool, per ogni eDTU allocata al pool si ottiene una quantità fissa di spazio di archiviazione del database. <br> **Su quale valore deve essere impostata la garanzia eDTU del pool?** <br>Come minimo, è necessario impostare la garanzia eDTU del pool su ([numero dei database] x [utilizzo medio DTU per database]) |
| **MIN eDTU** - Garanzia eDTU per ciascun database | La garanzia eDTU per database è il numero di eDTU garantito a un singolo database del pool. <br> **Su quale valore deve essere impostata la garanzia eDTU per database?** <br> In genere, la garanzia di eDTU per ogni database (eDTU MIN) è impostata su un punto qualsiasi compreso tra 0 e l’ ([utilizzo medio per ogni database]). La garanzia eDTU per database è un'impostazione globale che consente di impostare la garanzia eDTU per tutti i database nel pool. |
| **MAX eDTU** - Limite di utilizzo delle eDTU per database | Il valore MAX eDTU per database è il numero massimo di eDTU che un singolo database del pool può utilizzare. Impostare il limite di utilizzo eDTU per database su un valore sufficientemente elevato per gestire burst massimi o picchi che potrebbero verificarsi nei database. È possibile impostare questo limite di utilizzo sul limite di sistema, che dipende dal livello di prezzo del pool (1000 eDTU per il livello Premium). La dimensione specifica di questo limite di utilizzo deve essere in grado di contenere i modelli di utilizzo dei database all'interno del gruppo. È previsto un certo grado di overcommitt del gruppo, poiché il pool in genere presuppone schemi di utilizzo a freddo e a caldo per i database, in cui tutti i database non raggiungono il picco contemporaneamente.<br> **Su quale valore deve essere impostato il limite di utilizzo delle eDTU?** <br> Impostare il valore MAX eDTU o il limite di utilizzo delle eDTU per database su ([picco di utilizzo del database]). Si supponga, ad esempio, che il picco di utilizzo per database sia 50 DTU e che solo il 20% dei 100 database nel gruppo raggiunga il picco contemporaneamente. Se il limite di utilizzo delle eDTU per database è impostato su 50 eDTU, è ragionevole eseguire l’overcommit del pool moltiplicando per 5 e impostare la garanzia eDTU per il gruppo (eDTU POOL) su 1.000 eDTU. Vale inoltre la pena notare che il limite di utilizzo delle eDTU non è una garanzia di risorse per un database, bensì un limite massimo di eDTU che è possibile raggiungere, se disponibile. |


## Aggiungere e rimuovere database in un pool

Dopo aver creato il pool è possibile aggiungere o rimuovere database esistenti all'interno o all'esterno del pool aggiungendo o rimuovendo database dalla pagina **Database elastici** (passare al pool e fare clic sul collegamento **Database elastici** in **Informazioni di base**).

- Fare clic su **Aggiungi database** per aprire l'elenco dei database che è possibile aggiungere al pool.

    -oppure-

- Selezionare i database da rimuovere dal pool e fare clic su **Rimuovi database**.

![pool consigliato](./media/sql-database-elastic-pool-portal/add-remove-databases.png)

## Creare un nuovo database in un pool

Per creare un nuovo database in un pool, passare al pool scelto e fare clic su **Crea database**.

Il database SQL è già configurato per il pool e il server corretti. Immettere un nome e selezionare le opzioni di database, quindi fare clic su **OK** per creare il nuovo database:


   ![creare database elastici](./media/sql-database-elastic-pool-portal/create-database.png)



## Monitorare e gestire un pool di database elastici

Dopo aver creato un pool di database elastici, è possibile monitorare e gestire il pool nel portale passando all'elenco dei pool esistenti e selezionando il pool desiderato.

Dopo aver creato un pool, è possibile effettuare le seguenti operazioni:

- Selezionare **Configura pool** per modificare le impostazioni relative alle eDTU del pool e alle eDTU per database.
- Selezionare **Crea processo** e gestire i database nel pool mediante la creazione di processi elastici. I processi elastici consentono l'esecuzione di script Transact-SQL su qualsiasi numero di database nel pool. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).
- Selezionare **Gestione processi** per amministrare i processi elastici esistenti.
- Fare clic su **Crea database** per creare un nuovo database nel pool.
- Fare clic sul collegamento relativo alle impostazioni del pool per specificare i valori di eDTU e GB per ogni pool
- Fare clic sul collegamento relativo ai database per aggiungere o rimuovere database nel pool.
- Fare clic sul collegamento relativo alle impostazioni del database per specificare i valori minimo e massimo delle eDTU per i database elastici nel pool. 

![Monitorare un pool elastico](./media/sql-database-elastic-pool-portal/pool-tasks.png)


Quando si seleziona un pool esistente, è possibile visualizzare l'utilizzo delle risorse del pool. Fare clic sul grafico **Utilizzo delle risorse** per aprire il pannello **Metrica**, nel quale è possibile personalizzare il grafico e configurare gli avvisi.

![Monitorare un pool elastico][4] ![utilizzo delle risorse][6]

Fare clic su **Modifica grafico** per aggiungere parametri in modo da poter visualizzare facilmente i dati di telemetria per il pool.


![modifica grafico][7]


## Passaggi successivi
Dopo aver creato un pool di database elastici, è possibile gestire i database nel pool mediante la creazione di processi elastici. I processi elastici facilitano l’esecuzione di script Transact-SQL su qualsiasi numero di database nel pool. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).



## Risorse aggiuntive

- [Pool elastico di database SQL](sql-database-elastic-pool.md)
- [Creare un pool elastico di database SQL con PowerShell](sql-database-elastic-pool-powershell.md)
- [Creare e gestire Database SQL con C#](sql-database-client-library.md)
- [Riferimento ai database elastici](sql-database-elastic-pool-reference.md)


<!--Image references-->
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[10]: ./media/sql-database-elastic-pool-portal/star.png

<!---HONumber=AcomDC_0204_2016-->