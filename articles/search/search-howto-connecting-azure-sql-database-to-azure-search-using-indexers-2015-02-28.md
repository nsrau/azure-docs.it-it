<properties 
	pageTitle="Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori | Microsoft Azure | Indicizzatori" 
	description="Informazioni su come estrarre i dati dal database SQL di Azure a un indice di Ricerca di Azure tramite gli indicizzatori." 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="05/26/2016" 
	ms.author="eugenesh"/>

#Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori

Il servizio Ricerca di Azure è un servizio di ricerca ospitato sul cloud che rende più semplice fornire un'esperienza di ricerca eccellente. Prima di poter eseguire ricerche, è necessario popolare un indice di Ricerca di Azure con i dati. Se i dati si trovano in un database SQL di Azure, il nuovo **Indicizzatore di Ricerca di Azure per il database SQL di Azure** (o **Indicizzatore SQL di Azure**, in breve) in Ricerca di Azure è in grado di automatizzare il processo di indicizzazione. Ciò significa che è necessario scrivere meno codice e preoccuparsi di meno infrastruttura.

Attualmente, gli indicizzatori funzionano solo con il database SQL di Azure, SQL Server nelle macchine virtuali di Azure e [Azure DocumentDB](../documentdb/documentdb-search-indexer.md). In questo articolo l’attenzione verrà focalizzata sugli indicizzatori che funzionano con il database SQL di Azure. Se si desidera visualizzare il supporto per altre origini dati, fornire commenti e suggerimenti sul [forum relativo ai commenti e suggerimenti di Ricerca di Azure](https://feedback.azure.com/forums/263029-azure-search/).

In questo articolo verranno illustrati i meccanismi di utilizzo degli indicizzatori, ma verranno anche approfonditi funzionalità e comportamenti che sono disponibili solo con i database SQL (ad esempio, il rilevamento delle modifiche integrato).

## Indicizzatori e origini dati

Per impostare e configurare un indicizzatore SQL di Azure, è possibile chiamare l’[API REST di Ricerca di Azure](http://go.microsoft.com/fwlink/p/?LinkID=528173) per creare e gestire **indicizzatori** e **origini dati**.

È inoltre possibile utilizzare la [Classe indicizzatore](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) nel [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx), o la procedura guidata per l’importazione dei dati o il [portale di Azure classico](https://portal.azure.com) per creare e pianificare un indicizzatore.

Un'**origine dati** specifica i dati da indicizzare, le credenziali necessarie per accedere ai dati e i criteri che consentono a Ricerca di Azure di identificare in modo efficace le modifiche apportate ai dati (righe nuove, modificate o eliminate). È definita come risorsa indipendente affinché possa essere usata da più indicizzatori.

Un **indicizzatore** è una risorsa che connette le origini dati agli indici di ricerca di destinazione. Un indicizzatore viene usato nei modi seguenti:
 
- Eseguire una copia occasionale dei dati per popolare un indice.
- Aggiornare un indice con le modifiche nell'origine dati in base a una pianificazione.
- Eseguire aggiornamenti su richiesta in un indice in base alle esigenze. 

## Quando usare l’indicizzatore SQL di Azure

In base a diversi fattori relativi ai dati, l'utilizzo dell'indicizzatore di SQL di Azure potrebbe non essere appropriato. Se i dati soddisfano i seguenti requisiti, è possibile utilizzare l’indicizzatore SQL di Azure:

- Tutti i dati provengono da una singola tabella o vista
	- Se i dati sono sparsi tra più tabelle, è possibile creare una vista e utilizzarla con l'indicizzatore. Tuttavia, tenere presente che se si utilizza una vista, non si sarà in grado di utilizzare il rilevamento delle modifiche integrato di SQL Server. Per ulteriori informazioni, vedere questa sezione. 
- I tipi di dati utilizzati nell'origine dati sono supportati dall’indicizzatore. È supportata la maggior parte dei tipi SQL, ma non tutti. Per ulteriori informazioni, vedere [Mapping dei tipi di dati per gli indicizzatori in Ricerca di Azure](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Non sono necessari aggiornamenti quasi in tempo reale dell’indice quando viene modificata una riga. 
	- L'indicizzatore può reindicizzare la tabella al massimo ogni 5 minuti. Se i dati vengono modificati di frequente ed è necessario riflettere le modifiche nell’indice entro pochi secondi o pochi minuti, è consigliabile utilizzare direttamente l’[API dell’indice di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx). 
- Se si dispone di un set di dati di grandi dimensioni e si prevede di eseguire l'indicizzatore in una pianificazione, lo schema consente di identificare in modo efficiente le righe modificate (ed eliminate, se applicabili). Per ulteriori informazioni, vedere "Acquisizione delle righe modificate ed eliminate", di seguito. 
- La dimensione dei campi indicizzati in una riga non supera la dimensione massima di una richiesta di indicizzazione di Ricerca di Azure, vale a dire 16 MB. 

## Creare e utilizzare un indicizzatore SQL di Azure

Creare, innanzitutto, l'origine dati:

	POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
	    "container" : { "name" : "name of the table or view that you want to index" }
	}


È possibile ottenere la stringa di connessione dal [portale di Azure classico](https://portal.azure.com). Utilizzare l’opzione `ADO.NET connection string`.

Quindi, creare un indice di Ricerca di Azure di destinazione, se non ne è già disponibile uno. È possibile eseguire questa operazione dall’[interfaccia utente del portale](https://portal.azure.com) o usando l’[API di creazione dell’indice](https://msdn.microsoft.com/library/azure/dn798941.aspx). Assicurarsi che lo schema dell'indice di destinazione sia compatibile con lo schema della tabella di origine. Per informazioni dettagliate, vedere [Mapping tra tipi di dati SQL e tipi di dati di Ricerca di Azure](#TypeMapping).

Infine, creare l'indicizzatore assegnandogli un nome e il riferimento all’origine dati e all’indice di destinazione:

	POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myindexer",
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name"
	}

Un indicizzatore creato in questo modo non dispone di una pianificazione. Viene eseguito non appena viene creato. È possibile rieseguirlo in qualsiasi momento mediante una richiesta di **esecuzione indicizzatore**:

	POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
	api-key: admin-key

È possibile personalizzare alcuni aspetti del comportamento dell'indicizzatore, ad esempio le dimensioni del batch e il numero di documenti che è possibile ignorare prima che un'esecuzione dell'indicizzatore abbia esito negativo. Per altri dettagli, vedere [Create Indexer API](https://msdn.microsoft.com/library/azure/dn946899.aspx) (Creare un'API di indicizzatore).
 
Potrebbe essere necessario consentire ai servizi di Azure di connettersi al database. Vedere [Connessione da Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) per istruzioni su come eseguire questa operazione.

Per monitorare lo stato dell'indicizzatore e la cronologia di esecuzione (numero di elementi indicizzati, errori e così via), utilizzare una richiesta di **stato indicizzatore**:

	GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
	api-key: admin-key

La risposta sarà simile alla seguente:

	{
		"@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
		"status":"running",
		"lastResult": {
			"status":"success",
			"errorMessage":null,
			"startTime":"2015-02-21T00:23:24.957Z",
			"endTime":"2015-02-21T00:36:47.752Z",
			"errors":[],
			"itemsProcessed":1599501,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null 
        },
		"executionHistory":
		[
			{
				"status":"success",
				"errorMessage":null,
				"startTime":"2015-02-21T00:23:24.957Z",
				"endTime":"2015-02-21T00:36:47.752Z",
				"errors":[],
				"itemsProcessed":1599501,
				"itemsFailed":0,
				"initialTrackingState":null,
				"finalTrackingState":null 
			},
			... earlier history items 
		]
	}

La cronologia di esecuzione contiene fino a 50 esecuzioni completate più recenti, in ordine cronologico inverso (in modo che l'esecuzione più recente venga visualizzata per prima nella risposta). Sono disponibili informazioni aggiuntive relative alla risposta [Ottenere lo stato dell'indicizzatore](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## Eseguire gli indicizzatori in base a una pianificazione

È inoltre possibile fare in modo che l'indicizzatore si esegua periodicamente in base a una pianificazione. A tale scopo, è sufficiente aggiungere la proprietà **schedule** al momento della creazione o dell’aggiornamento dell’indicizzatore. Nell'esempio seguente viene illustrata una richiesta PUT di aggiornamento dell'indicizzatore:

	PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key 

	{ 
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name",
	    "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
	}

È richiesto il parametro **interval**. L'intervallo fa riferimento al tempo tra l'inizio di due esecuzioni consecutive dell'indicizzatore. L'intervallo minimo consentito è di 5 minuti, quello massimo di un giorno. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Il modello è: `P(nD)(T(nH)(nM))`. Esempi: `PT15M` ogni 15 minuti, `PT2H` ogni due ore.

Il valore facoltativo **startTime** indica quando devono essere avviate le esecuzioni pianificate; se viene omesso, verrà utilizzato l'ora UTC corrente. Può trattarsi di un’ora del passato, caso in cui la prima esecuzione verrà pianificata come se l'indicizzatore fosse stato continuamente in funzione sin dall'ora di inizio.

È possibile eseguire solo l'esecuzione di un indicizzatore specificato per volta. Se un indicizzatore è già in esecuzione quando dovrebbe per iniziare quello successivo, l'esecuzione viene ignorata e viene ripresa al successivo intervallo, presupponendo che non sia in esecuzione nessun altro indicizzatore.

Ecco un esempio per rendere il discorso più concreto. Si supponga che sia configurata la seguente pianificazione oraria:

	"schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Di seguito è illustrato ciò che accade:

1. L’esecuzione del primo indicizzatore comincia il 1 marzo 2015 alle 12:00 UTC più o meno.
1. Si supponga che l'esecuzione richieda 20 minuti (o un tempo qualsiasi inferiore a 1 ora).
1. La seconda esecuzione inizia il 1 marzo 2015 alle 13.00, più o meno 
1. Si supponga ora che l'esecuzione richieda più di un'ora (affinché ciò si verifichi sarebbero necessari molti documenti, ma si tratta di un esempio utile), ad esempio minuti 70, in modo che si completi intorno alle 02:10.
1. Ora sono le 02:00, l’ora dell’inizio della terza esecuzione. Tuttavia, poiché la seconda esecuzione delle 01:00 è ancora in esecuzione, la terza esecuzione viene ignorata. La terza esecuzione inizia alle 03:00.

È possibile aggiungere, modificare o eliminare una pianificazione per un indicizzatore esistente utilizzando una richiesta di **indicizzatore PUT**.

## Acquisizione di righe nuove, modificate ed eliminate

Se si utilizza una pianificazione e la tabella contiene un numero considerevole di righe, è necessario utilizzare un criterio di rilevamento delle modifiche di dati che consenta all'indicizzatore di recuperare in modo efficiente solo le righe nuove o modificate senza dover ripetere l'indicizzazione dell'intera tabella.

### Criteri di rilevamento delle modifiche integrati di SQL

Se il database SQL supporta il [rilevamento delle modifiche](https://msdn.microsoft.com/library/bb933875.aspx), è consigliabile usare i **criteri di rilevamento delle modifiche integrati di SQL**. Questi criteri favoriscono la massima efficienza del rilevamento delle modifiche e consentono a Ricerca di Azure di identificare le righe eliminate senza dover includere nello schema una colonna di "eliminazione temporanea" esplicita.

Il rilevamento delle modifiche integrato è supportata a partire dalle seguenti versioni di database di SQL Server:
 
- SQL Server 2008 R2 e versioni successive, se si utilizza SQL Server nelle macchine virtuali di Azure. 
- Database SQL di Azure V12, se si utilizza il database SQL di Azure SQL.

Quando si usano i criteri di rilevamento delle modifiche integrati di SQL, non specificare criteri di rilevamento dell'eliminazione dei dati separati, perché questi ultimi includono il supporto predefinito per l'identificazione delle righe eliminate.

Questi criteri possono essere usati solo con le tabelle, non con le viste. Prima di poter usare questi criteri, è necessario abilitare il rilevamento delle modifiche per la tabella in uso. Per le istruzioni, vedere [Abilitare e disabilitare il rilevamento delle modifiche](https://msdn.microsoft.com/library/bb964713.aspx).

Per utilizzare questo criterio, creare o aggiornare l'origine dati nel modo indicato di seguito:
 
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	  }
	}

### Criteri di rilevamento delle modifiche con limite massimo

Sebbene i criteri di rilevamento delle modifiche siano consigliati, non sarà possibile utilizzarli se i dati sono in una vista o se si utilizza una versione precedente del database SQL di Azure. In questo caso, è consigliabile utilizzare i criteri di limite massimo. Questi criteri possono essere utilizzati se la tabella contiene una colonna che soddisfa i criteri seguenti:

- Tutti gli inserimenti specificano un valore per la colonna. 
- Tutti gli aggiornamenti a un elemento modificano anche il valore della colonna. 
- Il valore di questa colonna aumenta a ogni modifica.
- Le query che usano una clausola `WHERE` simile a `WHERE [High Water Mark Column] > [Current High Water Mark Value]` possono essere eseguite in modo efficiente.

Ad esempio, una colonna **rowversion** indicizzata è un candidato ideale per la colonna con limite massimo. Per utilizzare questo criterio, creare o aggiornare l'origine dati nel modo indicato di seguito:

	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
	       "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	  }
	}

### Criteri di rilevamento eliminazione colonna di eliminazione temporanea

Quando le righe vengono eliminate dalla tabella di origine, è probabile che si desideri eliminarle anche dall’indice di ricerca. Se si utilizzano i criteri di rilevamento delle modifiche integrati di SQL, questa operazione è automatica. Tuttavia, i criteri di rilevamento delle modifiche limite massimo non sono di supporto all’utente con le righe eliminate. Cosa fare?

Se le righe vengono rimosse fisicamente dalla tabella, non è possibile dedurre in alcun modo la presenza di record che non esistono più. Tuttavia, è possibile utilizzare la tecnica dell’"eliminazione temporanea" per contrassegnare le righe come eliminate dal punto di vista logico, senza rimuoverle dalla tabella, aggiungendo una colonna e contrassegnando le righe come eliminate tramite un valore marcatore in tale colonna.

Quando si utilizza la tecnica dell’eliminazione temporanea, è possibile specificare la modalità di eliminazione temporanea come segue se si crea o si aggiorna l’origine dati:

	{ 
	    …, 
	    "dataDeletionDetectionPolicy" : { 
	       "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
	       "softDeleteColumnName" : "[a column name]", 
	       "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
	    }
	}

Si noti che **softDeleteMarkerValue** deve essere una stringa. Utilizzare la rappresentazione stringa del valore effettivo. Ad esempio, se si dispone di una colonna di tipo integer in cui le righe eliminate sono contrassegnate con il valore 1, utilizzare `"1"`; se si dispone di una colonna BIT, nella quale le righe eliminate vengono contrassegnate con valore booleano true, utilizzare `"True"`.

<a name="TypeMapping"></a>
## Mapping tra tipi di dati SQL e tipi di dati di Ricerca di Azure

|Tipo di dati SQL | Tipi di campi dell'indice di destinazione consentiti |Note 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, money decimal numeric | Edm.String| Ricerca di Azure non supporta la conversione di tipi decimali in Edm.Double, perché in tal caso si perderebbe la precisione |
| char, nchar, varchar, nvarchar | EDM.String<br/>Collection(Edm.String)|La trasformazione di una colonna di stringhe in Collection(Edm.String) richiede l'utilizzo di un'anteprima della versione 2015-02-28-anteprima dell'API. Vedere[questo articolo](search-api-indexers-2015-02-28-Preview.md#create-indexer)per informazioni dettagliate| 
|smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|geography | Edm.GeographyPoint | Sono supportate solo le istanze geografiche di tipo POINT con SRID 4326 (ossia l'impostazione predefinita) | | 
|rowversion| N/D |Le colonne di versione di riga non possono essere archiviate nell'indice di ricerca, ma possono essere usate per il rilevamento modifiche | |
| time, timespan, binary, varbinary, image, xml, geometry, CLR types | N/D |Non supportate |


## Domande frequenti

**D:** Posso utilizzare l'indicizzatore SQL di Azure SQL con i database SQL in esecuzione sulle macchine virtuali IaaS in Azure?

A: Sì. Tuttavia, è necessario consentire al servizio di ricerca di connettersi al database:

1. Configurare il firewall per consentire l'accesso all'indirizzo IP del servizio di ricerca. 
2. Può anche essere necessario configurare il database con un certificato attendibile per consentire al servizio di ricerca di aprire le connessioni SSL al database.

**D:** Posso utilizzare l'indicizzatore SQL di Azure SQL con i database SQL in esecuzione locale?

R: Tale operazione non è consigliata né supportata, in quanto richiederebbe l’apertura dei database al traffico Internet.

**D:** Posso utilizzare l'indicizzatore SQL di Azure SQL con database diversi da SQL Server in esecuzione in IaaS in Azure?

R: Questo scenario non è supportato, in quanto non è stato eseguito il test dell'indicizzatore con database diversi da SQL Server.

**D:** Posso creare più indicizzatori in esecuzione in una pianificazione?

A: Sì. Tuttavia, è possibile eseguire un solo indicizzatore per volta in un nodo. Se è necessario eseguire più indicizzatori contemporaneamente, considerare il ridimensionamento del servizio di ricerca a più unità di ricerca.

**D:** L’esecuzione di un indicizzatore influisce sul carico di lavoro della query?

A: Sì. L'indicizzatore viene eseguito in uno dei nodi del servizio di ricerca e le risorse di tale nodo vengono condivise tra l'indicizzazione e la gestione del traffico di query e altre richieste API. Se si eseguono un’indicizzazione e dei carichi di lavoro di query intensivi e si verifica una frequenza elevata di errori 503 o un aumento dei tempi di risposta, considerare il ridimensionamento del servizio di ricerca.

<!---HONumber=AcomDC_0601_2016-->