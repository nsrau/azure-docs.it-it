<properties 
	pageTitle="Scalabilità dei servizi mobili supportati dal database SQL di Azure - Servizi mobili di Azure" 
	description="Informazioni su come diagnosticare e correggere problemi di scalabilità nei servizi mobili supportati da database SQL" 
	services="mobile-services" 
	documentationCenter="" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>
# Scalabilità dei servizi mobili supportati dal database SQL di Azure

Servizi mobili di Azure consente di sviluppare con la massima semplicità un'app che si connette a un back-end ospitato nel cloud e archivia i dati in un database SQL. Man mano che l'app cresce, per scalare le istanze del servizio è sufficiente modificare le impostazioni di scalabilità nel portale in modo da aggiungere capacità di calcolo e di rete. Per scalare il database SQL che supporta il servizio, tuttavia, sono necessarie alcune attività di pianificazione e monitoraggio proattive, perché il servizio riceve un carico maggiore. Questo documento illustra una serie di best practice mirate ad assicurare prestazioni ottimali ininterrotte per i servizi mobili supportati da SQL.

Questo argomento descrive le seguenti operazioni di base:

1. [Diagnostica dei problemi](#Diagnosing)
2. [Indicizzazione](#Indexing)
3. [Progettazione dello schema](#Schema)
4. [Progettazione delle query](#Query)
5. [Architettura del servizio](#Architecture)
6. [Risoluzione dei problemi avanzata](#Advanced)

<a name="Diagnosing"></a>
## Diagnostica dei problemi

Se si sospetta la presenza di problemi nel servizio mobile in condizioni di carico, il primo punto da esaminare è la scheda **Dashboard** del servizio nel [portale di gestione di Azure][]. In questa scheda, verificare che:

- Le metriche di uso che includono **Chiamate API** e **Dispositivi attivi** non indichino un superamento della quota.
- Lo stato di **monitoraggio degli endpoint** indichi che il servizio è attivo (disponibile solo se il servizio usa il livello Standard e il monitoraggio degli endpoint è abilitato). 

Se una delle condizioni precedenti non è vera, considerare la possibilità di modificare le impostazioni di scalabilità nella scheda *Scale*. Se il problema persiste, è possibile procedere e valutare se l'origine del problema può essere il database SQL di Azure. Le prossime sezioni illustrano alcuni approcci diversi per diagnosticare il problema.

### Scelta del livello corretto per il database SQL 

È importante comprendere i diversi livelli di database disponibili per assicurarsi di aver scelto il livello corretto in base alle esigenze dell'app. Il database SQL di Azure offre due diverse edizioni del database, con livelli diversi:

- Edizione Web e Business (ritirata)
- Edizione Basic, Standard e Premium 

Anche se le edizioni Web e Business sono completamente supportate, il servizio verrà interrotto entro il 24 aprile 2015, come illustrato in [Domande frequenti sull'interruzione delle edizioni Web e Business](http://msdn.microsoft.com/library/azure/dn741330.aspx). In preparazione per questa modifica, i nuovi clienti sono invitati a iniziare a usare le edizioni Basic, Standard e Premium. Questa nuova edizione offre numerosi nuovi livelli e funzionalità di monitoraggio, che agevolano la comprensione e la risoluzione dei problemi relativi alle prestazione del database. Tutti i nuovi servizi mobili vengono creati usando la nuova edizione.

Per convertire un servizio mobile che usa l'edizione Web e Business all'edizione Basic, Standard o Premium, seguire questa procedura.

1. Avviare il [portale di gestione di Azure][].
2. Selezionare **+NUOVO** nella barra degli strumenti, quindi **Servizi dati**, **Database SQL**, **Creazione rapida**.
3. Immettere un nome di database e quindi selezionare **Nuovo server di database SQL** nel campo **Server**. Verrà creato un server che usa la nuova edizione Basic, Standard e Premium. 
4. Compilare il resto dei campi e selezionare **Crea database SQL**. Verrà creato un database da 100 MB di livello Basic.
5. Configurare il servizio mobile per l'uso del database creato. Passare alla scheda **Configura** del servizio e selezionare **Cambia database** nella barra degli strumenti. Nella schermata successiva selezionare **Utilizza un database SQL esistente** nel campo **Database SQL**, quindi selezionare **Avanti**. Nella schermata successiva assicurarsi di selezionare il database creato nel passaggio 5, quindi selezionare **OK**.

Ecco alcuni consigli per la scelta del livello corretto per il database:

- **Basic**: usare nella fase di sviluppo o per piccoli servizi di produzione in cui si prevede l'esecuzione di un'unica query di database per volta
- **Standard**: usare per servizi di produzione in cui si prevede l'esecuzione di numerose query di database simultanee
- **Premium**: usare per servizi di produzione su vasta scala con numerose query simultanee, carico massimo e bassa latenza prevista per ogni richiesta.

Per altre informazioni sulle situazioni in cui usare ogni livello, vedere [Vantaggi dei livelli di servizio](http://msdn.microsoft.com/library/azure/dn369873.aspx#Reasons)

### Analisi delle metriche di database

Dopo aver acquisito familiarità con i diversi livelli di database, è possibile passare all'analisi delle metriche delle prestazioni del database, per comprendere la scalabilità all'interno dei livelli e tra di essi.

1. Avviare il [portale di gestione di Azure][].
2. Nella scheda Servizi mobili selezionare il servizio da usare.
3. Selezionare la scheda **Configura**.
4. Selezionare il nome del **Database SQL** nella sezione **Impostazioni database**. Verrà visualizzata la scheda del database SQL di Azure nel portale.
5. Passare alla scheda **Monitoraggio**
6. Verificare che siano visualizzate le metriche pertinenti usando il pulsante **Aggiungi metriche**. Includere quanto segue:
    - *Percentuale CPU* (disponibile solo nei livelli Basic/Standard/Premium)
    - *Percentuale di letture dati fisiche* (disponibile solo nei livelli Basic/Standard/Premium) 
    - *Percentuale di scritture nei log* (disponibile solo nei livelli Basic/Standard/Premium)
    - *Archiviazione* 
7. Esaminare le metriche nella finestra temporale in cui si sono verificati i problemi del servizio. 

    ![Azure Management Portal - SQL Database Metrics][PortalSqlMetrics]

Se qualsiasi metrica supera l'80% di uso per un periodo prolungato, il dato può indicare un problema di prestazioni. Per informazioni dettagliate sull'uso del database, vedere [Informazioni sull'uso delle risorse](http://msdn.microsoft.com/library/azure/dn369873.aspx#Resource).

Se le metriche indicano un uso elevato del database, come prima misura preventiva considerare la possibilità di **scalare verticalmente il database a un livello di servizio superiore**. Per risolvere immediatamente i problemi, considerare l'uso della scheda **Scala** del database per scalare verticalmente il database. L'operazione darà luogo ad addebiti aggiuntivi in fattura.

![Azure Management Portal - SQL Database Scale][PortalSqlScale]

Non appena possibile, considerare queste ulteriori procedure di prevenzione:

- **Ottimizzare il database.**
  Spesso è possibile ridurre l'uso del database ed evitare la necessità di passare a un livello superiore ottimizzando il database. 
- **Considerare l'architettura del servizio.**
  Spesso il carico del servizio non è distribuito in modo uniforme nel tempo, ma contiene picchi di richiesta elevata. Anziché scalare verticalmente il database per gestire i picchi, con il rischio che resti sottoutilizzato nei periodi di bassa richiesta, spesso è possibile modificare l'architettura del servizio in modo da evitare i picchi o gestirli senza bisogno di accedere al database.

Le sezioni successive di questo documento contengono linee guida personalizzate per l'implementazione delle misure preventive.


### Configurazione degli avvisi

Spesso è utile configurare avvisi per le metriche chiave del database, come misura preventiva per assicurarsi di disporre del tempo necessario per reagire in caso di esaurimento delle risorse. 

1. Passare alla scheda **Monitoraggio** del database per cui configurare gli avvisi
2. Verificare che siano visualizzate le metriche pertinenti, come descritto nella sezione precedente
3. Selezionare la metrica per cui configurare un avviso e quindi selezionare **Aggiungi regola**

    ![Azure Management Portal - SQL Alert][PortalSqlAddAlert]

4. Immettere un nome e una descrizione per l'avviso

    ![Azure Management Portal - SQL Alert Name and Descrizione][PortalSqlAddAlert2]

5. Specificare il valore da usare come soglia di avviso. È consigliabile impostare **80%** per disporre di un tempo di reazione sufficiente. Assicurarsi di specificare un indirizzo di posta elettronica controllato attivamente. 

    ![Azure Management Portal - SQL Alert Threshold and Email][PortalSqlAddAlert3]

Per altre informazioni sulla diagnosi dei problemi di SQL, vedere [Diagnostica avanzata](#AdvancedDiagnosing) alla fine di questo articolo.

<a name="Indexing"></a>
## Indicizzazione

Quando cominciano a verificarsi problemi relativi alle prestazioni delle query, il primo elemento da analizzare è la progettazione degli indici. Gli indici sono importanti, perché incidono direttamente sull'esecuzione delle query da parte del motore SQL. 

Ad esempio, se è necessario cercare spesso un elemento in base a uno specifico campo, è consigliabile aggiungere un indice per la colonna in questione. In caso contrario, il motore SQL sarà costretto ad eseguire una scansione di tabella e leggere ogni record fisico (o come minimo la colonna delle query) e i record potrebbero essere disseminati in posizioni diverse all'interno del disco.

Pertanto, se si eseguono spesso istruzioni WHERE o JOIN su specifiche colonne, assicurarsi di indicizzarle. Vedere la sezione [Creazione di indici](#CreatingIndexes) per altre informazioni.

Ma se gli indici sono così utili e le scansioni di tabella sono da evitare, per prevenire qualsiasi problema è opportuno indicizzare tutte le colonne della tabella?  La risposta breve è "probabilmente no". Gli indici occupano spazio e comportano un certo sovraccarico: ogni volta che viene eseguita un'operazione di inserimento in una tabella, è necessario aggiornare le strutture dell'indice per ogni colonna indicizzata. Vedere la seguente sezione per le linee guida sulla scelta degli indici di colonna.

### Linee guida sulla progettazione degli indici

Come descritto in precedenza, non sempre è un bene aggiungere altri indici a una tabella, perché gli indici stessi possono essere costosi in termini sia di prestazioni che di overhead di archiviazione.

#### Considerazioni sulle query

- Valutare l'aggiunta di indici alle colonne usate spesso nei predicati (ad esempio nelle clausole WHERE) e nelle condizioni di join, tenendo conto anche delle seguenti considerazioni sul database.
- Scrivere query che inseriscano o modifichino il più alto numero possibile di righe in un'unica istruzione, anziché usare più query per aggiornare le stesse righe. Quando è presente una sola istruzione, il motore di database può ottimizzare in modo migliore le modalità di gestione degli indici.
	
#### Considerazioni sul database

La presenza di un numero elevato di indici in una tabella incide sulle prestazioni delle istruzioni INSERT, UPDATE, DELETE e MERGE, perché quando vengono modificati i dati nella tabella, tutti gli indici devono essere modificati di conseguenza.

- Nelle tabelle **che vengono aggiornate spesso** evitare di indicizzare le colonne aggiornate con maggiore frequenza.
- Usare molti indici nelle tabelle **che vengono aggiornate raramente** ma che contengono grandi volumi di dati. Questo può contribuire a migliorare le prestazioni delle query che non modificano i dati, ad esempio delle istruzioni SELECT, perché Query Optimizer dispone di un numero maggiore di opzioni per determinare il metodo di accesso più rapido.

L'indicizzazione di tabelle di piccole dimensioni può non risultare ottimale, perché Query Optimizer impiega più tempo per scorrere l'indice cercando i dati che per eseguire una semplice scansione della tabella. Pertanto, può accadere che gli indici delle tabelle di piccole dimensioni non vengano mai usati, ma devono comunque essere gestiti in caso di modifica dei dati della tabella.


<a name="CreatingIndexes"></a>
### Creazione di indici

#### Back-end JavaScript

Per impostare l'indice per una colonna nel back-end JavaScript, seguire questa procedura:

1. Aprire il servizio mobile nel [portale di gestione di Azure][].
2. Fare clic sulla scheda **Dati**.
3. Selezionare la tabella da modificare.
4. Fare clic sulla scheda **Colonne**.
5. Selezionare la colonna. Nella barra dei comandi fare clic su **Imposta indice**:

	![Mobile Services Portal - Set Index][SetIndexJavaScriptPortal]

In questa visualizzazione è anche possibile rimuovere gli indici.

#### Back-end .NET

Per definire un indice in Entity Framework, usare l'attributo `[Index]` nei campi da indicizzare. Ad esempio:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }

		[Index]
        public bool Complete { get; set; }
    }
		 
Per altre informazioni sugli indici, vedere l'argomento relativo alle [annotazioni di indice in Entity Framework][]. Per altri suggerimenti sull'ottimizzazione degli indici, vedere [Indicizzazione avanzata](#AdvancedIndexing) alla fine di questo articolo.

<a name="Schema"></a>
## Progettazione dello schema

Di seguito sono illustrate alcune considerazioni di cui tenere conto nella scelta del tipo di dati per gli oggetti, che andrà a incidere sullo schema del database SQL. Spesso ottimizzando lo schema è possibile migliorare significativamente le prestazioni, in quanto SQL dispone di modalità di ottimizzazione personalizzate per gestire l'indicizzazione e l'archiviazione dei diversi tipi di dati:

- **Usare la colonna ID già disponibile**. Ogni tabella di Servizi mobili contiene una colonna ID predefinita configurata come chiave primaria, su cui è impostato un indice. Non è necessario creare un'ulteriore colonna ID.
- **Usare i tipi di dati corretti nel modello.** Se si sa che una determinata proprietà del modello sarà un valore numerico o booleano, assicurarsi che sia definita in tal modo nel modello anziché come stringa. Nel back-end JavaScript usare valori letterali, ad esempio `true` anziché `"true"` e `5` anziché `"5"`. Nel back-end .NET usare i tipi `int` e `bool` quando si dichiarano le proprietà del modello. In questo modo si consentirà al codice SQL di creare lo schema corretto per questi tipi, rendendo le query più efficienti.

<a name="Query"></a>
## Progettazione delle query

Ecco alcune linee guida da tenere in considerazione per le query sul database:

- **Eseguire sempre le operazioni di join nel database.** Spesso è necessario combinare i record di due o più tabelle in cui i record combinati condividono un campo comune (noto anche come *join*). Questa operazione può risultare inefficace se eseguita in modo incorretto, poiché potrebbe comportare lo spostamento verso il basso di tutte le entità da entrambe le tabelle e quindi l'iterazione su tutti gli elementi. È preferibile lasciare l'esecuzione di questo tipo di operazione al database stesso, ma è frequente eseguirla erroneamente sul client o nel codice del servizio mobile.
    - Non eseguire join nel codice dell'app
    - Non eseguire join nel codice del servizio mobile. Quando si usa il back-end JavaScript, tenere presente che l'[oggetto table](http://msdn.microsoft.com/library/windowsazure/jj554210.aspx) non gestisce i join. Assicurarsi di usare l'[oggetto mssql](http://msdn.microsoft.com/library/windowsazure/jj554212.aspx) direttamente per garantire che il join abbia luogo nel database. Per altre informazioni, vedere [Creare un join tra tabelle relazionali](http://azure.microsoft.com/documentation/articles/mobile-services-how-to-use-server-scripts/#joins). Se si usa il back-end .NET e le query vengono eseguite tramite LINQ, i join vengono gestiti automaticamente a livello di database da Entity Framework.
- **Implementare il paging.** L'esecuzione di query del database può talvolta causare un numero elevato di record restituiti al client. Per ridurre al minimo le dimensioni e la latenza delle operazioni, è consigliabile implementare il paging.
    - Per impostazione predefinita, il servizio mobile limiterà le query in arrivo a una pagina di dimensioni pari a 50 righe ed è possibile richiedere manualmente fino a 1.000 record. Per altre informazioni, vedere "Restituire i dati in pagine" per [Windows Store](http://azure.microsoft.com/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/#paging), [iOS](http://azure.microsoft.com/documentation/articles/mobile-services-ios-how-to-use-client-library/#paging), [Android](http://azure.microsoft.com/documentation/articles/mobile-services-android-how-to-use-client-library/#paging), [HTML/JavaScript](http://azure.microsoft.com/documentation/articles/mobile-services-html-how-to-use-client-library/#paging) e [Xamarin](http://azure.microsoft.com/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/#paging).
    - Per le query eseguite dal codice del servizio mobile non sono previste dimensioni di pagina predefinite. Se l'app non implementa il paging, o come misura preventiva, valutare l'applicazione di limiti predefiniti alle query. Nel back-end JavaScript usare l'operatore **take** sull'[oggetto query](http://msdn.microsoft.com/library/azure/jj613353.aspx). Se si usa il back-end .NET, considerare l'uso del [metodo Take](http://msdn.microsoft.com/library/vstudio/bb503062(v=vs.110).aspx) come parte della query LINQ.  

Per altre informazioni su come migliorare la progettazione di query, incluso come analizzare i piani di query, vedere [Progettazione avanzata delle query](#AdvancedQuery) alla fine di questo articolo.

<a name="Architecture"></a>
## Architettura del servizio

Si immagini uno scenario in cui si invia una notifica push a tutti i clienti per avvisare della disponibilità di nuovo contenuto nell'app. Quando i clienti fanno clic sulla notifica l'app si avvia, attivando probabilmente una chiamata al servizio mobile e l'esecuzione di una query sul database SQL. Potenzialmente, questa operazione può essere eseguita da milioni di clienti nel giro di pochi minuti, generando un picco di carico SQL notevolmente superiore rispetto allo stato stazionario dell'app. Il problema può essere risolto scalando l'app a un livello SQL superiore durante il picco e quindi tornando al livello precedente, ma questa soluzione richiede l'intervento manuale e comporta un aumento dei costi. Spesso dei piccoli aggiustamenti all'architettura del servizio mobile consentono di bilanciare il carico sul database SQL generato dai client ed eliminare i problematici picchi nella richiesta. Spesso è possibile implementare queste modifiche facilmente e con un impatto minimo sull'esperienza utente. Di seguito sono riportati alcuni esempi:

- **Distribuire il carico nel tempo.** Se si controlla la tempistica di determinati eventi (ad esempio la trasmissione di notifiche push) che si prevede che generino un picco nella domanda, e la cui tempistica non è fondamentale, è possibile distribuire tali eventi nel tempo. Nell'esempio precedente, per i clienti delle app, potrebbe essere accettabile ricevere una notifica dei nuovi contenuti di app in batch per un intero giorno, anziché ricevere notifiche separate contemporaneamente alla loro generazione. Considerare l'aggregazione dei clienti in gruppi in modo da consentire un recapito sfalsato a ogni gruppo. Se si usa Hub di notifica, l'applicazione di un tag aggiuntivo per tenere traccia del batch e l'invio di una notifica push a tale tag rappresenta un modo semplice di implementare tale strategia. Per altre informazioni sui tag, vedere la pagina relativa all'[uso di Hub di notifica per inviare le ultime notizie](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/).
- **Usare l'archiviazione BLOB e in tabelle quando appropriato.** Spesso i contenuti che i clienti visualizzeranno durante i picchi di uso sono alquanto statici e non devono essere archiviati in un database SQL, poiché in genere non è richiesta la funzionalità di query relazionali su tali contenuti. In questi casi, considerare l'archiviazione del contenuto in tabelle o in BLOB. È possibile accedere a BLOB pubblici nell'archiviazione BLOB direttamente dal dispositivo. Per accedere ai BLOB in modo sicuro o usare l'archiviazione tabelle, è necessario passare attraverso un'API personalizzata di Servizi mobili per proteggere la chiave di accesso alle risorse di archiviazione. Per altre informazioni, vedere la pagina relativa al [caricamento di immagini in Archiviazione di Azure usando Servizi mobili](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/).
- **Usare una cache in memoria**. Un'altra possibilità consiste nell'archiviare i dati normalmente accessibili durante un picco di traffico in una cache in memoria, ad esempio [Cache di Azure](http://azure.microsoft.com/services/cache/). In questo modo le richieste in ingresso possono recuperare le informazioni necessarie dalla memoria, anziché eseguire query ripetute sul database.

<a name="Advanced"></a>
## Risoluzione dei problemi avanzata
Questa sezione illustra alcune attività di diagnostica avanzate che possono risultare utili se le soluzioni precedenti non consentono di risolvere completamente il problema.

### Prerequisiti
Per eseguire alcune delle attività di diagnostica descritte in questa sezione è necessario l'accesso a uno strumento di gestione per i database SQL, ad esempio **SQL Server Management Studio** o le funzionalità di gestione integrate nel **portale di gestione di Azure**.

SQL Server Management Studio è un'applicazione Windows gratuita che offre funzionalità avanzate. Se non si dispone di un computer Windows (ad esempio se si usa un Mac), si può eseguire il provisioning di una macchina virtuale in Azure come illustrato in [Creare una macchina virtuale che esegue Windows Server](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/) e connettersi a questa in modalità remota. Se si prevede di usare la VM principalmente per eseguire SQL Server Management Studio, un'istanza **Basic A0** (in precedenza denominata "Molto piccola") dovrebbe essere sufficiente. 

Il portale di gestione di Azure offre un'esperienza di gestione integrata, più limitata ma disponibile senza bisogno di un'installazione locale.

Di seguito è illustrata la procedura per ottenere le informazioni di connessione per il database SQL che supporta il servizio mobile e quindi usare uno dei due strumenti per eseguire la connessione. È possibile scegliere uno qualsiasi dei due strumenti.

#### Ottenere le informazioni per la connessione SQL 
1. Avviare il [portale di gestione di Azure][].
2. Nella scheda Servizi mobili selezionare il servizio da usare.
3. Selezionare la scheda **Configura**.
4. Selezionare il nome del **Database SQL** nella sezione **Impostazioni database**. Verrà visualizzata la scheda del database SQL di Azure nel portale.
5. Selezionare **Imposta regole firewall di Microsoft Azure per questo indirizzo IP**.
6. Prendere nota dell'indirizzo del server nella sezione **Connetti al database**, ad esempio: *mcml4otbb9.database.windows.net*.

#### SQL Server Management Studio
1. Passare a [Edizioni di SQL Server - Express](http://www.microsoft.com/server-cloud/products/sql-server-editions/sql-server-express.aspx)
2. Trovare la sezione **SQL Server Management Studio** e selezionare il pulsante **Download** corrispondente.
3. Completare la procedura di installazione finché non è possibile eseguire l'applicazione:

    ![SQL Server Management Studio][SSMS]

4. Nella finestra di dialogo **Connetti al server** immettere i seguenti valori
    - Nome server: *indirizzo del server ottenuto in precedenza*
    - Autenticazione: *autenticazione di SQL Server*
    - Account di accesso: *account di accesso scelto durante la creazione del server*
    - Password: *password scelta durante la creazione del server*
5. A questo punto si dovrebbe essere connessi.

#### Portale di gestione database SQL
1. Nella scheda Database SQL di Azure del database in uso selezionare il pulsante **Gestisci** 
2. Configurare la connessione con i seguenti valori
    - Server: *dovrebbe essere preimpostato sul valore corretto*
    - Database: *lasciare il campo vuoto*
    - Nome utente: *account di accesso scelto durante la creazione del server*
    - Password: *password scelta durante la creazione del server*
3. A questo punto si dovrebbe essere connessi.

    ![Azure Management Portal - SQL Database][PortalSqlManagement]

<a name="AdvancedDiagnosing" />
### Diagnostica avanzata

Molte attività di diagnostica possono essere completate facilmente nel **portale di gestione di Azure**, ma alcune attività avanzate sono possibili solo tramite **SQL Server Management Studio** o il **portale di gestione database SQL**.  Verranno usate le viste a gestione dinamica (DMV), un set di viste popolate automaticamente con informazioni di diagnostica sul database. Questa sezione contiene un set di query che è possibile eseguire su queste viste per esaminare le varie metriche. Per altre informazioni, vedere [Monitoraggio di database SQL di Azure mediante le viste a gestione dinamica][].

Dopo aver completato i passaggi nella sezione precedente per eseguire la connessione al database in SQL Server Management Studio, selezionare il database in **Esplora oggetti**. Espandendo **Viste** e quindi **Viste di sistema** viene visualizzato un elenco delle viste di gestione. Per inoltrare le seguenti query, selezionare **Nuova query**, dopo aver selezionato il database in **Esplora oggetti**, quindi copiare e incollare la query e scegliere **Esegui**.

![SQL Server management Studio - dynamic management views][SSMSDMVs]

In alternativa, se si usa il portale di gestione database SQL, selezionare il database e quindi **Nuova query**.

![SQL Database Management Portal - new query][PortalSqlManagementNewQuery]

Per eseguire qualsiasi delle seguenti query, incollarla nella finestra e selezionare **Esegui**.

![SQL Database Management Portal - run query][PortalSqlManagementRunQuery]

#### Metriche avanzate

Se si usano i livelli Basic, Standard e Premium, nel portale di gestione sono immediatamente disponibili alcune metriche. Se tuttavia si usano i livelli Web e Business, solo la metrica Archiviazione è disponibile tramite il portale. Per fortuna è possibile ottenere facilmente queste e altre metriche usando la vista di gestione **[sys.resource\_stats](http://msdn.microsoft.com/library/dn269979.aspx)** indipendentemente dal livello in uso. Considerare la seguente query:

    SELECT TOP 10 * 
    FROM sys.resource_stats 
    WHERE database_name = 'todoitem_db' 
    ORDER BY start_time DESC

> [AZURE.NOTE] 
> Eseguire la query sul database **master** del server, la vista **sys.resource\_stats** è presente solo in quel database.

Il risultato conterrà le seguenti metriche utili: CPU (% del limite del livello), Archiviazione (MB), Letture dati fisiche (% del limite del livello), Scritture nei log (% del limite del livello), Memoria (% del limite del livello), Conteggio Worker, Conteggio sessioni e così via. 

#### Eventi di connettività SQL

La vista **[sys.event\_log](http://msdn.microsoft.com/library/azure/jj819229.aspx)** contiene i dettagli degli eventi correlati alla connettività.

    select * from sys.event_log 
    where database_name = 'todoitem_db'
    and event_type like 'throttling%'
    order by start_time desc

> [AZURE.NOTE] 
> Eseguire la query sul database **master** del server, la vista **sys.event\_log** è presente solo in quel database.

<a name="AdvancedIndexing" />
### Indicizzazione avanzata

Una tabella o una vista può contenere i seguenti tipi di indice:

- **Cluster**. Un indice cluster specifica la modalità di archiviazione fisica dei record sul disco. Deve essere presente un solo indice cluster per tabella, perché le righe di dati possono essere ordinate in un solo ordine.

- **Non cluster**. Gli indici non cluster vengono archiviati separatamente rispetto alle righe di dati e consentono di eseguire ricerche in base al valore dell'indice. Tutti gli indici non cluster di una tabella usano come chiavi di ricerca i valori chiave dell'indice cluster.

Per fornire un'analogia reale: considerare un libro o un manuale tecnico. Il contenuto di ogni pagina è un record, il numero di pagina è l'indice cluster e l'indice degli argomenti in fondo al libro è l'indice non cluster. Ogni voce nell'indice degli argomenti punta all'indice cluster, il numero di pagina.

> [AZURE.NOTE] 
> Per impostazione predefinita, il back-end JavaScript di Servizi mobili di Azure imposta **\_createdAt** come indice cluster. Se si rimuove questa colonna o si desidera un indice cluster diverso, assicurarsi di seguire le [linee guida per la progettazione di indici cluster](#ClusteredIndexes) più avanti. Nel back-end .NET la classe `EntityData` definisce `CreatedAt` come indice cluster usando l'annotazione `[Index(IsClustered = true)]`.

<a name="ClusteredIndexes"></a>
#### Linee guida per la progettazione di indici cluster

Ogni tabella deve disporre di un indice cluster sulla colonna (o sulle colonne, nel caso di una chiave composta) con le seguenti caratteristiche:

- Narrow: viene usato un tipo di dati di piccole dimensioni o è una [chiave composta][Vincoli di chiavi primarie ed esterne] di un numero ridotto di colonne strette
- Univoco o pressoché univoco
- Statico: il valore non viene modificato di frequente
- Crescente 
- (Facoltativo) Di larghezza fissa
- (Facoltativo) Non Null

Lo scopo della proprietà **narrow** è che tutti gli altri indici in una tabella usano i valori chiave dall'indice cluster come chiavi di ricerca. Nell'esempio di un indice degli argomenti in fondo a un libro, l'indice cluster è un numero di pagina ed è un piccolo numero. Se l'indice cluster includesse il titolo del capitolo, l'indice degli argomenti sarebbe molto più lungo, perché il valore della chiave sarebbe (nome capitolo, numero pagina).

La chiave deve essere **statica** e **crescente** per evitare di dover gestire la posizione fisica dei record (ovvero spostare fisicamente i record o potenzialmente frammentare l'archiviazione dividendo le pagine in cui i record sono archiviati). 

L'indice cluster sarà particolarmente utile per le query che:

- Restituiscono un intervallo di valori usando operatori come BETWEEN, >, >=, < e <=. 
	- Quando la riga con il primo valore viene trovata usando l'indice cluster, le righe con i valori indicizzati successivi sono sempre fisicamente adiacenti. 
- Usano clausole JOIN, come nel caso delle colonne chiave esterne.
- Usano clausole ORDER BY o GROUP BY.
	- L'uso di un indice basato sulle colonne specificate nella clausola ORDER BY o GROUP BY consente di evitare operazioni di ordinamento dei dati nel motore di database perché le righe sono già ordinate e pertanto di ottimizzare le prestazioni delle query.

#### Creazione di indici cluster in Entity Framework

Per impostare l'indice cluster nel back-end .NET usando Entity Framework, impostare la proprietà `IsClustered` dell'annotazione. Ad esempio, questa è la definizione di `CreatedAt` in `Microsoft.WindowsAzure.Mobile.Service.EntityData`:

	[Index(IsClustered = true)]
	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	[TableColumnAttribute(TableColumnType.CreatedAt)]
	public DateTimeOffset? CreatedAt { get; set; }

#### Creazione di indici nello schema del database

Per il back-end JavaScript, è possibile modificare l'indice cluster di una tabella solo modificando direttamente lo schema del database, mediante SQL Server Management Studio o il Portale database SQL di Azure.

Le seguenti guide descrivono come impostare un indice cluster o non cluster modificando direttamente lo schema del database:  

- [Creazione e modifica di vincoli PRIMARY KEY][]
- [Creare indici non cluster][]
- [Creare indici cluster][]
- [Creare indici univoci][]

#### Trovare i primi n indici mancanti 
È possibile scrivere query SQL sulle DMV che offriranno informazioni più dettagliate circa l'uso delle risorse delle singole query o dati euristici sugli indici di aggiungere. La seguente query determina i 10 indici mancanti in grado di determinare il massimo miglioramento cumulativo previsto, in ordine decrescente, per le query utente.

    SELECT TOP 10 *
    FROM sys.dm_db_missing_index_group_stats
    ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)
    DESC;

La seguente query di esempio esegue un join tra le tabelle per ottenere un elenco delle colonne che dovrebbero fare parte di ogni indice mancante e calcola un 'index advantage' per determinare se l'indice specifico va preso in considerazione:

    SELECT * from 
    (
        SELECT 
        (user_seeks+user_scans) * avg_total_user_cost * (avg_user_impact * 0.01) AS index_advantage, migs.*
        FROM sys.dm_db_missing_index_group_stats migs
    ) AS migs_adv,
      sys.dm_db_missing_index_groups mig,
      sys.dm_db_missing_index_details mid
    WHERE
      migs_adv.group_handle = mig.index_group_handle and
      mig.index_handle = mid.index_handle
      AND migs_adv.index_advantage > 10
    ORDER BY migs_adv.index_advantage DESC;

Per altre informazioni, vedere [Monitoraggio di database SQL di Azure mediante le viste a gestione dinamica][] e [DMV di indici mancanti](sys-missing-index-stats).

<a name="AdvancedQuery" />
### Progettazione avanzata delle query 

Spesso è difficile diagnosticare quali siano le query con il costo più elevato per il database. 

#### Ricerca delle prime n query

Nel seguente esempio vengono restituite informazioni sulle prime cinque query classificate in base al tempo medio della CPU. Nell'esempio le query vengono aggregate in base al relativo valore hash, in modo da raggruppare le query logicamente equivalenti in base all'uso di risorse cumulativo.

	SELECT TOP 5 query_stats.query_hash AS "Query Hash", 
	    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
	    MIN(query_stats.statement_text) AS "Statement Text"
	FROM 
	    (SELECT QS.*, 
	    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
	    ((CASE statement_end_offset 
	        WHEN -1 THEN DATALENGTH(st.text)
	        ELSE QS.statement_end_offset END 
	            - QS.statement_start_offset)/2) + 1) AS statement_text
	     FROM sys.dm_exec_query_stats AS QS
	     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
	GROUP BY query_stats.query_hash
	ORDER BY 2 DESC;

Per altre informazioni, vedere [Monitoraggio di database SQL di Azure mediante le viste a gestione dinamica][]. Oltre ad eseguire la query, il **portale di gestione database SQL** offre un comodo collegamento per visualizzare questi dati. È sufficiente selezionare **Riepilogo** per il database e quindi **Prestazioni query**:

![SQL Database Management Portal - query performance][PortalSqlManagementQueryPerformance]

#### Analisi del piano di query

Dopo aver identificato le query con costo elevato o se si sta per distribuire codice usando nuove query e se ne vogliono esaminare le prestazioni, gli strumenti offrono un ampio supporto per analizzare il **piano di query**. Il piano di query consente di vedere quali operazioni usano più tempo della CPU e risorse di I/O quando viene eseguita una determinata query SQL. Per analizzare il piano di query in **SQL Server Management Studio**, usare i pulsanti della barra degli strumenti evidenziati.

![SQL Server Management Studio - query plan][SSMSQueryPlan]

Per analizzare il piano di query nel **portale di gestione database SQL**, usare i pulsanti della barra degli strumenti evidenziati.

![SQL Database Management Portal - query plan][PortalSqlManagementQueryPlan]

## Vedere anche

- [Documentazione relativa al database SQL di Azure][]
- [Prestazioni e scalabilità del database SQL di Azure][]
- [Risoluzione dei problemi relativi al database SQL di Azure][]

### Indicizzazione

- [Nozioni di base sugli indici][]
- [Linee guida generali per la progettazione di indici][]
- [Linee guida per la progettazione di indici univoci][]
- [Linee guida per la progettazione di indici cluster][]
- [Vincoli di chiavi primarie ed esterne][]
- [Costo delle chiavi][]

### Entity Framework
- [Considerazioni sulle prestazioni per Entity Framework 5][]
- [Annotazioni dei dati per Code First][]

<!-- IMAGES -->
 
[SSMS]: ./media/mobile-services-sql-scale-guidance/1.png
[PortalSqlManagement]: ./media/mobile-services-sql-scale-guidance/2.png
[PortalSqlMetrics]: ./media/mobile-services-sql-scale-guidance/3.png
[PortalSqlScale]: ./media/mobile-services-sql-scale-guidance/4.png
[PortalSqlAddAlert]: ./media/mobile-services-sql-scale-guidance/5.png
[PortalSqlAddAlert2]: ./media/mobile-services-sql-scale-guidance/6.png
[PortalSqlAddAlert3]: ./media/mobile-services-sql-scale-guidance/7.png
[SetIndexJavaScriptPortal]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png
[SSMSDMVs]: ./media/mobile-services-sql-scale-guidance/8.png
[PortalSqlManagementNewQuery]: ./media/mobile-services-sql-scale-guidance/9.png
[PortalSqlManagementRunQuery]: ./media/mobile-services-sql-scale-guidance/10.png
[PortalSqlManagementQueryPerformance]: ./media/mobile-services-sql-scale-guidance/11.png
[SSMSQueryPlan]: ./media/mobile-services-sql-scale-guidance/12.png
[PortalSqlManagementQueryPlan]: ./media/mobile-services-sql-scale-guidance/13.png

<!-- LINKS -->

[portale di gestione di Azure]: http://manage.windowsazure.com

[Documentazione relativa al database SQL di Azure]: http://azure.microsoft.com/documentation/services/sql-database/
[Gestione di database SQL mediante SQL Server Management Studio]: http://go.microsoft.com/fwlink/p/?linkid=309723&clcid=0x409
[Monitoraggio di database SQL di Azure mediante le viste a gestione dinamica]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Prestazioni e scalabilità del database SQL di Azure]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409
[Risoluzione dei problemi relativi al database SQL di Azure]: http://msdn.microsoft.com/library/azure/ee730906.aspx

<!-- MSDN -->
[Creazione e modifica di vincoli PRIMARY KEY]: http://technet.microsoft.com/library/ms181043(v=sql.105).aspx
[Creare indici cluster]: http://technet.microsoft.com/library/ms186342(v=sql.120).aspx
[Creare indici univoci]: http://technet.microsoft.com/library/ms187019.aspx
[Creare indici non cluster]: http://technet.microsoft.com/library/ms189280.aspx

[Vincoli di chiavi primarie ed esterne]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx
[Nozioni di base sugli indici]: http://technet.microsoft.com/library/ms190457(v=sql.105).aspx
[Linee guida generali per la progettazione di indici]: http://technet.microsoft.com/library/ms191195(v=sql.105).aspx 
[Linee guida per la progettazione di indici univoci]: http://technet.microsoft.com/library/ms187019(v=sql.105).aspx
[Linee guida per la progettazione di indici cluster]: http://technet.microsoft.com/library/ms190639(v=sql.105).aspx

[sys-missing-index-stats]: http://technet.microsoft.com/library/ms345421.aspx

<!-- EF -->
[Considerazioni sulle prestazioni per Entity Framework 5]: http://msdn.microsoft.com/data/hh949853
[Annotazioni dei dati per Code First]: http://msdn.microsoft.com/data/jj591583.aspx
[annotazioni di indice in Entity Framework]:http://msdn.microsoft.com/data/jj591583.aspx#Index

<!-- BLOG LINKS -->
[Costo delle chiavi]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/

<!--HONumber=47-->
