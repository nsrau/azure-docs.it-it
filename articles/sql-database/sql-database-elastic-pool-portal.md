<properties
	pageTitle="Creare e gestire un database di pool di database elastici SQL tramite PowerShelll | Microsoft Azure"
	description="Creare un singolo pool di risorse da condividere tra un gruppo di database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/29/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Creare un pool di database elastici

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

In questo articolo viene illustrato come creare un pool di database elastico, che semplifica il processo di creazione, manutenzione e gestione delle prestazioni e dei costi per più database. Prima di iniziare, è necessario almeno un database in un server di database SQL V12. Se non è disponibile, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md) per crearne uno in meno di cinque minuti.


> [AZURE.NOTE]I pool di database elastici sono attualmente in anteprima e sono disponibili unicamente con i server di Database SQL V12.


## Passaggio 1: aggiungere un pool a un server

Creare un pool di database elastici aggiungendo un nuovo pool a un server. È possibile aggiungere più pool a un server, ma solo un (1) server può essere associato a ciascun pool. Inoltre, tutti o alcuni dei database in un server possono essere aggiunti a un pool.


Nel [portale di anteprima di Azure](https://ms.portal.azure.com/) fare clic su **SQL Server**, fare clic sul server che ospita i database che si desidera aggiungere al pool, quindi fare clic su **Aggiungi pool**.

![Aggiungere un pool a un server](./media/sql-database-elastic-pool-portal/elastic-pool-add-pool.png)

-oppure-

Se viene visualizzato un messaggio che informa che esiste un pool consigliato per un server, fare clic per esaminare facilmente e creare un pool ottimizzato per i database del server. Per altre informazioni, vedere [Pool di database elastici consigliati](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).
   
  
   ![Creare un pool elastico][1]


Il pannello **Pool di database elastico** fornisce opzioni per scegliere il livello di prezzo, aggiungere i database e configurare le caratteristiche di prestazioni del pool.

> [AZURE.NOTE]Quando si seleziona il comando **Aggiungi pool** per la prima volta è necessario accettare le condizioni dell'anteprima selezionando **CONDIZIONI PER L'ANTEPRIMA** e completando il pannello **Condizioni per l'anteprima**. È sufficiente eseguire questa operazione una volta per ogni sottoscrizione.

   ![Configurare un pool elastico][2]

## Passaggio 2: scegliere un piano tariffario

Il piano tariffario del pool determina le funzionalità disponibili per i database elastici nel pool e il numero massimo di eDTU (MAX eDTU) e la memoria (GB) disponibili per ciascun database. Per altre informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools).

>[AZURE.NOTE]Attualmente in anteprima, non è possibile modificare il livello di prezzo di un pool di database elastici dopo averlo creato. Per modificare il livello di prezzo per un pool di database elastici esistente creare un nuovo pool elastico nel livello di prezzo desiderato e migrare i database elastici in questo nuovo pool.

   ![Livello di prezzo][9]


### Indicazioni sul piano tariffario

Il servizio di Database SQL valuta la cronologia di utilizzo e consiglia uno o più pool di database elastici quando è sono più convenienti rispetto all'uso di livelli di prestazioni per singoli database.

I piani tariffari con una stella (![stella][10]) sono consigliati in base ai carichi di lavoro dei database.

Se più di un livello di prezzo è consigliabile esso indica che è necessario creare più pool di database elastici. Ogni indicazione viene configurata con un unico sottoinsieme di database del server che meglio si adatta al pool.

Oltre a suggerire semplicemente un livello di prezzo di un pool di database elastici, ogni indicazione relativa al pool contiene quanto segue:

- Livello di prezzo per il pool (Basic, Standard o Premium).
- Quantità di eDTU del pool appropriata.
- Impostazioni di eDTU min/max dei database elastici.  
- Elenco di database consigliati.

Il servizio prende in considerazione la telemetria degli ultimi 30 giorni quando vengono consigliati pool di database elastici. Affinché un database possa essere considerato un candidato per un pool di database elastici, deve esistere per almeno 7 giorni. I database che sono già in un pool di database elastici non vengono considerati come candidati dai consigli relativi ai pool di database elastici.

Il servizio valuta le risorse necessarie e l'efficienza dei costi dello spostamento dei singoli database in ogni livello di servizio in pool di database elastici dello stesso livello. Ad esempio, vengono valutati tutti i database Standard in un server per l’utilizzo in un pool elastico Standard. Ciò significa che il servizio non effettua consigli relativi a livelli diversi, ad esempio lo spostamento di un database Standard in un pool Premium.

>[AZURE.NOTE]I database Web e Business vengono mappati a uno dei nuovi livelli Basic, Standard o Premium in base alle dimensioni di database e alla cronologia di utilizzo. Il mapping ai nuovi livelli consiglia database Web e Business al pool appropriato.


## Passaggio 3: aggiungere database al pool

In qualsiasi momento, è possibile selezionare i database specifici che si desidera includere nel pool. Quando si crea un nuovo pool, Azure consiglia i database che beneficeranno della presenza in un pool e li contrassegna per l'inclusione. È possibile aggiungere tutti i database disponibili nel server oppure è possibile selezionare o deselezionare i database dall'elenco iniziale come desiderato.

   ![Aggiungere database][5]

Quando si seleziona un database da aggiungere a un pool, è necessario soddisfare le condizioni seguenti:

- Il pool deve disporre di spazio sufficiente per il database (non può già contenere il numero massimo di database). In particolare, il pool deve disporre di un numero di eDTU sufficiente a coprire la garanzia eDTU per ogni database (ad esempio, se la garanzia di eDTU per il gruppo è 400, e la garanzia di eDTU per ciascun database è 10, il numero massimo di database consentiti nel pool è 40 (400 eDTU/10 eDTU garantite per database = massimo 40 database).
- Le funzionalità correnti utilizzate dal database devono essere disponibili nel pool.


## Passaggio 4: modificare le caratteristiche delle prestazioni

È possibile configurare le prestazioni del pool impostando i parametri delle prestazioni sia per il pool e che per i database elastici nel pool. Tenere presente che le **impostazioni dei database elastici** si applicano a tutti i database nel pool.

   ![Configurare un pool elastico][3]

È possibile impostare tre parametri che definiscono le prestazioni per il pool: la garanzia di eDTU per il pool e il numero MIN e MAX di eDTU per i database elastici nel pool. Nella tabella seguente vengono descritti singolarmente e vengono fornite alcune indicazioni su come impostarli. Per le specifiche impostazioni dei valori disponibili, vedere [Riferimento ai pool di database elastici](sql-database-elastic-pool-reference.md).

| Parametro di prestazioni | Descrizione |
| :--- | :--- |
| **eDTU POOL** - garanzia eDTU per il pool | La garanzia eDTU per il pool è il numero garantito di eDTU disponibili e condivise da tutti i database nel pool. <br> È necessario eseguire il provisioning della dimensione della garanzia eDTU specificata per un gruppo considerando l'utilizzo di eDTU cronologico del gruppo. In alternativa, è possibile impostare questa dimensione mediante la garanzia eDTU desiderata per database e l'utilizzo dei database attivi contemporaneamente. La garanzia eDTU per il pool, inoltre, è correlata alla quantità di spazio di archiviazione disponibile per il pool, per ogni eDTU allocata al pool si ottiene una quantità fissa di spazio di archiviazione del database. <br> **Su quale valore deve essere impostata la garanzia eDTU del pool?** <br>Come minimo, è necessario impostare la garanzia eDTU del pool su ([numero dei database] x [utilizzo medio DTU per database]) |
| **MIN eDTU** - Garanzia eDTU per ciascun database | La garanzia eDTU per database è il numero di eDTU garantito a un singolo database del pool. Ad esempio, nei pool Standard è possibile impostare questa garanzia su 0, 10, 20, 50 o 100 eDTU oppure è possibile scegliere di non fornire una garanzia ai database nel gruppo (MIN eDTU = 0). <br> **Su quale valore deve essere impostata la garanzia eDTU per database?** <br> In genere, la garanzia di eDTU per ogni database (eDTU MIN) è impostata su un punto qualsiasi compreso tra 0 e l’ ([utilizzo medio per ogni database]). La garanzia eDTU per database è un'impostazione globale che consente di impostare la garanzia eDTU per tutti i database nel pool. |
| **MAX eDTU** - Limite di utilizzo delle eDTU per database | Il valore MAX eDTU per database è il numero massimo di eDTU che un singolo database del pool può utilizzare. Impostare il limite di utilizzo eDTU per database su un valore sufficientemente elevato per gestire burst massimi o picchi che potrebbero verificarsi nei database. È possibile impostare questo limite di utilizzo sul limite di sistema, che dipende dal livello di prezzo del pool (1000 eDTU per il livello Premium). La dimensione specifica di questo limite di utilizzo deve essere in grado di contenere i modelli di utilizzo dei database all'interno del gruppo. È previsto un certo grado di overcommitt del gruppo, poiché il pool in genere presuppone schemi di utilizzo a freddo e a caldo per i database, in cui tutti i database non raggiungono il picco contemporaneamente.<br> **Su quale valore deve essere impostato il limite di utilizzo delle eDTU?** <br> Impostare il valore MAX eDTU o il limite di utilizzo delle eDTU per database su ([picco di utilizzo del database]). Si supponga, ad esempio, che il picco di utilizzo per database sia 50 DTU e che solo il 20% dei 100 database nel gruppo raggiunga il picco contemporaneamente. Se il limite di utilizzo delle eDTU per database è impostato su 50 eDTU, è ragionevole eseguire l’overcommit del pool moltiplicando per 5 e impostare la garanzia eDTU per il gruppo (eDTU POOL) su 1.000 eDTU. Vale inoltre la pena notare che il limite di utilizzo delle eDTU non è una garanzia di risorse per un database, bensì un limite massimo di eDTU che è possibile raggiungere, se disponibile. |

## Pool di database elastici consigliati

Passare a un server SQL Database V12 e sarà possibile visualizzare un messaggio che informa che sono presenti pool di database elastici consigliati per il server.

Esattamente come per le raccomandazioni relative al piano tariffario dei pool di database elastici, i pool consigliati sono preconfigurati con gli elementi seguenti già impostati:

- Piano tariffario del pool.
- Quantità di eDTU del pool appropriata.
- Impostazioni di eDTU min/max di database.  
- Elenco di database consigliati.

### Creare un pool consigliato

1. Fare clic sul messaggio per visualizzare un elenco dei pool consigliati:
 
     ![pool consigliati][12]
  
1. Fare clic su un pool per visualizzare le impostazioni di raccomandazione dettagliate.
2. È sufficiente modificare il nome del pool e scegliere **OK** per creare il pool. (I pool consigliati non possono essere modificati fino a dopo la creazione).

    ![pool consigliato][11]


## Aggiungere e rimuovere i database dal pool

Dopo aver creato il pool, è possibile aggiungere database al pool o rimuoverli selezionando o deselezionando i database nella pagina **Aggiungi database**.

Dopo la creazione di un pool è anche possibile utilizzare Transact-SQL per creare nuovi database elastici nel pool e per spostare i database esistenti da e verso un pool. Per informazioni dettagliate, vedere [Riferimento al pool di database elastici - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*


## Monitorare e gestire un pool di database elastici

Dopo aver creato un pool di database elastici, è possibile monitorare e gestire il pool nel portale passando all'elenco dei pool esistenti e selezionando il pool desiderato.

Dopo aver creato un pool, è possibile effettuare le seguenti operazioni:

- Selezionare **Configura pool** per modificare le impostazioni relative alle eDTU del pool e alle eDTU per database.
- Selezionare **Crea processo** e gestire i database nel pool mediante la creazione di processi elastici. I processi elastici consentono l'esecuzione di script Transact-SQL su qualsiasi numero di database nel pool. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).
- Selezionare **Gestione processi** per amministrare i processi elastici esistenti.



![Monitorare un pool elastico][8]




![Monitorare un pool elastico][4]

Quando si seleziona un pool esistente, è possibile visualizzare l'utilizzo delle risorse del pool. Fare clic sul grafico **Utilizzo delle risorse** per aprire il pannello **Metrica**, nel quale è possibile personalizzare il grafico e configurare gli avvisi.


![utilizzo delle risorse][6]

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
[1]: ./media/sql-database-elastic-pool-portal/new-elastic-pool.png
[2]: ./media/sql-database-elastic-pool-portal/configure-elastic-pool.png
[3]: ./media/sql-database-elastic-pool-portal/configure-performance.png
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[5]: ./media/sql-database-elastic-pool-portal/add-databases.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[8]: ./media/sql-database-elastic-pool-portal/configure-pool.png
[9]: ./media/sql-database-elastic-pool-portal/pricing-tier.png
[10]: ./media/sql-database-elastic-pool-portal/star.png
[11]: ./media/sql-database-elastic-pool-portal/recommended-pool.png
[12]: ./media/sql-database-elastic-pool-portal/pools-message.png

<!---HONumber=Nov15_HO2-->