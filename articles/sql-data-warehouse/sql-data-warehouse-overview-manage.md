<properties
   pageTitle="Gestire i database in Azure SQL Data Warehouse | Microsoft Azure"
   description="Panoramica della gestione dei database di SQL Data Warehouse. Include strumenti di gestione, prestazioni di DWU e di scalabilità orizzontale, risoluzione dei problemi di prestazioni delle query, definizione dei criteri di protezione e ripristino di un database da un danneggiamento dei dati o da un'interruzione dell'alimentazione locale."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/04/2016"
   ms.author="barbkess;sonyama;"/>

# Gestire i database in Azure SQL Data Warehouse

SQL Data Warehouse automatizza molti aspetti della gestione dei database. Ad esempio, per aumentare le prestazioni è necessario solo regolare e pagare il corretto livello di risorse di calcolo e lasciare che SQL Data Warehouse esegua tutte le operazioni di scalabilità orizzontale e scalabilità inversa.

Senza dubbio si vorrà monitorare il carico di lavoro per identificare le esigenze di prestazioni, nonché risolvere i problemi relativi a query con esecuzione prolungata. Sarà anche necessario eseguire alcune attività di sicurezza per gestire le autorizzazioni per gli utenti e gli accessi.

Questa panoramica illustra questi aspetti della gestione di SQL Data Warehouse.

- Strumenti di gestione
- Ridimensionare le risorse di calcolo
- Sospendere e riprendere
- Procedure consigliate per le prestazioni
- Monitoraggio delle query
- Sicurezza
- Backup e ripristino

## Strumenti di gestione

È possibile usare un'ampia gamma di strumenti per gestire i database in SQL Data Warehouse. Durante la gestione dei database si svilupperanno preferenze per determinati strumenti per ciascun tipo di attività da eseguire.

### Portale di Azure
Il [portale di Azure][] è un portale basato sul Web in cui è possibile creare, aggiornare ed eliminare i database e monitorare le risorse di database. Si tratta dello strumento ideale se si sta appena iniziando a usare Azure, se si gestisce un numero ridotto di database del data warehouse o se è necessario operare rapidamente.

Per un'introduzione al portale di Azure, vedere [Creare un SQL Data Warehouse][] (portale di Azure).

### SQL Server Data Tools in Visual Studio
[SQL Server Data Tools][] (SSDT) in Visual Studio consente di connettersi, gestire e sviluppare i database. È opportuno che gli sviluppatori di applicazioni con una certa familiarità con Visual Studio o altri ambienti di sviluppo integrato (IDE), provino a usare SSDT in Visual Studio.

SSDT include Esplora oggetti di SQL Server., che consente di visualizzare, connettersi ed eseguire gli script su database di SQL Data Warehouse. Per connettersi rapidamente a SQL Data Warehouse, è sufficiente fare clic sul pulsate **Apri in Visual Studio** sulla barra dei comandi quando i dettagli del database sono visualizzati nel portale di Azure classico.

Per iniziare a usare SSDT in Visual Studio, vedere [Connettersi a SQL Data Warehouse con Visual Studio][].

### Strumenti da riga di comando
Gli strumenti da riga di comando sono ideali per l'automazione dei carichi di lavoro. PowerShell e sqlcmd sono due ottimi modi per automatizzare i processi. È consigliabile servirsi di questi strumenti per gestire un numero elevato di server logici e distribuire le modifiche relative alle risorse in un ambiente di produzione, in quanto è possibile scrivere gli script per le attività necessarie e quindi automatizzare tali attività.

### Viste a gestione dinamica 

Le viste a gestione dinamica sono alla base della gestione di SQL Data Warehouse. Quasi tutte le informazioni che emergono nel portale si basano sulle viste a gestione dinamica. Per visualizzare un elenco di viste a gestione dinamica di SQL Data Warehouse, vedere [Viste di sistema][] di SQL Data Warehouse.

Per iniziare, vedere [Connettersi ed eseguire query con SQLCMD][] e [Creare un database (PowerShell)][].

## Ridimensionare le risorse di calcolo

In SQL Data Warehouse, è possibile aumentare o ridurre rapidamente le prestazioni aumentando o riducendo le risorse di calcolo di CPU, memoria e larghezza di banda di I/O. Per ridimensionare le prestazioni, è sufficiente modificare il numero di Unità Data Warehouse (DWU) allocate al proprio database da SQL Data Warehouse. SQL Data Warehouse apporta rapidamente la modifica e gestisce tutte le modifiche sottostanti all'hardware o al software.

Per altre informazioni sulla scalabilità delle DWU, vedere la sezione [Scalare le prestazioni][].

##  Sospendere e riprendere

Per ridurre i costi, è possibile sospendere e riprendere le risorse di calcolo su richiesta. Ad esempio, se non si usa il database durante la notte e nei fine settimana, è possibile sospenderlo in questi intervalli di tempo e riprenderne l'esecuzione durante il giorno. Mentre il database è sospeso, non verranno addebitate DWU.

Per altre informazioni, vedere le sezioni [Sospendere il calcolo][] e [Riprendere il calcolo][].

## Procedure consigliate per le prestazioni

Quando si inizia a usare una nuova tecnologia, scoprire i suggerimenti e consigli che meglio funzionano da subito consente di risparmiare molto tempo. Sono disponibili procedure consigliate in molti di questi argomenti.

Per vedere molti riepiloghi delle considerazioni più importanti nello sviluppo di un carico di lavoro, vedere [Procedure consigliate per SQL Data Warehouse][].

## Monitoraggio delle query

A volte una query è in esecuzione da troppo tempo, ma non si è certi di quale sia la causa del problema. SQL Data Warehouse offre viste a gestione dinamica (DMV) che è possibile usare per trovare la query la cui esecuzione richiede troppo tempo.

Per trovare le query con esecuzione prolungata, vedere [Monitoraggio del carico di lavoro mediante DMV][].

## Sicurezza

Per mantenere un sistema sicuro, è necessario vigilare e proteggersi da qualsiasi tipo di accesso non autorizzato. Un sistema di sicurezza deve accertare che siano state applicate le regole del firewall, in modo che solo gli indirizzi IP autorizzati possano connettersi. È necessaria l'autenticazione corretta delle credenziali dell'utente. Dopo che un utente si è connesso al database, l'utente deve avere le autorizzazioni solo per eseguire un numero minimo di azioni. Per proteggere i dati, è possibile usare la crittografia. È anche importante predisporre azioni di controllo e rilevamento, in modo che sia possibile ripercorrere gli eventi in caso di eventuali attività sospette.

Per altre informazioni sulla gestione della sicurezza, vedere la [Panoramica della sicurezza][].

## Backup e ripristino

È possibile ripristinare un database in due modi. Se alcuni dati sono danneggiati nel database o si è verificato un errore, è possibile ripristinare uno snapshot del database. In caso di una completa interruzione del servizio a livello locale o di una calamità che rende una delle aree non disponibile, è possibile creare di nuovo il database in un'altra area.

SQL Data Warehouse esegue automaticamente il backup del database a intervalli regolari. Per la pianificazione del backup dei dati e i criteri di conservazione, vedere la sezione [Affidabilità elevata][].

### Archiviazione con ridondanza geografica

Poiché SQL Data Warehouse separa il calcolo dall'archiviazione, tutti i dati vengono scritti direttamente in Archiviazione di Azure con ridondanza geografica (RA-GRS). Con l'archiviazione con ridondanza geografica i dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Sia nelle aree primarie che in quelle secondarie i dati vengono replicati tre volte tra domini di errore e domini di aggiornamento separati. In questo modo viene garantita la durabilità dei dati anche in caso di una completa interruzione del servizio a livello locale o di una calamità che rende una delle aree non disponibile. Per altre informazioni sull'archiviazione con accesso in lettura e ridondanza geografica, vedere le [opzioni di ridondanza dell'archiviazione di Azure][].

### Ripristino del database

La funzionalità di ripristino del database è stata progettata per ripristinare il database da un punto precedente nel tempo. Il servizio SQL Data Warehouse protegge tutti i database eseguendo snapshot di archiviazione automatica almeno ogni 8 ore e li conserva per 7 giorni in modo da fornire un set discreto di punti di ripristino. Questi backup vengono archiviati in Archiviazione di Azure RA-GRS e pertanto offrono ridondanza geografica per impostazione predefinita. Le funzionalità di backup e ripristino automatici sono disponibili senza spese aggiuntive e consentono di proteggere i database da un danneggiamento o un'eliminazione accidentale a costo zero.

Per altre informazioni sul ripristino di database, vedere [Eseguire il ripristino da uno snapshot][].

### Ripristino geografico

Il ripristino geografico è progettato per ripristinare il database se diventa non disponibile a causa di un evento di arresto improvviso. È possibile contattare il supporto tecnico per ripristinare un database da un backup con ridondanza geografica e creare un nuovo database in un'area di Azure. Poiché il backup è con ridondanza geografica, è possibile usarlo per ripristinare un database anche se il database è inaccessibile a causa di un'interruzione del servizio. La funzionalità di ripristino geografico viene fornita senza alcun costo aggiuntivo.

Per usare la funzionalità di ripristino geografico, vedere [Eseguire il ripristino geografico da uno snapshot][].

## Passaggi successivi
L'uso di buoni principi di progettazione dei database semplificherà la gestione dei database in SQL Data Warehouse. Per altre informazioni, vedere la [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->
[opzioni di ridondanza dell'archiviazione di Azure]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Creare un SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Creare un database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Connettersi a SQL Data Warehouse con Visual Studio]: sql-data-warehouse-get-started-connect.md
[Connettersi ed eseguire query con SQLCMD]: sql-data-warehouse-get-started-connect-sqlcmd.md
[panoramica sullo sviluppo]: sql-data-warehouse-overview-development.md
[Eseguire il ripristino geografico da uno snapshot]: sql-data-warehouse-backup-and-geo-restore-from-snapshot.md
[Affidabilità elevata]: sql-data-warehouse-overview-expectations.md#high-reliability
[Monitoraggio del carico di lavoro mediante DMV]: sql-data-warehouse-manage-monitor.md
[Sospendere il calcolo]: sql-data-warehouse-overview-scalability.md#pause-compute-bk
[Eseguire il ripristino da uno snapshot]: sql-data-warehouse-backup-and-restore-from-snapshot.md
[Riprendere il calcolo]: sql-data-warehouse-overview-scalability.md#resume-compute-performance-bk
[Scalare le prestazioni]: sql-data-warehouse-overview-scalability.md#scale-performance-bk
[Panoramica della sicurezza]: sql-data-warehouse-overview-security.md
[Procedure consigliate per SQL Data Warehouse]: sql-data-warehouse-best-practices.md
[Viste di sistema]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[portale di Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0601_2016-->