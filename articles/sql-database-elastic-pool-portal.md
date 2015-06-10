<properties 
	pageTitle="Creare e gestire un pool di database elastico di database SQL (anteprima)" 
	description="Creare un singolo pool di risorse da condividere tra un gruppo di database SQL di Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/29/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Creare e gestire un pool elastico di database SQL (anteprima)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

In questo articolo viene illustrato come creare un pool elastico con il [portale di Azure](https://portal.azure.com).

I pool elastici semplificano il processo di creazione, manutenzione e gestione delle prestazioni e dei costi per un numero elevato di database.
 

> [AZURE.NOTE]I pool elastici sono attualmente in anteprima e sono disponibili unicamente con i server di database SQL V12.

 


## Prerequisiti

Per creare un pool elastico sono necessari i seguenti requisiti:

- Una sottoscrizione ad Azure. Se si necessita di una sottoscrizione ad Azure, fare clic su VERSIONE DI VALUTAZIONE GRATUITA nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Un server di database SQL di Azure versione 12. Se non si dispone di un server V12, crearne uno seguendo i passaggi in: [Creare il primo database SQL di Azure](sql-database-get-started.md).



## Creare un pool elastico

Creare un pool elastico aggiungendo un nuovo pool a un server. È possibile aggiungere più pool a un server, ma solo un server può essere associato a ciascun pool. Inoltre, tutti o alcuni dei database in un server possono essere aggiunti a un pool.


1.	Selezionare un server di database SQL V12 contenente i database che si desidera aggiungere al pool.
2.	Creare il pool selezionando **aggiungi pool** nella parte superiore del pannello **SQL Server**.

   ![Creare un pool elastico][1]

## Configurare un pool elastico

Configurare il pool impostando il livello di prezzo, aggiungendo i database e configurando le caratteristiche di prestazioni del pool.

*Quando si seleziona il comando **Aggiungi pool** è necessario accettare le condizioni dell’anteprima selezionando **ANTEPRIMA CONDIZIONI** e completando il pannello **Anteprima condizioni**. È sufficiente accettare le condizioni una volta per ogni sottoscrizione.*

   ![Configurare un pool elastico][2]


### Pricing tier

Il livello di prezzo di un pool elastico è per alcuni versi analogo al livello di servizio di un database SQL. Il livello di prezzo determina le funzionalità disponibili per i database elastici nel pool e il numero massimo di DTU (MAX DTU) e la memoria (GB) disponibili per ciascun database.

> [AZURE.NOTE]L'anteprima è attualmente limitata al livello di prezzo **Standard**.

| Piano tariffario | MAX DTU per database |
| :--- | :--- |
| Standard | Massimo di 100 DTU per database |

### Aggiungere database

In qualsiasi momento, è possibile selezionare i database specifici che si desidera includere nel pool. Quando si crea un nuovo pool, Azure consiglia i database che beneficeranno della presenza in un pool e li contrassegna per l'inclusione. È possibile aggiungere tutti i database disponibili nel server oppure è possibile selezionare o deselezionare i database dall'elenco iniziale come desiderato.

   ![Aggiungere database][5]

Quando si seleziona un database da aggiungere a un pool, è necessario soddisfare le condizioni seguenti:

- Il pool deve disporre di spazio sufficiente per il database (non può già contenere il numero massimo di database). In particolare, il pool deve disporre di un numero di DTU sufficiente a coprire la garanzia DTU per ogni database (ad esempio, se la garanzia di DTU per il gruppo è 400, e la garanzia di DTU per ciascun database è 10, il numero massimo di database consentiti nel pool è 40 (400 DTU/10 DTU garantite per database = massimo 40 database).
- Le funzionalità correnti utilizzate dal database devono essere disponibili nel pool. 


### Configurare le prestazioni

È possibile configurare le prestazioni del pool di impostando i parametri delle prestazioni per il pool e per i database elastici nel pool. Tenere presente che le **impostazioni dei database elastici** si applicano a tutti i database nel pool.

   ![Configurare un pool elastico][3]

È possibile impostare tre parametri che definiscono le prestazioni per il pool: la garanzia di DTU per il pool e il numero MIN e MAX di DTU per i database elastici nel pool. Nella tabella seguente vengono descritti singolarmente e vengono fornite alcune indicazioni su come impostarli.

| Parametro di prestazioni | Descrizione |
| :--- | :--- |
| **POOL DTU/GB** - Garanzia DTU per il pool | La garanzia DTU per il pool è il numero garantito di DTU disponibili e condivise da tutti i database nel pool. <br> Attualmente, questo parametro può essere impostato su 200, 400, 800 o 1200. <br> È necessario eseguire il provisioning della dimensione della garanzia DTU specificata per un gruppo considerando l'utilizzo di DTU cronologico del gruppo. In alternativa, è possibile impostare questa dimensione mediante la garanzia DTU desiderata per database e l'utilizzo dei database attivi contemporaneamente. La garanzia DTU per il pool, inoltre, è correlata alla quantità di spazio di archiviazione disponibile per il pool, per ogni DTU allocata al pool si ottiene 1 GB di spazio di archiviazione del database (1 DTU = 1 GB di spazio di archiviazione). <br> **Su quale valore deve essere impostata la garanzia DTU del pool?** <br>Come minimo, è necessario impostare la garanzia DTU del pool su ([numero dei database] x [utilizzo medio DTU per database]) |
| **MIN DTU** - Garanzia DTU per ciascun database | La garanzia DTU per database è il numero di DTU garantito a un singolo database del pool. Attualmente, è possibile impostare questa garanzia su 0, 10, 20 o 50 DTU oppure è possibile scegliere di non fornire una garanzia ai database nel gruppo (MIN DTU = 0). <br> **Su quale valore deve essere impostata la garanzia DTU per database?** <br> Come minimo, è necessario impostare la garanzia DTU del pool (MIN DTU) su ([utilizzo medio per database]). La garanzia DTU per database è un'impostazione globale che consente di impostare la garanzia DTU per tutti i database nel pool. |
| **MAX DTU** - Limite di utilizzo delle DTU per database | Il valore MAX DTU per database è il numero massimo di DTU che un singolo database del pool può utilizzare. Impostare il limite di utilizzo DTU per database su un valore sufficientemente elevato per gestire burst massimi o picchi che potrebbero verificarsi nei database. È possibile impostare questo limite di utilizzo sul limite di sistema, che dipende dal livello di prezzo del pool di (100 DTU per il livello Standard). La dimensione specifica di questo limite di utilizzo deve essere in grado di contenere i modelli di utilizzo dei database all'interno del gruppo. È previsto un certo grado di overcommitt del gruppo, poiché il pool in genere presuppone schemi di utilizzo a freddo e a caldo per i database, in cui tutti i database non raggiungono il picco contemporaneamente.<br> **Su quale valore deve essere impostato il limite di utilizzo delle DTU?** <br> Impostare il valore MAX DTU o il limite di utilizzo delle DTU per database su ([picco di utilizzo del database]). Si supponga, ad esempio, che il picco di utilizzo per database sia 50 DTU e che solo il 20% dei 100 database nel gruppo raggiunga il picco contemporaneamente. Se il limite di utilizzo delle DTU per database è impostato su 50 DTU, è ragionevole eseguire l’overcommit del gruppo moltiplicando per 5 e impostare la garanzia DTU per il gruppo su 1.000 DTU. Vale inoltre la pena notare che il limite di utilizzo delle DTU non è una garanzia di risorse per un database, bensì un limite massimo di DTU che è possibile raggiungere, se disponibile. |


## Aggiunta di database a un pool elastico

Dopo aver creato il pool, è possibile aggiungere i database al pool o rimuoverli da questo


## Monitorare e gestire un pool elastico

Dopo aver creato un pool elastico, è possibile monitorare e gestire il pool nel portale passando all'elenco dei pool esistenti e selezionando il pool desiderato.

Dopo aver creato un pool, è possibile effettuare le seguenti operazioni:

- Selezionare **Configura pool** per modificare le impostazioni relative alle DTU del pool e alle DTU per database.
- Selezionare **Crea processo** e gestire i database nel pool mediante la creazione di processi elastici. I processi elastici facilitano l’esecuzione di script T-SQL su qualsiasi numero di database nel pool. Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).
- Selezionare **Gestione processi** per amministrare i processi elastici esistenti.



![Monitorare un pool elastico][8]




![Monitorare un pool elastico][4]

Quando si seleziona un pool esistente, è possibile visualizzare l'utilizzo delle risorse del pool. Fare clic sul grafico **Utilizzo delle risorse** per aprire il pannello **Metrica**, nel quale è possibile personalizzare il grafico e configurare gli avvisi.


![utilizzo delle risorse][6]

Fare clic su **Modifica grafico** per aggiungere parametri in modo da poter visualizzare facilmente i dati di telemetria per il pool.


![modifica grafico][7]







## Passaggi successivi
Dopo aver creato un pool elastico, è possibile gestire i database nel pool mediante la creazione di processi elastici. I processi elastici facilitano l’esecuzione di script T-SQL su qualsiasi numero di database nel pool.

Per ulteriori informazioni, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).



## Risorse correlate

- [Pool elastico di database SQL](sql-database-elastic-pool.md)
- [Creare un pool elastico di database SQL con PowerShell](sql-database-elastic-pool-powershell.md)
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

<!---HONumber=58-->