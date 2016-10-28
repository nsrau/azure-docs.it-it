<properties
	pageTitle="Panoramica del Estensione database | Microsoft Azure"
	description="Informazioni su come Estensione database esegue la migrazione dei dati non attivi in modo trasparente e sicuro al cloud di Microsoft Azure."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/27/2016"
	ms.author="douglasl"/>

# Panoramica del Estensione database

Estensione database esegue la migrazione dei dati non attivi in modo trasparente e sicuro al cloud di Microsoft Azure.

Per iniziare subito a usare l'estensione database, vedere [Introduzione all'esecuzione della procedura guidata Abilitare il database per l'estensione](sql-server-stretch-database-wizard.md).

## Vantaggi del Estensione database
Il Estensione database offre i vantaggi seguenti:

### Disponibilità conveniente per i dati usati raramente
I dati transazionali usati di frequente e raramente vengono estesi dinamicamente da SQL Server a Microsoft Azure con Estensione database di SQL Server. A differenza dell'archiviazione dati non attivi, i dati sono sempre online e disponibili per le query. È possibile fornire più sequenze temporali di conservazione dei dati senza interrompere la banca dati per le tabelle di grandi dimensioni come la cronologia degli ordini dei clienti. È possibile trarre vantaggio dal costo contenuto di Azure invece di ridimensionare una costosa risorsa di archiviazione locale. Per mantenere il controllo sui costi, si sceglie il piano tariffario e si configurano le impostazioni nel portale di Azure. Passare a un piano superiore o inferiore secondo le esigenze. Per informazioni dettagliate, vedere la pagina [Prezzi di Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Non richiede modifiche a query o applicazioni
Possibilità di accedere facilmente ai dati di SQL Server indipendentemente dal fatto che siano locali o con estensione nel cloud. Si impostano i criteri che determinano dove vengono archiviati i dati e SQL Server gestisce lo spostamento dei dati in background. L'intera tabella è sempre online e disponibile per le query. Estensione database non richiede modifiche apportate alle query o alle applicazioni esistenti, il percorso dei dati è completamente trasparente per l'applicazione.

### Gestione semplificata dei dati locali
Riduzione delle attività di gestione e archiviazione locale dei dati. I backup per i dati in locale vengono eseguiti più velocemente e completati entro la finestra di manutenzione. I backup per la parte cloud dei dati vengono eseguiti automaticamente. Le esigenze di archiviazione locale sono notevolmente ridotte. Archiviazione di Azure può essere dell'80% meno costosa dell'aggiunta di un'unità SSD locale.

### Protezione dei dati anche durante la migrazione
L'estensione delle applicazioni più importanti nel cloud viene eseguita in modo sicuro. Crittografia sempre attiva di SQL Server fornisce la crittografia per i dati in movimento. La sicurezza a livello di riga e altre funzionalità di sicurezza avanzate di SQL Server sono utilizzabili anche con Estensione database per proteggere i dati.

## Funzionalità del Estensione database
Dopo avere abilitato l'estensione database per un'istanza di SQL Server, un database e almeno una tabella, l'estensione database inizia automaticamente la migrazione dei dati usati raramente in Azure.

-   Se si archiviano dati inattivi in una tabella separata, è possibile eseguire la migrazione dell'intera tabella.

-   Se la tabella contiene sia dati usati molto di frequente che dati usati raramente è possibile specificare una funzione di filtro per selezionare le righe per la migrazione.

**Non è necessario modificare le query e le app client esistenti.** Si continuerà ad avere accesso trasparente ai dati locali e remoti, anche durante la migrazione dei dati. Esiste una piccola latenza per le query remote, ma questa latenza si verifica solo quando si esegue una query sui dati cronologici.

**L'estensione database assicura che nessun dato vada perso** se si verifica un errore durante la migrazione. È disponibile anche una logica di ripetizione dei tentativi per gestire i problemi di connessione che possono verificarsi durante la migrazione. Una vista a gestione dinamica fornisce lo stato della migrazione.

**È possibile sospendere la migrazione dei dati** per risolvere i problemi nel server locale o per ottimizzare la larghezza di banda di rete disponibile.

![Panoramica del Estensione database][StretchOverviewImage1]

## Esigenze soddisfatte dal Estensione database
Se è possibile fare le seguenti affermazioni, Estensione database può soddisfare i requisiti e risolvere i problemi specifici.

|L'utente è un decision maker.|L'utente è un amministratore di database.|
|------------------------------|-------------------|
|Si devono mantenere i dati transazionali per molto tempo.|Le dimensioni delle tabelle sono fuori controllo.|
|A volte è necessario eseguire query sui dati cronologici.|Gli utenti dicono di voler accedere ai dati cronologici, ma li usano solo raramente.|
|Sono disponibili app, incluse app meno recenti, che non si vuole aggiornare.|È necessario continuare ad acquistare e aggiungere altro spazio di archiviazione.|
|Si vuole trovare un modo per risparmiare sull'archiviazione.|Non è possibile eseguire il backup o ripristino di tali tabelle di grandi dimensioni entro i limiti del contratto di servizio.|

## Tipi di database e tabelle candidati per il Estensione database
Estensione database è destinato a database transazionali con grandi quantità di dati cronologici, in genere archiviati in un numero limitato di tabelle. Queste tabelle possono contenere più di un miliardo di righe.

Se si usa la funzionalità tabella temporale di SQL Server 2016, usare il Estensione database per eseguire la migrazione di tutta o parte della tabella della cronologia associata per un'archiviazione economica in Azure. Per altre informazioni, vedere [Manage Retention of Historical Data in System-Versioned Temporal Tables](https://msdn.microsoft.com/library/mt637341.aspx) (Gestire la conservazione dei dati cronologici nelle tabelle temporali con controllo delle versioni di sistema).

Per identificare i database e le tabelle candidati per il Estensione database, usare l'ottimizzazione guidata Estensione database di SQL Server 2016 Upgrade Advisor. Per altre informazioni, vedere [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (Identificare database e tabelle per l'estensione database). Per altre informazioni sui possibili problemi di blocco, vedere [Limitazioni di Estensione database](sql-server-stretch-database-limitations.md).

## Test Drive del Estensione database
**Test Drive del Estensione database con il database di esempio AdventureWorks.** Per ottenere il database di esempio AdventureWorks, scaricare almeno il file di database e il file di script ed esempi [qui](https://www.microsoft.com/download/details.aspx?id=49502). Dopo aver ripristinato il database di esempio per un'istanza di SQL Server 2016, decomprimere il file degli esempi e aprire il file Stretch DB Samples dalla cartella Stretch DB. Eseguire gli script in questo file per controllare lo spazio usato dai dati prima e dopo aver abilitato il Estensione database per tenere traccia dell'avanzamento della migrazione dei dati e per verificare se è possibile continuare a eseguire query sui dati esistenti e inserire nuovi dati durante e dopo la migrazione dei dati.

## Passaggio successivo
**Identificare database e tabelle candidati per il Estensione database.** Scaricare SQL Server 2016 Upgrade Advisor ed eseguire l'ottimizzazione guidata Estensione database per identificare i database e le tabelle candidati per il Estensione database. L'ottimizzazione guidata Estensione database consente anche di identificare i problemi di blocco. Per altre informazioni, vedere [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (Identificare database e tabelle per l'estensione database).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

<!----HONumber=AcomDC_0629_2016-->