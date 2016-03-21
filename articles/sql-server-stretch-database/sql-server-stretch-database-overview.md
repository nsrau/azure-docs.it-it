<properties
	pageTitle="Panoramica del Database Estensione | Microsoft Azure"
	description="Informazioni sul modo in cui il Database Estensione esegue la migrazione dei dati cronologici in modo trasparente e sicuro al cloud di Microsoft Azure."
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
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Panoramica del Database Estensione

Il Database Estensione esegue la migrazione dei dati cronologici in modo trasparente e sicuro al cloud di Microsoft Azure.

Per iniziare subito a usare l'estensione database, vedere [Introduzione all'esecuzione della procedura guidata Abilitare il database per l'estensione](sql-server-stretch-database-wizard.md).

## Vantaggi del Database Estensione
Il Database Estensione offre i vantaggi seguenti:

**Fornisce disponibilità conveniente per i dati non attivi.** I dati transazionali attivi e non attivi vengono estesi dinamicamente da SQL Server a Microsoft Azure con il Database Estensione SQL Server. A differenza dell'archiviazione dati non attivi, i dati sono sempre online e disponibili per le query. È possibile fornire più sequenze temporali di conservazione dei dati senza interrompere la banca dati per le tabelle di grandi dimensioni come la cronologia degli ordini dei clienti. È possibile trarre vantaggio dal costo contenuto di Azure invece di ridimensionare una costosa risorsa di archiviazione locale. Per mantenere il controllo sulla velocità di accesso e sui prezzi, si sceglie il piano tariffario e si configurano le impostazioni nel portale di Azure. Passare a un piano superiore o inferiore secondo le esigenze. Per altri dettagli, vedere la pagina [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

**Non richiede modifiche a query o applicazioni.** È possibile accedere ai dati di SQL Server direttamente, sia in locale o estesi al cloud. Si impostano i criteri che determinano dove vengono archiviati i dati e SQL Server gestisce lo spostamento dei dati in background. L'intera tabella è sempre online e disponibile per le query. Database Estensione non richiede modifiche apportate alle query o alle applicazioni esistenti, il percorso dei dati è completamente trasparente per l'applicazione.

**Semplifica la gestione dei dati locali.** Viene ridotta la gestione e l'archiviazione in locale dei dati. I backup per la parte cloud dei dati vengono eseguiti automaticamente. I backup per i dati in locale vengono eseguiti più velocemente e completati entro la finestra di manutenzione. Le esigenze di archiviazione locale sono notevolmente ridotte. Archiviazione di Azure può essere dell'80% meno costosa dell'aggiunta di un'unità SSD locale.

**Protegge i dati anche durante la migrazione.** L'estensione nel cloud delle applicazioni più importanti può essere eseguita in modo sicuro e senza problemi. Crittografia sempre attiva di SQL Server fornisce la crittografia per i dati in movimento. La sicurezza a livello di riga e altre funzionalità di sicurezza avanzate di SQL Server sono utilizzabili anche con Database Estensione per proteggere i dati.

## Funzionalità del Database Estensione
Dopo avere abilitato il Database Estensione per un'istanza di SQL Server, un database e almeno una tabella, inizia automaticamente la migrazione dei dati cronologici in Azure.

-   Se si archiviano dati cronologici in una tabella separata, è possibile eseguire la migrazione dell'intera tabella.

-   Se la tabella contiene dati attuali e cronologici, è possibile specificare un predicato del filtro per selezionare le righe di cui eseguire la migrazione.

Database Estensione assicura che nessun dato venga perso se si verifica un errore durante la migrazione. È disponibile anche una logica di ripetizione dei tentativi per gestire i problemi di connessione che possono verificarsi durante la migrazione. Una vista a gestione dinamica fornisce lo stato della migrazione.

È possibile sospendere la migrazione dei dati per risolvere i problemi nel server locale o per ottimizzare la larghezza di banda di rete disponibile.

Non è necessario modificare le query e le app client esistenti. Si continuerà ad avere accesso trasparente ai dati locali e remoti, anche durante la migrazione dei dati. Esiste una piccola quantità di latenza per le query remote, ma questa latenza si verifica solo quando si esegue una query sui dati cronologici.

![Panoramica del Database Estensione][StretchOverviewImage1]

## Esigenze soddisfatte dal Database Estensione
Se è possibile fare le seguenti affermazioni, Database estensione può soddisfare i requisiti e risolvere i problemi specifici.

|L'utente è un decision maker.|L'utente è un amministratore di database.|
|------------------------------|-------------------|
|Si devono mantenere i dati transazionali per molto tempo.|Le dimensioni delle tabelle sono fuori controllo.|
|A volte è necessario eseguire query sui dati cronologici.|Gli utenti dicono di voler accedere ai dati cronologici, ma li usano solo raramente.|
|Sono disponibili app, incluse app meno recenti, che non si vuole aggiornare.|È necessario continuare ad acquistare e aggiungere altro spazio di archiviazione.|
|Si vuole trovare un modo per risparmiare sull'archiviazione.|Non è possibile eseguire il backup o ripristino di tali tabelle di grandi dimensioni entro i limiti del contratto di servizio.|

## Tipi di database e tabelle candidati per il Database estensione
Il Database Estensione è destinato a database transazionali con grandi quantità di dati cronologici, in genere archiviati in un numero limitato di tabelle. Queste tabelle possono contenere più di un miliardo di righe.

Se si usa la funzionalità tabella temporale di SQL Server 2016, usare il Database Estensione per eseguire la migrazione di tutta o parte della tabella della cronologia associata per un'archiviazione economica in Azure. Per altre informazioni, vedere l'articolo su come [gestire la conservazione dei dati cronologici nelle tabelle temporali con controllo delle versioni di sistema](https://msdn.microsoft.com/library/mt637341.aspx).

Per identificare i database e le tabelle candidati per il Database Estensione, usare l'ottimizzazione guidata Database Estensione di SQL Server 2016 Upgrade Advisor. Per altre informazioni, vedere l'articolo relativo all'[identificazione di database e tabelle per l'estensione database](sql-server-stretch-database-identify-databases.md). Per altre informazioni sui possibili problemi di blocco, vedere l'articolo relativo alle [limitazioni della superficie di attacco e problemi di blocco per l'estensione database](sql-server-stretch-database-limitations.md).

## <a name="FAQ"></a>Domande frequenti sull'estensione database
**Il Database Estensione funziona con & lt;Nome funzionalità di SQL Server& gt;?**
-   Per un elenco delle funzionalità di SQL Server che rendono una tabella non idonea per l'estensione, vedere l'articolo relativo alle [limitazioni della superficie di attacco e problemi di blocco per l'estensione database](sql-server-stretch-database-limitations.md).

-   È possibile scaricare SQL Server 2016 Upgrade Advisor ed eseguire l'ottimizzazione guidata Database Estensione per identificare i database e le tabelle candidati per il Database Estensione. Per altre informazioni, vedere l'articolo relativo all'[identificazione di database e tabelle per l'estensione database](sql-server-stretch-database-identify-databases.md).

**È possibile usare come destinazione un'altra istanza di SQL Server locale per il Database Estensione?** No. Il Database estensione non supporta un'altra istanza di SQL Server locale come endpoint remoto.

**È possibile disabilitare l'estensione e ripristinare i dati di cui è stata effettuata la migrazione nella tabella locale?** Sì. Per altre informazioni, vedere l'articolo relativo alla [disabilitazione dell'estensione database e recupero dei dati remoti](sql-server-stretch-database-disable.md).

## Termini
**Database locale**. Il database SQL Server locale.

**Endpoint remoto**. Posizione in Microsoft Azure che contiene i dati del database remoto.

**Dati locali**. Dati in un database con abilitato il Database Estensione che non saranno spostati in Azure in base alla configurazione del Database Estensione per le tabelle del database.

**Dati idonei**. Dati in un database con abilitato il Database Estensione che non sono ancora stati spostati in Azure in base alla configurazione del Database Estensione per le tabelle del database.

**Dati remoti**. Dati in un database con abilitato Database Estensione che sono già stati spostati in Azure.

## Architettura
Database Estensione sfrutta le risorse di Microsoft Azure per l'offload dell'archiviazione dei dati e l'elaborazione delle query.

Quando si abilita il Database Estensione in un database, crea una definizione di server collegato sicuro nel Server SQL locale. L'endpoint remoto è la destinazione di questa definizione di server collegato. Quando si abilita il Database Estensione in una tabella nel database, effettua il provisioning di risorse remote e inizia a eseguire la migrazione di dati idonei, se la migrazione è abilitata.

Le query sulle tabelle con abilitato il Database Estensione vengono eseguite automaticamente sia sul database locale che sull'endpoint remoto. Il Database Estensione sfrutta la capacità di elaborazione di Azure per eseguire query su dati remoti riscrivendo la query. È possibile visualizzare questo riscrittura come un operatore di "query remota" nel nuovo piano di query.

![Architettura del Database Estensione][StretchOverviewImage2]

## Sicurezza e autorizzazioni

### Abilitazione e disabilitazione del Database Estensione per un'istanza di SQL Server
Per iniziare a configurare i database per il Database Estensione, è prima di tutto necessario modificare l'opzione di configurazione a livello di istanza "archivio dati remoto" tramite sp\_configure. Per eseguire questa operazione è necessario avere privilegi a livello sysadmin o serveradmin. Con questa opzione abilitata è possibile configurare i database per il Database Estensione, eseguire la migrazione di dati ed eseguire query sui dati nell'endpoint remoto.

### Abilitazione e disabilitazione del Database Estensione per un database o una tabella
Per configurare un database per il Database Estensione, è necessario avere l'autorizzazione di CONTROLLO DEL DATABASE. È anche necessario fornire le credenziali di amministratore per l'endpoint remoto.

Per configurare una tabella per il Database Estensione, è necessario il privilegio di MODIFICA sulla tabella e il database deve già essere configurato per il Database Estensione.

### Accesso ai dati
Solo i processi di sistema possono accedere alla definizione del server collegato del Database Estensione. Gli account di accesso utente non possono eseguire query sull'endpoint remoto tramite la definizione di server collegato.

Il Database Estensione non modifica il modello di autorizzazioni di un database esistente. Gli account di accesso utente possono eseguire query sui dati in una tabella con abilitato il Database Estensione tramite il database locale. Il database locale esegue controlli delle autorizzazione per tutte le azioni avviate dall'utente così come avviene per tutti gli altri oggetti. Se si ha l'autorizzazione per accedere alla tabella con abilitato il Database Estensione, sarà possibile accedere a tutto il relativo contenuto per cui si è autorizzati, indipendentemente dalla posizione fisica dei dati.

![Modello di accesso ai dati del Database Estensione][StretchOverviewImage3]

## Test Drive del Database Estensione
**Test Drive del Database Estensione con il database di esempio AdventureWorks.** Per ottenere il database di esempio AdventureWorks, scaricare almeno il file di database e il file di script e gli esempi [qui](https://www.microsoft.com/download/details.aspx?id=49502). Dopo aver ripristinato il database di esempio per un'istanza di SQL Server 2016, decomprimere il file degli esempi e aprire il file Stretch DB Samples dalla cartella Stretch DB. Eseguire gli script in questo file per controllare lo spazio usato dai dati prima e dopo aver abilitato il Database Estensione per tenere traccia dell'avanzamento della migrazione dei dati e per verificare se è possibile continuare a eseguire query sui dati esistenti e inserire nuovi dati durante e dopo la migrazione dei dati.

## Passaggio successivo
**Identificare database e tabelle candidati per il Database Estensione.** Scaricare SQL Server 2016 Upgrade Advisor ed eseguire l'ottimizzazione guidata Database Estensione per identificare i database e le tabelle candidati per il Database Estensione. L'ottimizzazione guidata Database Estensione consente anche di identificare i problemi di blocco. Per altre informazioni, vedere l'articolo relativo all'[identificazione di database e tabelle per l'estensione database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

<!---HONumber=AcomDC_0309_2016-->