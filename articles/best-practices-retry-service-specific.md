<properties
   pageTitle="Indicazioni specifiche del servizio per la ripetizione di tentativi | Microsoft Azure"
   description="Indicazioni specifiche del servizio per impostare il meccanismo di ripetizione dei tentativi."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>


<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>


# Indicazioni specifiche del servizio per la ripetizione di tentativi

![](media/best-practices-retry-service-specific/pnp-logo.png)

## Panoramica

La maggior parte dei servizi di Azure e degli SDK client include un meccanismo di ripetizione dei tentativi. Ogni servizio, tuttavia, presenta caratteristiche e requisiti diversi e, quindi, ogni meccanismo di ripetizione dei tentativi è ottimizzato per il servizio specifico in cui è incorporato. Questo articolo riepiloga le caratteristiche dei meccanismi di ripetizione dei tentativi relativi alla maggior parte dei servizi di Azure e include utili informazioni per usare, adattare o estendere il meccanismo di ripetizione dei tentativi per ogni servizio.

Per indicazioni generali sulla gestione degli errori temporanei e sulla ripetizione dei tentativi di eseguire connessioni e operazioni su servizi e risorse, vedere [Indicazioni generali per la ripetizione di tentativi](best-practices-retry-general.md).

La tabella seguente riepiloga le caratteristiche dei meccanismi di ripetizione dei tentativi associati ai servizi di Azure descritti in questo articolo.

| **Servizio** | **Funzionalità per la ripetizione di tentativi** | **Configurazione dei criteri** | **Ambito** | **Funzionalità di telemetria** |
|---------------------------------------|-----------------------------------------|------------------------------|--------------------------------------------------|------------------------
| **[AzureStorage](#azure-storage-retry-guidelines)** | Native nel client | Programmatica | Client e singole operazioni | TraceSource |
| **[Database SQL con Entity Framework](#sql-database-using-entity-framework-6-retry-guidelines)** | Native nel client | Programmatica | Globale per AppDomain | Nessuno |
| **[Database SQL con ADO.NET](#sql-database-using-ado-net-retry-guidelines)** | Topaz* | Dichiarativa e a livello di codice | Singole istruzioni o blocchi di codice | Personalizzate |
| **[Bus di servizio](#service-bus-retry-guidelines)** | Native nel client | Programmatica | Gestore dello spazio dei nomi, factory di messaggistica e client | ETW |
| **[Cache](#cache-redis-retry-guidelines)** | Native nel client | Programmatica | Client | TextWriter |
| **[DocumentDB](#documentdb-pre-release-retry-guidelines)** | Native nel servizio | Non configurabili | Globale | TraceSource |
| **[Ricerca](#search-retry-guidelines)** | Topaz* (con strategia di rilevamento personalizzata) | Dichiarativa e a livello di codice | Blocchi di codice | Personalizzate |
| **[Active Directory](#azure-active-directory-retry-guidelines)** | Topaz* (con strategia di rilevamento personalizzata) | Dichiarativa e a livello di codice | Blocchi di codice | Personalizzate |
**Topaz come nome descrittivo per il Blocco di applicazioni per la gestione degli errori temporanei incluso nella <a href="http://msdn.microsoft.com/library/dn440719.aspx">Enterprise Library 6.0</a>. Con Topaz è possibile usare una strategia di rilevamento personalizzata per la maggior parte dei servizi, come descritto in questo articolo. La sezione [Strategie del Blocco di applicazioni per la gestione degli errori temporanei (Topaz)](#transient-fault-handling-application-block-topaz-strategies), alla fine di questo articolo, illustra le strategie predefinite per Topaz. Tenere presente che il blocco è ora un framework open source e non è direttamente supportato da Microsoft.

> [AZURE.NOTE]Per la maggior parte dei meccanismi di ripetizione dei tentativi incorporati in Azure, non è attualmente possibile applicare criteri di ripetizione dei tentativi differenti per diversi tipi di errore o eccezione, oltre alle funzionalità previste dai criteri stessi. Al momento della stesura di questo documento, quindi, il consiglio migliore è quello di configurare criteri che forniscano una combinazione ottimale di prestazioni e disponibilità. I criteri possono essere successivamente ottimizzati analizzando i file di log per determinare i tipi di errori temporanei che si sono verificati. Ad esempio, se la maggior parte degli errori è correlata a problemi di connettività di rete, si potrebbe optare per un tentativo immediato anziché attendere molto tempo per ripetere il primo tentativo.

## Archiviazione di Azure - Linee guida per la ripetizione di tentativi

I servizi di archiviazione di Azure includono funzioni di archiviazione di BLOB e tabelle, file e code di archiviazione.

### Meccanismo di ripetizione dei tentativi

I nuovi tentativi si compiono a livello di singola operazione REST e sono parte integrante dell'implementazione dell'API client. L'SDK di archiviazione dei client usa classi che implementano l'interfaccia [IExtendedRetryPolicy Interface](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).

Esistono diverse implementazioni di questa interfaccia. I client di archiviazione possono scegliere tra vari tipi di criteri, progettati per l'accesso a tabelle, BLOB o code. Ogni implementazione usa una strategia di ripetizione dei tentativi diversa, che essenzialmente definisce l'intervallo di tempo tra i tentativi e altri dettagli.

Le classi incorporate forniscono il supporto per intervalli lineari (tempo d'intervallo costante) ed esponenziali con sequenza casuale. Esiste anche un criterio di tipo "nessun tentativo" se la gestione dei tentativi viene eseguita da un altro processo a un livello superiore. Se, tuttavia, sono presenti requisiti specifici non previsti dalle classi incorporate, è possibile implementare classi di ripetizione dei tentativi personalizzate.

Se si usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è possibile, ad esempio, alternare i tentativi tra il percorso del servizio di archiviazione primario e quello del servizio secondario e il risultato della richiesta è un errore non irreversibile. Per altre informazioni, vedere [Opzioni di ridondanza dell'archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).

### Configurazione dei criteri (archiviazione di Azure)

I criteri di ripetizione dei tentativi sono configurati a livello di codice. La procedura tipica consiste nel creare e popolare un'istanza **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions** o **QueueRequestOptions**.

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

L'istanza delle opzioni di richiesta può essere quindi impostata sul client in modo che tutte le operazioni eseguite sul client usino le opzioni di richiesta specificate.

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

È comunque possibile ignorare le opzioni di richiesta del client passando ai metodi dell'operazione un'istanza popolata della classe delle opzioni di richiesta sotto forma di parametro.

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

Si userà in questo caso un'istanza **OperationContext** per specificare il codice da eseguire quando si ripete un tentativo o quando, invece, viene completata un'operazione. Questo codice è in grado di raccogliere informazioni sull'operazione, da usare nei log e nella telemetria.

	// Set up notifications for an operation
	var context = new OperationContext();
	context.ClientRequestID = "some request id";
	context.Retrying += (sender, args) =>
	{
	  /* Collect retry information */
	};
	context.RequestCompleted += (sender, args) =>
	{
	  /* Collect operation completion information */
	};
	var stats = await client.GetServiceStatsAsync(null, context);

Oltre a indicare se un errore può essere risolto eseguendo nuovi tentativi, i criteri estesi di ripetizione dei tentativi restituiscono un oggetto **RetryContext**, che indica il numero di tentativi, i risultati dell'ultima richiesta e se il tentativo successivo verrà eseguito nel percorso primario o secondario (vedere la tabella seguente per maggiori dettagli). Le proprietà dell'oggetto **RetryContext** possono essere inoltre usate per decidere se e quando eseguire un nuovo tentativo. Per altre informazioni, vedere [IExtendedRetryPolicy.Evaluate Method](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).

La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.

| **Contesto** | **Impostazione** | **Valore predefinito** | **Significato** |
|--------------------------|-------------------------------------------------------------|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| QueueRequestOptions<br />
Tabella / Blob / File | MaximumExecutionTime<br />
<br />
ServerTimeout<br />
<br />
<br />
<br />
<br />
LocationMode<br />
<br />
<br />
<br />
<br />
<br />
<br />
RetryPolicy | 120 secondi<br />
<br />
Nessuno<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
ExponentialPolicy | Tempo di esecuzione massimo per la richiesta, inclusi tutti i potenziali tentativi.<br />
Intervallo di timeout del server per la richiesta (il valore viene arrotondato a secondi). Se non specificato, verrà usato il valore predefinito per tutte le richieste al server. In genere, la scelta migliore è omettere questa impostazione in modo che venga usato il valore predefinito del server.<br />
Se l'account di archiviazione viene creato con l'opzione di replica "archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)", è possibile usare la modalità percorso per indicare in corrispondenza di quale percorso deve essere ricevuta la richiesta. Ad esempio, se è specificata l'opzione **PrimaryThenSecondary**, le richieste vengono sempre inviate prima al percorso primario. Se una richiesta ha esito negativo, viene quindi inviata al percorso secondario.<br />
Per informazioni dettagliate su ogni opzione, vedere le sezioni seguenti. |
| Criteri esponenziali | maxAttempt<br />
deltaBackoff<br />
<br />
<br />
MinBackoff<br />
<br />
MaxBackoff | 3<br />
4 secondi<br />
<br />
<br />
3 secondi<br />
<br />
30 secondi | Numero di tentativi.<br />
Intervallo di backoff tra i tentativi. Per i tentativi successivi verranno usati multipli di questo intervallo di tempo, combinati con un elemento casuale.<br />
I valori vengono aggiunti a tutti gli intervalli tra i tentativi, calcolati a partire da deltaBackoff. Questo valore non può essere modificato.<br />
MaxBackoff viene usato se l'intervallo tra i tentativi è maggiore di MaxBackoff. Questo valore non può essere modificato. |
| Criteri lineari | maxAttempt<br />
deltaBackoff | 3<br />
30 secondi | Numero di tentativi.<br />
Intervallo di backoff tra i tentativi. |

### Linee guida sull'uso dei criteri di ripetizione dei tentativi
Quando si accede ai servizi di archiviazione di Azure tramite l'API del client di archiviazione, tenere presenti le linee guida seguenti:

* Usare i criteri di ripetizione dei tentativi dello spazio dei nomi Microsoft.WindowsAzure.Storage.RetryPolicies se appropriati per le proprie esigenze. Nella maggior parte dei casi, questi criteri saranno sufficienti.
* Usare invece i criteri **ExponentialRetry** nelle operazioni in batch, nelle attività in background e negli scenari non interattivi. In questi scenari, è generalmente possibile concedere più tempo per il ripristino del servizio, aumentando così le probabilità che l'operazione abbia esito positivo.
* Valutare la possibilità di specificare la proprietà **MaximumExecutionTime** del parametro **RequestOptions** per limitare il tempo di esecuzione complessivo, ma tenere conto del tipo e delle dimensioni dell'operazione quando si sceglie un valore di timeout.
* Se è necessario implementare un meccanismo di ripetizione dei tentativi personalizzato, evitare di creare wrapper per le classi dei client di archiviazione. Usare invece questa funzionalità per estendere i criteri esistenti tramite l'interfaccia **IExtendedRetryPolicy**.
* Se si usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è possibile adottare la modalità **LocationMode** per specificare che i nuovi tentativi accederanno alla copia secondaria di sola lettura dell'archivio, nel caso in cui l'accesso alla versione principale abbia esito negativo. Quando si usa questa opzione, tuttavia, è necessario verificare che l'applicazione possa gestire correttamente anche dati non aggiornati, nel caso in cui la replica dall'archivio primario non sia ancora stata completata.

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.

| **Contesto** | **Destinazione di esempio E2E<br />
latenza massima** | **Criteri di ripetizione** | **Impostazioni** | **Valori** | **Funzionamento** |
|----------------------|-----------------------------------|------------------|-------------------------|-------------|-----------------------------------------------------------------------------|
| Interattivo, interfaccia utente<br />
o in primo piano | 2 secondi | Lineari | maxAttempt<br />
deltaBackoff | 3<br />
500 ms | Tentativo di 1 - intervallo di 500 ms<br />
Tentativo 2 - intervallo di 500 ms<br />
Tentativo 3 - intervallo di 500 ms |
| Background<br />
o batch | 30 secondi | Esponenziali | maxAttempt<br />
deltaBackoff | 5<br />
4 secondi | Tentativo di 1 - intervallo di \~3 sec<br />
Tentativo 2 - intervallo di \~7 sec<br />
Tentativo 3 - intervallo di \~15 sec |

## Telemetria

Il numero di tentativi viene registrato in un'origine **TraceSource**. È necessario configurare un oggetto **TraceListener** per acquisire gli eventi e scriverli in un log di destinazione appropriato. È possibile usare **TextWriterTraceListener** o **XmlWriterTraceListener** per scrivere i dati in un file di log, **EventLogTraceListener** per scriverli nel Registro eventi di Windows o **EventProviderTraceListener** per scrivere i dati di traccia nel sottosistema ETW. È inoltre possibile configurare lo svuotamento automatico del buffer e il livello di dettaglio degli eventi che verranno registrati nel log (specificando, ad esempio, i valori Error, Warning, Informational e Verbose). Per altre informazioni, vedere [Registrazione lato client con la libreria client di archiviazione .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx).

Le operazioni possono ricevere un'istanza **OperationContext**, che espone un evento **Retrying** di cui è possibile usufruire per associare una logica personalizzata per i dati telemetrici. Per altre informazioni, vedere [Evento OperationContext.Retrying](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).

## Esempi (archiviazione di Azure)

L'esempio di codice seguente mostra come creare due istanze **TableRequestOptions** con due diverse impostazioni di ripetizione dei tentativi: una per le richieste interattive e una per le richieste in background. L'esempio imposta quindi questi due criteri di ripetizione dei tentativi in modo che vengano applicati a tutte le richieste. Imposta inoltre la strategia interattiva su una richiesta specifica in modo che ignori le impostazioni predefinite applicate al client.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

## Altre informazioni

- [Suggerimenti per i criteri di ripetizione dei tentativi nella libreria client dell'archiviazione di Azure](http://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
- [Libreria client di archiviazione 2.0 - Implementazione dei criteri di ripetizione dei tentativi](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## Database SQL con Entity Framework 6 - Linee guida per la ripetizione di tentativi

Il database SQL, in questo caso, è un database SQL ospitato, di varie dimensioni, disponibile come servizio standard (condiviso) o premium (non condiviso). Entity Framework, invece, è un mapper relazionale a oggetti che consente agli sviluppatori .NET di usare dati relazionali mediante oggetti specifici di dominio. Elimina la necessità di gran parte del codice di accesso ai dati che in genere gli sviluppatori devono scrivere.

## Meccanismo di ripetizione dei tentativi

Quando si accede al database SQL con Entity Framework 6.0 o versione superiore, il supporto per la ripetizione di tentativi viene fornito attraverso un meccanismo denominato [resilienza delle connessioni / logica di ripetizione dei tentativi](http://msdn.microsoft.com/data/dn456835.aspx). Una specifica completa è disponibile nell'articolo wiki [.NET Entity Framework](https://entityframework.codeplex.com/wikipage?title=Connection%20Resiliency%20Spec) su Codeplex. Di seguiti sono illustrate le caratteristiche principali del meccanismo di ripetizione dei tentativi:

* L'astrazione primaria è l'interfaccia **IDbExecutionStrategy**. Questa interfaccia:
  * Definisce i metodi **Execute*** sincroni e asincroni.
  * Definisce le classi che possono essere usate direttamente o configurate in un contesto di database come strategia predefinita, quindi mappate a un nome di provider o a un nome di provider e un nome di server. Se configurate in un contesto, i nuovi tentativi vengono eseguiti a livello di singole operazioni di database, che possono essere molteplici per una determinata operazione di contesto.
  * Definisce quando tentare di eseguire nuovamente una connessione non riuscita e in che modo.
* Include diverse implementazioni incorporate dell'interfaccia **IDbExecutionStrategy**:
  * Predefinita - Nessun nuovo tentativo.
  * Predefinita per il database SQL (automatica) - Nessun nuovo tentativo, ma controlla le eccezioni e ne esegue il wrapping con il suggerimento di usare la strategia relativa al database SQL.
  * Predefinita per il database SQL - Esponenziale (ereditata dalla classe di base), combinata con la logica di rilevamento del database SQL.
* Implementa una strategia di backoff esponenziale che include la sequenza casuale.
* Le classi di ripetizione dei tentativi incorporate sono classi con stato di tipo non thread-safe. Possono comunque essere riusate dopo il completamento dell'operazione corrente.
* Se viene superato il numero di tentativi specificato, i risultati vengono inclusi in una nuova eccezione e l'eccezione corrente non viene visualizzata.

## Configurazione dei criteri (database SQL con Entity Framework 6)

Quando si accede al database SQL con Entity Framework 6.0, o versioni successive, è già disponibile il supporto per la ripetizione di tentativi. I criteri di ripetizione dei tentativi sono configurati a livello di codice. La configurazione non può essere modificata a livello di singola operazione.

Quando si configura una strategia di contesto come impostazione predefinita, è necessario specificare anche una funzione che consente di creare una nuova strategia su richiesta. Il codice seguente mostra come creare una classe di configurazione di nuovi tentativi che estende la classe di base **DbConfiguration**.

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

È quindi possibile impostarla come strategia di ripetizione dei tentativi predefinita per tutte le operazioni usando il metodo **SetConfiguration** dell'istanza **DbConfiguration** all'avvio dell'applicazione. Per impostazione predefinita, Entity Framework rileverà e userà automaticamente questa classe di configurazione.

	DbConfiguration.SetConfiguration(new BloggingContextConfiguration());

È possibile specificare la classe di configurazione di nuovi tentativi per un contesto specifico annotando la classe del contesto con un attributo **DbConfigurationType**. Tuttavia, se è presente una sola classe di configurazione, Entity Framework la userà anche senza dover annotare il contesto.

	[DbConfigurationType(typeof(BloggingContextConfiguration))]
	public class BloggingContext : DbContext
	{ ...

Se per determinate operazioni è necessario usare strategie di ripetizione dei tentativi diverse o si disabilita la ripetizione dei tentativi, è possibile creare una classe di configurazione che consente di sospendere o cambiare strategie impostando un flag in **CallContext**. Questo flag può essere usato dalla classe di configurazione per cambiare strategie o per disabilitare la strategia specificata e usare invece una strategia predefinita. Per altre informazioni, vedere la sezione sulla [sospensione della strategia di esecuzione](http://msdn.microsoft.com/dn307226#transactions_workarounds) nella pagina relativa alle limitazioni quando si ripetono strategie di esecuzione (Entity Framework 6 o versione successiva).

Un'altra tecnica per usare strategie di ripetizione specifiche per singole operazioni consiste nel creare un'istanza della classe di strategia necessaria, fornire le impostazioni desiderate attraverso dei parametri e quindi richiamare il metodo **ExecuteAsync**.

	var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
	var blogs = await executionStrategy.ExecuteAsync(
	    async () =>
	    {
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Acquire some values asynchronously and return them
	        }
	    },
	    new CancellationToken()
	);

Il modo più semplice per usare una classe **DbConfiguration** consiste nell'individuarla nello stesso assembly della classe **DbContext**. Questo metodo, tuttavia, non è appropriato quando lo stesso contesto è richiesto in scenari diversi, ad esempio in varie strategie di ripetizione dei tentativi, interattiva o in background. Se i diversi contesti vengono eseguiti in AppDomain distinti, è possibile usare il supporto incorporato per specificare le classi di configurazione nel file di configurazione o impostarle in modo esplicito tramite codice. Se invece i diversi contesti devono essere eseguiti nello stesso AppDomain, sarà necessaria una soluzione personalizzata.

Per altre informazioni, vedere l'articolo sulla [configurazione basata su codice (Entity Framework 6 o versione successiva)](http://msdn.microsoft.com/data/jj680699.aspx).

La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati quando si usa Entity Framework 6.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable4.png)
## Linee guida sull'uso dei criteri di ripetizione dei tentativi

Quando si accede al database SQL con Entity Framework 6, tenere presente le linee guida seguenti:

* Scegliere il tipo di servizio appropriato (condiviso o premium). Un'istanza condivisa può subire limitazioni o ritardi di connessione maggiori rispetto alla media perché può essere usata da altri tenant del server condiviso. Se sono necessarie prestazioni prevedibili o affidabili operazioni di bassa latenza, valutare la possibilità di scegliere il tipo servizio premium.
* Per il database SQL di Azure non è consigliabile usare una strategia a intervalli fissi, bensì una strategia di backoff esponenziale, poiché il servizio può essere sovraccarico e intervalli più lunghi concedono più tempo per il ripristino del servizio.
* Quando si definiscono le connessioni, scegliere un valore appropriato per i timeout di connessione e dei comandi. I timeout devono essere definiti in base alla progettazione della logica di business e tramite una serie di test. Potrebbe essere necessario modificare questi valori nel tempo man mano che i volumi dei dati o dei processi di business cambiano. Un timeout troppo breve può comportare errori di connessione prematuri quando il database è occupato, mentre un timeout troppo lungo può impedire che la logica di ripetizione dei tentativi funzioni correttamente, poiché l'intervallo di attesa prima che venga rilevata una connessione non riuscita è troppo lungo. Sebbene il valore di timeout sia un componente della latenza end-to-end, non è possibile determinare facilmente il numero di comandi che verranno eseguiti durante il salvataggio del contesto. È possibile modificare il timeout predefinito impostando la proprietà **CommandTimeout** dell'istanza **DbContext**.
* Entity Framework supporta le configurazioni di ripetizione dei tentativi definite nei file di configurazione. Per ottenere la massima flessibilità in Azure, tuttavia, è opportuno valutare la possibilità di creare la configurazione a livello di codice all'interno dell'applicazione. I parametri specifici dei criteri di ripetizione dei tentativi, ad esempio il numero di tentativi e l'intervallo tra di essi, possono essere archiviati nel file di configurazione del servizio e usati in fase di esecuzione per creare i criteri appropriati. In questo modo, è possibile modificare le impostazioni senza dover riavviare l'applicazione.

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Non è possibile specificare l'intervallo tra i tentativi (si tratta di un valore fisso generato come sequenza esponenziale); è possibile specificare solo i valori massimi, come illustrato di seguito, a meno che non sia stata creata una strategia di ripetizione dei tentativi personalizzata. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.

| **Contesto** | **Destinazione di esempio E2E<br />
latenza massima** | **Criteri di ripetizione** | **Impostazioni** | **Valori** | **Funzionamento** |
|----------------------|-----------------------------------|--------------------|------------------------|--------------|-----------------------------------------------------------------------------------------------------------------------------|
| Interattivo, interfaccia utente<br />
o in primo piano | 2 secondi | Esponenziali | MaxRetryCount<br />
MaxDelay | 3<br />
750 ms | Tentativo di 1 - intervallo di 0 sec<br />
Tentativo 2 - intervallo di 750 ms<br />
Tentativo 3 - intervallo di 750 ms |
| Background<br />
 o batch | 30 secondi | Esponenziali | MaxRetryCount<br />
MaxDelay | 5<br />
12 secondi | Tentativo 1 - intervallo di 0 sec<br />
Tentativo 2 - intervallo di \~1 sec<br />
Tentativo 3 - intervallo di \~3 sec<br />
Tentativo 4 - intervallo di \~7 sec<br />
Tentativo 5 - intervallo di 12 sec |

> [AZURE.NOTE]Gli obiettivi di latenza end-to-end presuppongono il timeout predefinito per le connessioni al servizio. Se si specifica un timeout di connessione più lungo, la latenza end-to-end verrà estesa di questo intervallo di tempo aggiuntivo per ogni nuovo tentativo.

## Esempi (Database SQL con Entity Framework 6)

L'esempio di codice seguente definisce una soluzione di accesso ai dati semplice che usa Entity Framework. Imposta una strategia di ripetizione dei tentativi specifica mediante la definizione di un'istanza di una classe denominata **BlogConfiguration** che estende **DbConfiguration**.

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
	public class BlogConfiguration : DbConfiguration
	{
	    public BlogConfiguration()
	    {
	        // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
	        // These values could be loaded from configuration rather than being hard-coded.
	        this.SetExecutionStrategy(
	                "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
	    }
	}

	// Specify the configuration type if more than one has been defined.
	// [DbConfigurationType(typeof(BlogConfiguration))]
	public class BloggingContext : DbContext
	{
	    // Definition of content goes here.
	}

	class EF6CodeSamples
	{
	    public async static Task Samples()
	    {
	        // Execution strategy configured by DbConfiguration subclass, discovered automatically or
	        // or explicitly indicated through configuration or with an attribute. Default is no retries.
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Add, edit, delete blog items here, then:
	            await db.SaveChangesAsync();
	        }
	    }
	}
}
```

Altri esempi sull'uso del meccanismo di ripetizione dei tentavi in Entity Framework sono disponibili in [resilienza delle connessioni / logica di ripetizione dei tentativi](http://msdn.microsoft.com/data/dn456835.aspx).

## Altre informazioni

* [Guida relativa all'elasticità e alle prestazioni del database SQL di Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## Database SQL con ADO.NET - Linee guida per la ripetizione di tentativi

Il database SQL, in questo caso, è un database SQL ospitato, di varie dimensioni, disponibile come servizio standard (condiviso) o premium (non condiviso).

### Meccanismo di ripetizione dei tentativi

Il database SQL non offre il supporto incorporato per la ripetizione dei tentativi quando si accede ad esso con ADO.NET. Per determinare il motivo per cui una richiesta ha avuto esito negativo, è possibile usare i codici restituiti dalle richieste. La pagina [Limitazione del database SQL di Azure](http://msdn.microsoft.com/library/dn338079.aspx) spiega come la limitazione possa impedire le connessioni, descrive i codici restituiti dalle richieste in situazioni specifiche e mostra come gestire tali situazioni e ritentare l'esecuzione delle operazioni.

È possibile usare il Blocco di applicazioni per la gestione degli errori temporanei (Topaz) con il pacchetto Nuget EnterpriseLibrary.TransientFaultHandling.Data (classe **SqlAzureTransientErrorDetectionStrategy**) per implementare un meccanismo di ripetizione dei tentativi per il database SQL.

Il blocco offre inoltre la classe **ReliableSqlConnection**, che implementa l'API di ADO.NET 1.0 precedente (**IDbConnection** anziché **DbConnection**) ed esegue internamente la ripetizione dei tentativi e la gestione delle connessioni. Questa soluzione, seppure molto pratica, richiede l'uso di un diverso set di metodi per richiamare operazioni con nuovi tentativi e, pertanto, non costituisce una semplice sostituzione diretta. Non supporta inoltre l'esecuzione asincrona, che invece è consigliabile quando si implementano e usano servizi di Azure. Ma non solo: questa classe usa ADO.NET 1.0 e quindi non beneficia dei recenti miglioramenti e aggiornamenti apportati ad ADO.NET.

### Configurazione dei criteri (database SQL con ADO.NET)

Il Blocco di applicazioni per la gestione degli errori temporanei supporta la configurazione sia basata su file sia a livello di codice. In generale, è consigliabile usare la configurazione a livello di codice per ottenere la massima flessibilità (per altre informazioni, vedere le note nella sezione seguente). Il codice seguente, che deve essere eseguito dopo l'avvio dell'applicazione, crea e popola un oggetto **RetryManager** con un elenco di quattro strategie di ripetizione dei tentativi appropriate per il database SQL di Azure. Imposta inoltre le strategie predefinite per l'oggetto **RetryManager**, che verranno usate per le connessioni e i comandi se non si specifica un'alternativa durante la creazione di una connessione o un comando.

```csharp
RetryManager.SetDefault(new RetryManager(
	new List<RetryStrategy> { new ExponentialBackoff(name: "default", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql connection", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql command", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "alt sql", retryCount: 5,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true), },
	"default",
	new Dictionary<string, string> {
	    {
	    RetryManagerSqlExtensions.DefaultStrategyConnectionTechnologyName, "default sql connection"
	    },
	    {
	    RetryManagerSqlExtensions.DefaultStrategyCommandTechnologyName, "default sql command"}
	    }));
```

Per informazioni su come usare i criteri di ripetizione dei tentativi configurati quando si accede al database SQL di Azure, vedere la sezione degli [esempi](#examples-sql-database-using-ado-net-) riportata di seguito.

Le strategie predefinite per il Blocco di applicazioni per la gestione degli errori temporanei sono illustrate nella sezione [Strategie per il Blocco di applicazioni per la gestione degli errori temporanei (Topaz)](#transient-fault-handling-application-block-topaz-strategies) nella parte finale dell'articolo.

### Linee guida sull'uso dei criteri di ripetizione dei tentativi

Quando si accede al database SQL con ADO.NET, tenere presente le linee guida seguenti:

* Scegliere il tipo di servizio appropriato (condiviso o premium). Un'istanza condivisa può subire limitazioni o ritardi di connessione maggiori rispetto alla media perché può essere usata da altri tenant del server condiviso. Se sono necessarie prestazioni più prevedibili o affidabili operazioni di bassa latenza, valutare la possibilità di scegliere il tipo servizio premium.
* Assicurarsi di eseguire i nuovi tentativi al livello o nell'ambito appropriato per evitare operazioni non idempotenti che possono causare incoerenze nei dati. In teoria, tutte le operazioni dovrebbero essere idempotenti in modo da poter essere ripetute senza causare incoerenze. Se questo non avviene, i nuovi tentativi devono essere eseguiti a un livello o in un ambito che consenta l'annullamento di tutte le modifiche correlate in caso di esito negativo di un'operazione, ad esempio un ambito transazionale. Per altre informazioni, vedere l'articolo sul [livello di accesso ai dati di Cloud Service Fundamentals e la gestione degli errori temporanei](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).
* Non è consigliabile una strategia basata su intervalli fissi per il database SQL di Azure, se non negli scenari interattivi in cui vengono eseguiti solo alcuni tentativi a intervalli molto brevi. In alternativa, valutare la possibilità di usare una strategia di backoff esponenziale per la maggior parte degli scenari.
* Quando si definiscono le connessioni, scegliere un valore appropriato per i timeout di connessione e dei comandi. Un timeout troppo breve può comportare errori di connessione prematuri quando il database è occupato, mentre un timeout troppo lungo può impedire che la logica di ripetizione dei tentativi funzioni correttamente, poiché l'intervallo di attesa prima che venga rilevata una connessione non riuscita è troppo lungo. Il valore di timeout è un componente della latenza end-to-end; per ogni nuovo tentativo, viene aggiunto all'intervallo tra i tentativi specificato nei criteri di ripetizione dei tentativi.
* Chiudere la connessione dopo un certo numero di nuovi tentativi, anche se si usa una logica di ripetizione dei tentativi con backoff esponenziale, e ripetere l'operazione su una nuova connessione. Se si ripete più volte la stessa operazione su un'unica connessione, infatti, è possibile che si verifichino problemi di connessione. Per altre informazioni su questa tecnica, vedere l'articolo sul [livello di accesso ai dati di Cloud Service Fundamentals e la gestione degli errori temporanei](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).
* Se si usa il pool di connessioni (impostazione predefinita), è probabile che venga scelta la stessa connessione, anche dopo aver chiuso e riaperto una connessione. Se si verifica questo problema, è possibile risolverlo chiamando il metodo **ClearPool** della classe **SqlConnection** per contrassegnare la connessione come non riutilizzabile. Questo metodo, tuttavia, deve essere adottato solo dopo vari tentativi di connessione non riusciti e solo quando è presente la classe di errori temporanei, come i timeout SQL (codice di errore -2), specifica delle connessioni difettose.
* Se il codice di accesso ai dati usa transazioni avviate come istanze **TransactionScope**, la logica di ripetizione dei tentativi deve riaprire la connessione e avviare un nuovo ambito della transazione. Per questo motivo, il blocco di codice non irreversibile deve interessare l'intero ambito della transazione.
* Il Blocco di applicazioni per la gestione degli errori temporanei supporta configurazioni di ripetizione dei tentativi interamente definite nei file di configurazione. Per ottenere la massima flessibilità in Azure, tuttavia, è opportuno valutare la possibilità di creare la configurazione a livello di codice all'interno dell'applicazione. I parametri specifici dei criteri di ripetizione dei tentativi, ad esempio il numero di tentativi e l'intervallo tra di essi, possono essere archiviati nel file di configurazione del servizio e usati in fase di esecuzione per creare i criteri appropriati. In questo modo, è possibile modificare le impostazioni senza dover riavviare l'applicazione.

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.

| **Contesto** | **Destinazione di esempio E2E<br />
latenza massima** | **Strategia di ripetizione dei tentativi** | **Impostazioni** | **Valori** | **Funzionamento** |
|----------------------|-----------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interattivo, interfaccia utente<br />
o in primo piano | 2 secondi | FixedInterval | Numero tentativi<br />
Intervallo tra tentativi<br />
Primo tentativo rapido | 3<br />
500 ms<br />
true | Tentativo 1 - intervallo di 0 sec<br />
Tentativo 2 - intervallo di 500 ms<br />
Tentativo 3 - intervallo di 500 ms |
| Background<br />
o batch | 30 secondi | ExponentialBackoff | Numero tentativi<br />
Backoff minimo<br />
Backoff massimo<br />
Backoff delta<br />
Primo tentativo veloce | 5<br />
0 sec<br />
60 sec<br />
2 sec<br />
false | Tentativo 1 - intervallo di 0 sec<br />
Tentativo 2 - intervallo di \~2 sec<br />
Tentativo 3 - intervallo di \~6 sec<br />
Tentativo 4 - intervallo di \~14 sec<br />
Tentativo 5 - intervallo di 30 sec |

> [AZURE.NOTE]Gli obiettivi di latenza end-to-end presuppongono il timeout predefinito per le connessioni al servizio. Se si specifica un timeout di connessione più lungo, la latenza end-to-end verrà estesa di questo intervallo di tempo aggiuntivo per ogni nuovo tentativo.

### Esempi (Database SQL con ADO.NET)

Questa sezione descrive come usare il Blocco di applicazioni per la gestione degli errori temporanei per accedere al database SQL di Azure mediante un set di criteri di ripetizione dei tentativi configurato nell'oggetto **RetryManager** (come illustrato nella sezione precedente [Configurazione dei criteri](#policy-configuration-sql-database-using-ado-net-)). Per accedere al blocco, è possibile usare la classe **ReliableSqlConnection** oppure chiamare i metodi di estensione come **OpenWithRetry** su una connessione (per altre informazioni, vedere [Blocco di applicazioni per la gestione degli errori temporanei](http://msdn.microsoft.com/library/hh680934.aspx)).

Nella versione corrente del Blocco di applicazioni per la gestione degli errori temporanei, tuttavia, questi approcci non supportano in modo nativo operazioni asincrone sul database SQL. È buona norma usare solo tecniche asincrone per accedere ai servizi di Azure come il database SQL ed è quindi opportuno avvalersi di una delle tecniche seguenti per usare il Blocco di applicazioni per la gestione degli errori temporanei con il database SQL.

Nella versione 5 del linguaggio C#, è possibile usare il supporto asincrono semplificato per creare versioni asincrone dei metodi indicati dal blocco. Il codice seguente, ad esempio, mostra come creare una versione asincrona del metodo di estensione **ExecuteReaderWithRetry**. Sono evidenziate le parti modificate o aggiunte rispetto al codice originale. Il codice sorgente per Topaz è disponibile nell'articolo [Blocco di applicazioni per la gestione degli errori temporanei ("Topaz")](http://topaz.codeplex.com/SourceControl/latest) di GitHub.

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command, RetryPolicy cmdRetryPolicy,
RetryPolicy conRetryPolicy)
{
	GuardConnectionIsNotNull(command);

	// Check if retry policy was specified, if not, use the default retry policy.
	return await (cmdRetryPolicy ?? RetryPolicy.NoRetry).ExecuteAsync(async () =>
	{
	    var hasOpenConnection = await EnsureValidConnectionAsync(command, conRetryPolicy).ConfigureAwait(false);

	    try
	    {
	        return await command.ExecuteReaderAsync().ConfigureAwait(false);
	    }
	    catch (Exception)
	    {
	        if (hasOpenConnection && command.Connection != null && command.Connection.State == ConnectionState.Open)
	        {
	            command.Connection.Close();
	        }

	        throw;
	    }
	}).ConfigureAwait(false);
}
```

Questo nuovo metodo di estensione asincrona può essere usato nello stesso modo in cui vengono usate le versioni sincrone incluse nel blocco.

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

var retryPolicy =
	RetryManager.Instance.GetRetryPolicy<SqlDatabaseTransientErrorDetectionStrategy>("alt sql");
using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync(retryPolicy))
{
	// Do something with the values
}
```

Questo approccio, tuttavia, prevede solo operazioni o comandi individuali e non blocchi di istruzioni in cui è possibile che siano correttamente definiti limiti transazionali. Non consente inoltre la rimozione di connessioni difettose dal pool di connessioni per impedire che non vengano selezionate in tentativi successivi. Un esempio sincrono di risoluzione di questi problemi è disponibile nell'articolo sul [livello di accesso ai dati di Cloud Service Fundamentals e la gestione degli errori temporanei.](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Timeouts_amp_Connection_Management). Oltre a ritentare sequenze arbitrarie di istruzioni di database, cancella il pool di connessioni per rimuovere eventuali connessioni non valide e instrumenta l'intero processo. Sebbene il codice illustrato in questo esempio sia sincrono, convertirlo in codice asincrono è relativamente semplice.

### Altre informazioni

Per informazioni dettagliate sull'uso del Blocco di applicazioni per la gestione degli errori temporanei, vedere:

* [Uso del Blocco di applicazioni per la gestione degli errori temporanei con SQL Azure](http://msdn.microsoft.com/library/hh680899.aspx)
* [Perseveranza, il segreto di tutti i successi: uso del Blocco di applicazioni per la gestione degli errori temporanei](http://msdn.microsoft.com/library/dn440719.aspx)
* [Livello di accesso ai dati di Cloud Service Fundamentals e gestione degli errori temporanei](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).

Per indicazioni generali su come sfruttare al meglio le potenzialità del database SQL, vedere:

* [Guida relativa all'elasticità e alle prestazioni del database SQL di Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)
* [Drastica riduzione degli errori del pool di connessioni in SQL Azure](http://blogs.msdn.com/b/adonet/archive/2011/11/05/minimizing-connection-pool-errors-in-sql-azure.aspx)

## Bus di servizio - Linee guida per la ripetizione di tentativi

Il bus di servizio è una piattaforma di messaggistica basata sul cloud che offre una funzione di scambio dei messaggi di tipo "loosely coupled" con alti livelli di scalabilità e resilienza per i componenti di un'applicazione (ospitata nel cloud o in locale).

### Meccanismo di ripetizione dei tentativi

Il bus di servizio implementa la ripetizione dei tentativi usando implementazioni della classe di base [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx). Tutti i client del bus di servizio espongono una proprietà **RetryPolicy** che può essere impostata in una delle implementazioni della classe di base **RetryPolicy**. Le implementazioni predefinite sono:

* La [classe RetryExponential](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx). Espone proprietà che controllano l'intervallo di backoff, il numero di tentativi e la proprietà **TerminationTimeBuffer** usata per limitare il tempo complessivo per il completamento dell'operazione.
* La [classe NoRetry](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx). Viene usata quando non è necessario eseguire nuovi tentativi al livello dell'API del bus di servizio, ad esempio quando i nuovi tentativi vengono gestiti da un altro processo nell'ambito di un'operazione in batch o composta da più passaggi.

Alcune azioni del bus di servizio possono restituire un intervallo di eccezioni, come elencato in [Appendice: eccezioni di messaggistica](http://msdn.microsoft.com/library/hh418082.aspx). L'elenco fornisce informazioni su quali delle eccezioni indicano che è opportuno ripetere l'operazione. [ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx), ad esempio, indica che il client deve attendere un determinato intervallo di tempo, quindi ripetere l'operazione. Un'occorrenza di **ServerBusyException** induce inoltre il bus di servizio a passare a una modalità diversa, in cui viene aggiunto un ulteriore intervallo di 10 secondi all'intervallo tra i tentativi elaborato. Questa modalità viene reimpostata dopo un breve intervallo di tempo.

Le eccezioni restituite dal bus di servizio espongono la proprietà **IsTransient** che indica se il client deve ripetere l'operazione. I criteri **RetryExponential** incorporati si basano sulla proprietà **IsTransient** della classe **MessagingException**, che costituisce la classe di base per tutte le eccezioni del bus di servizio. Se si creano implementazioni personalizzate della classe di base **RetryPolicy**, è possibile usare una combinazione del tipo di eccezione e della proprietà **IsTransient** per ottenere un controllo più preciso sulle azioni di ripetizione dei tentativi. Se si identifica un'eccezione di tipo **QuotaExceededException**, ad esempio, è possibile intraprendere le azioni necessarie per esaurire la coda prima di riprovare a inviare un messaggio.

### Configurazione dei criteri (bus di servizio)

I criteri di ripetizione dei tentativi vengono impostati a livello di codice e possono essere impostati come criteri predefiniti per **NamespaceManager** e **MessagingFactory** oppure impostati singolarmente per ogni client di messaggistica. Per impostare criteri di ripetizione dei tentativi predefiniti per una sessione di messaggistica, impostare la proprietà **RetryPolicy** di **NamespaceManager**.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                             maxBackoff: TimeSpan.FromSeconds(30),
	                                                             deltaBackoff: TimeSpan.FromSeconds(2),
	                                                             terminationTimeBuffer: TimeSpan.FromSeconds(5),
	                                                             maxRetryCount: 3);

Per maggiore chiarezza, questo codice usa parametri denominati. In alternativa, è possibile omettere i nomi poiché nessuno dei parametri è facoltativo.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(TimeSpan.FromSeconds(0.1),
	                 TimeSpan.FromSeconds(30), TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(5), 3);

Per impostare criteri di ripetizione dei tentativi predefiniti per tutti i client da una factory di messaggistica, impostare la proprietà **RetryPolicy** di **MessagingFactory**.

	messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                    maxBackoff: TimeSpan.FromSeconds(30),
	                                                    deltaBackoff: TimeSpan.FromSeconds(2),
	                                                    terminationTimeBuffer: TimeSpan.FromSeconds(5),
	                                                    maxRetryCount: 3);

Per impostare criteri di ripetizione dei tentativi per un client di messaggistica o per ignorare i criteri predefiniti, impostarne la proprietà **RetryPolicy** usando un'istanza della classe di criteri necessaria:

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                        maxBackoff: TimeSpan.FromSeconds(30),
	                                        deltaBackoff: TimeSpan.FromSeconds(2),
	                                        terminationTimeBuffer: TimeSpan.FromSeconds(5),
	                                        maxRetryCount: 3);
```

I criteri di ripetizione dei tentativi non possono essere impostati a livello di singola operazione. Questo vale per tutte le operazioni relative al client di messaggistica. La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable7.png)

### Linee guida sull'uso dei criteri di ripetizione dei tentativi

Quando si usa il bus di servizio, tenere presente le linee guida seguenti:

* Se si sceglie l'implementazione **RetryExponential** incorporata, non implementare un'operazione di fallback poiché i criteri reagiscono alle eccezioni di tipo "server occupato" e viene automaticamente attivata una modalità di ripetizione dei tentativi appropriata al nuovo scenario.
* Il bus di servizio supporta una funzionalità relativa agli spazi dei nomi associati che implementa il failover automatico su una coda di backup di uno spazio dei nomi diverso in caso di esito negativo della coda nello spazio dei nomi primario. I messaggi ricevuti dalla coda secondaria possono essere nuovamente inviati alla coda primaria nel momento in cui viene ripristinata. Questa funzionalità consente di risolvere eventuali problemi temporanei. Per altre informazioni, vedere [Modelli di messaggistica asincrona e disponibilità elevata](http://msdn.microsoft.com/library/azure/dn292562.aspx).

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.


![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable8.png)

### Telemetria

Il bus di servizio registra i tentativi come eventi ETW tramite un oggetto **EventSource**. È necessario associare un **EventListener** all'origine eventi per acquisire gli eventi e visualizzarli nel visualizzatore delle prestazioni o scriverli in un log di destinazione appropriato. A questo scopo, è possibile usare il [blocco applicativo di registrazione semantica](http://msdn.microsoft.com/library/dn775006.aspx). Gli eventi di ripetizione dei tentativi sono caratterizzati dal formato seguente:

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### Esempi (bus di servizio)

L'esempio di codice seguente illustra come impostare i criteri di ripetizione dei tentativi per:

* Un gestore dello spazio dei nomi. I criteri si applicano a tutte le operazioni eseguite sul gestore e non possono essere sostituiti a favore di singole operazioni.
* Una factory di messaggistica. I criteri si applicano a tutti i client creati dalla factory e non possono essere sostituiti durante la creazione di client individuali.
* Un singolo client di messaggistica. Dopo aver creato un client, è possibile impostarne i criteri di ripetizione dei tentativi. I criteri di applicano a tutte le operazioni eseguite sul client.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
	class ServiceBusCodeSamples
	{
		private const string connectionString =
		    @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
		        SharedAccessKeyName=RootManageSharedAccessKey;
		        SharedAccessKey=C99..........Mk=";

		public async static Task Samples()
		{
		    const string QueueName = "TestQueue";

		    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

		    var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

		    // The namespace manager will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for all operations on the namespace manager.
		        namespaceManager.Settings.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                deltaBackoff: TimeSpan.FromSeconds(1.75),
		                terminationTimeBuffer: TimeSpan.FromSeconds(4),
		                maxRetryCount: 3);

		        // Policies cannot be specified on a per-operation basis.
		        if (!await namespaceManager.QueueExistsAsync(QueueName))
		        {
		            await namespaceManager.CreateQueueAsync(QueueName);
		        }
		    }


		    var messagingFactory = MessagingFactory.Create(
		        namespaceManager.Address, namespaceManager.Settings.TokenProvider);
		    // The messaging factory will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for clients created from it.
		        messagingFactory.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                deltaBackoff: TimeSpan.FromSeconds(2),
		                terminationTimeBuffer: TimeSpan.FromSeconds(5),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await messagingFactory.AcceptMessageSessionAsync();
		    }


		    {
		        var client = messagingFactory.CreateQueueClient(QueueName);
		        // The client inherits the policy from the factory that created it.


		        // Set different values for the retry policy on the client.
		        client.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0.1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                deltaBackoff: TimeSpan.FromSeconds(2),
		                terminationTimeBuffer: TimeSpan.FromSeconds(5),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await client.AcceptMessageSessionAsync();
		    }
		}
	}
}
```

## Altre informazioni

* [Modelli di messaggistica asincrona e disponibilità elevata](http://msdn.microsoft.com/library/azure/dn292562.aspx).

## Cache (Redis) - Linee guida per la ripetizione di tentativi

Cache Redis di Azure è un servizio cache a bassa latenza e di rapido accesso ai dati basato sulla nota Cache Redis open source. È un servizio protetto, gestito da Microsoft e accessibile da qualsiasi applicazione in Azure.

Le indicazioni fornite in questa sezione presuppongono che si usi il client StackExchange.Redis per accedere alla cache. Nel [sito Web di Redis](http://redis.io/clients) è disponibile un elenco di altri client idonei, a cui possono essere associati vari meccanismi di ripetizione dei tentativi.

Il client StackExchange.Redis usa il multiplexing tramite un'unica connessione. È consigliabile quindi creare un'istanza del client all'avvio dell'applicazione e usarla per tutte le operazioni eseguite sulla cache. In questo modo, la connessione alla cache viene eseguita una sola volta e tutte le indicazioni fornite in questa sezione fanno riferimento ai criteri di ripetizione dei tentativi definiti per la connessione iniziale e non per ogni operazione che accede alla cache.

### Meccanismo di ripetizione dei tentativi

Il client StackExchange.Redis usa una classe di gestione della connessione configurata tramite un set di opzioni, che include anche la proprietà **ConnectRetry**, in cui si specifica il numero di volte in cui si deve ripetere una connessione non riuscita alla cache. I criteri di ripetizione dei tentativi, tuttavia, vengono usati solo per l'azione di connessione iniziale e non si prevedono tempi di attesa tra i tentativi.

### Configurazione dei criteri (Cache Redis di Azure)

I criteri di ripetizione dei tentativi vengono configurati a livello di codice impostando le opzioni per il client prima di connettersi alla cache. Questa operazione può essere eseguita creando un'istanza della classe **ConfigurationOptions**, popolandone le proprietà e passandola al metodo **Connect**.

```csharp
var options = new ConfigurationOptions { EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000 };
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

La proprietà **ConnectTimeout** specifica il tempo massimo di attesa (in millisecondi), non l'intervallo di tempo tra i tentativi.

In alternativa, è possibile specificare le opzioni sotto forma di stringa, per poi passarla al metodo **Connect**.

```csharp
	var options = "localhost,connectRetry=3,connectTimeout=2000";
	ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

È anche possibile specificare le opzioni direttamente durante la connessione alla cache.

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.

| **Contesto** | **Impostazione** | **Valore predefinito**<br />
(v 1.0.331) | **Significato** |
|----------------------|-----------------------------------------|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Opzioni di configurazione | ConnectRetry<br />
<br />
ConnectTimeout<br />
<br />
SyncTimeout | 3<br />
<br />
Massimo 5000 ms più SyncTimeout<br />
1000 | Numero di nuovi tentativi di connessione durante l'operazione di connessione iniziale.<br />
Timeout (ms) per operazioni di connessione. Non un intervallo tra i tentativi.<br />
Tempo (ms) concesso per consentire operazioni sincrone. |

> [AZURE.NOTE]SyncTimeout contribuisce alla latenza end-to-end di un'operazione. Tuttavia, in generale, l'uso di operazioni sincrone non è consigliato. Per altre informazioni, vedere [Pipeline e multiplexer](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).

## Linee guida sull'uso dei criteri di ripetizione dei tentativi

Quando si usa Cache Redis di Azure, tenere presente le linee guida seguenti:

* Il client StackExchange Redis gestisce autonomamente i propri tentativi, ma solo se viene stabilita una connessione alla cache al primo avvio dell'applicazione. È possibile configurare il timeout di connessione e il numero massimo di tentativi per stabilire la connessione, ma i criteri di ripetizione non si applicano alle operazioni eseguite sulla cache.
* Il meccanismo di ripetizione dei tentativi non prevede alcun intervallo tra i tentativi. Si limita a ritentare una connessione non riuscita al termine del timeout di connessione specificato e per il numero di volte stabilito.
* Anziché ricorrere a un numero elevato di tentativi, valutare la possibilità di eseguire il fallback accedendo alla sorgente originale dei dati.

## Telemetria

È possibile raccogliere informazioni sulle connessioni, ma su non altre operazioni, usando un **TextWriter**.

```csharp
var writer = new StringWriter();
...
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Seguito è riportato un esempio di output generato.

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

## Esempi (Cache Redis di Azure)

L'esempio di codice seguente illustra come configurare l'impostazione del timeout di connessione e il numero di tentativi quando si inizializza il client StackExchange.Redis per accedere a Cache Redis di Azure all'avvio dell'applicazione. Il timeout di connessione è l'intervallo di tempo che si è disposti ad attendere per connettersi alla cache, non l'intervallo tra i tentativi.

Questo esempio illustra come impostare la configurazione usando un'istanza di **ConfigurationOptions**.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using object-based configuration.
	                var options = new ConfigurationOptions
	                                    {
	                                        EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000  // The maximum waiting time (ms), not the delay for retries.
	                                    };
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

Questo esempio illustra come impostare la configurazione specificando le opzioni sotto forma di stringa.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using string-based configuration.
	                var options = "localhost,connectRetry=3,connectTimeout=2000";
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

Per altri esempi, vedere la sezione relativa alla [configurazione](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) sul sito Web di progetto.

## Altre informazioni

* [Sito Web di Redis](http://redis.io/)

## DocumentDB (versione provvisoria) - Linee guida per la ripetizione di tentativi

DocumentDB è un servizio di database di documenti completamente gestito e dotato di funzionalità di query e indicizzazione avanzate, basato su un modello di dati JSON senza schema. Offre prestazioni affidabili e configurabili, consente l'elaborazione transazionale JavaScript nativa e, grazie alla scalabilità elastica, è ottimizzato per il cloud.

## Meccanismo di ripetizione dei tentativi

La versione non definitiva del client DocumentDB include un meccanismo di ripetizione dei tentativi interno e non configurabile (potrà cambiare nelle versioni successive). Le impostazioni predefinite variano a seconda del contesto in cui viene usato. Alcune operazioni usano una strategia di backoff esponenziale con parametri a livello di codice, mentre altre specificano solo il numero di nuovi tentativi da eseguire e usano l'intervallo tra tentativi specificato nell'istanza [DocumentClientException](http://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx) restituita dal servizio. Se non viene specificato alcun intervallo di tempo, viene usato un intervallo di cinque secondi.

## Configurazione dei criteri (DocumentDB)

Nessuno. Tutte le classi usate per implementare la ripetizione dei tentativi sono interne. I parametri di ripetizione dei tentativi sono costanti oppure vengono impostati applicando dei parametri ai costruttori della classe.

La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.

| **Contesto** | **Impostazioni** | **Valori** | **Funzionamento** |
|------------------------|---------------------------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RetryPolicy (interno) | MaxRetryAttemptsOnQuery<br />
<br />
MaxRetryAttemptsOnRequest | 3<br />
<br />
0 | Il numero di tentativi per le query eseguite nel documento. Questo valore non può essere modificato.<br />
Il numero di tentativi per altre richieste. Questo valore non può essere modificato. |

## Linee guida sull'uso dei criteri di ripetizione dei tentativi

Quando si usa DocumentDB, tenere presente le linee guida seguenti:

* Non è possibile modificare i criteri di ripetizione dei tentativi predefiniti.
* Per altre informazioni sulle impostazioni predefinite, vedere [TBD].

## Telemetria

I tentativi vengono registrati come messaggi di traccia non strutturati tramite un oggetto **TraceSource** in .NET. È necessario configurare un oggetto **TraceListener** per acquisire gli eventi e scriverli in un log di destinazione appropriato.

## Ricerca - Linee guida per la ripetizione di tentativi

Lo strumento Ricerca di Azure può essere usato per aggiungere sofisticate e avanzate funzionalità di ricerca in un sito Web o un'applicazione, ottimizzare i risultati di ricerca in modo semplice e rapido e creare avanzati modelli di classificazione.

### Meccanismo di ripetizione dei tentativi

Per Ricerca non esiste un meccanismo di ripetizione dei tentativi incorporato poiché vengono generalmente usate richieste HTTP. Per attuare la ripetizione dei tentativi è possibile usare un'implementazione generica di un client REST e prendere decisioni in merito a se e quando ripetere l'operazione in base alla risposta ottenuta dal servizio. Per altre informazioni, vedere la sezione [Linee guida generali su REST e sulla ripetizione di tentativi](#general-rest-and-retry-guidelines) più avanti in questo articolo.

### Linee guida sull'uso dei criteri di ripetizione dei tentativi

Quando si usa Ricerca di Azure, tenere presente le linee guida seguenti:

* Usare il codice di stato restituito dal servizio per determinare il tipo di errore. I codici di stati sono definiti nei [codici di stato HTTP (Ricerca di Azure)](http://msdn.microsoft.com/library/dn798925.aspx). Il codice di stato "503 - Servizio non disponibile" indica che il servizio è sottoposto a un carico elevato e la richiesta non può essere elaborata immediatamente. È quindi opportuno ripetere l'operazione solo dopo aver concesso il tempo necessario per il ripristino del servizio. Ripetere un tentativo dopo un intervallo di tempo troppo breve rischia di prolungare lo stato di mancata disponibilità.
* Per altre informazioni sulla ripetizione di operazioni REST, vedere la sezione [Linee guida generali su REST e sulla ripetizione di tentativi](#general-rest-and-retry-guidelines) più avanti in questo articolo.

## Altre informazioni

* [API REST Ricerca di Azure](http://msdn.microsoft.com/library/dn798935.aspx)

## Azure Active Directory - Linee guida per la ripetizione di tentativi

Azure Active Directory (AD) è una soluzione cloud completa per la gestione delle identità e dell'accesso che combina servizi directory di importanza strategica, governance avanzata delle identità e gestione della sicurezza e dell'accesso alle applicazioni. Azure AD offre inoltre agli sviluppatori una piattaforma di gestione delle identità per consentire il controllo dell'accesso alle applicazioni in base a regole e criteri centralizzati.

### Meccanismo di ripetizione dei tentativi

Nella libreria di autenticazione di Active Directory (ADAL) non è previsto alcun meccanismo di ripetizione dei tentativi incorporato per Azure Active Directory. È possibile invece usare il Blocco di applicazioni per la gestione degli errori temporanei per implementare una strategia di ripetizione dei tentativi che contenga un meccanismo di rilevamento personalizzato per le eccezioni restituite da Active Directory.

### Configurazione dei criteri (Azure Active Directory)

Quando si usa il Blocco di applicazioni per la gestione degli errori temporanei con Azure Active Directory, è necessario creare un'istanza **RetryPolicy** basata su una classe che definisce la strategia di rilevamento da usare.

```csharp
var policy = new RetryPolicy<AdalDetectionStrategy>(new ExponentialBackoff(retryCount: 5,
	                                                                 minBackoff: TimeSpan.FromSeconds(0),
	                                                                 maxBackoff: TimeSpan.FromSeconds(60),
	                                                                 deltaBackoff: TimeSpan.FromSeconds(2)));
```

È quindi necessario chiamare il metodo **ExecuteAction** o **ExecuteAsync** dei criteri di ripetizione dei tentativi e passare l'operazione che si desidera eseguire.

```csharp
var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));
```

La classe di strategia di rilevamento riceve le eccezioni quando si verifica un errore e deve stabilire se si tratta di un errore temporaneo o un errore più permanente. Per stabilire questo aspetto, in genere, esamina il codice di stato e il tipo di eccezione. Una risposta "Servizio non disponibile", ad esempio, indica che deve essere eseguito un nuovo tentativo. Il Blocco di applicazioni per la gestione degli errori temporanei non offre una classe di strategia di rilevamento adeguata al client ADAL, ma nella sezione degli [Esempi](#examples-azure-active-directory-) riportata di seguito è disponibile una strategia di rilevamento personalizzata di esempio. È possibile usare indifferentemente una strategia di rilevamento personalizzata o una delle strategie fornite con il blocco.

Le strategie predefinite per il Blocco di applicazioni per la gestione degli errori temporanei sono illustrate nella sezione [Strategie per il Blocco di applicazioni per la gestione degli errori temporanei (Topaz)](#transient-fault-handling-application-block-topaz-strategies) nella parte finale dell'articolo.

## Linee guida sull'uso dei criteri di ripetizione dei tentativi

Quando si usa Azure Active Directory, tenere presente le linee guida seguenti:

* Se si usa l'API REST per Azure Active Directory, è necessario ripetere l'operazione solo se il risultato è un errore compreso nell'intervallo 5xx (ad esempio 500 - Errore interno del server, 502 - Gateway non valido, 503 - Servizio non disponibile o 504 - Timeout gateway). Non ripetere nuovi tentativi per altri tipi di errore.
* Se si usa la libreria di autenticazione di Active Directory, i codici HTTP non sono immediatamente accessibili. Occorre creare una strategia di rilevamento personalizzata che includa la logica necessaria per verificare le proprietà delle eccezioni specifiche della libreria ADAL. Vedere la sezione degli [esempi](#examples-azure-active-directory-) riportata di seguito.
* Negli scenari in batch di Azure Active Directory è opportuno usare criteri di backoff esponenziale.

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.


| **Contesto** | **Destinazione di esempio E2E<br />
latenza massima** | **Strategia di ripetizione dei tentativi** | **Impostazioni** | **Valori** | **Funzionamento** |
|----------------------|----------------------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interattivo, interfaccia utente<br />
o in primo piano | 2 secondi | FixedInterval | Numero tentativi<br />
Intervallo tra tentativi<br />
Primo tentativo rapido | 3<br />
500 ms<br />
true | Tentativo 1 - intervallo di 0 sec<br />
Tentativo 2 - intervallo di 500 ms<br />
Tentativo 3 - intervallo di 500 ms |
| Background<br />
 o batch | 60 secondi | ExponentialBackoff | Numero tentativi<br />
Backoff minimo<br />
Backoff massimo<br />
Backoff delta<br />
Primo tentativo veloce | 5<br />
0 sec<br />
60 sec<br />
2 sec<br />
false | Tentativo 1 - intervallo di 0 sec<br />
Tentativo 2 - intervallo di \~2 sec<br />
Tentativo 3 - intervallo di \~6 sec<br />
Tentativo 4 - intervallo di \~14 sec<br />
Tentativo 5 - intervallo di 30 sec |

## Esempi (Azure Active Directory)

L'esempio di codice seguente illustra come usare il Blocco di applicazioni per la gestione degli errori temporanei (Topaz) per definire una strategia personalizzata di rilevamento degli errori temporanei idonea per il client ADAL. Il codice crea una nuova istanza **RetryPolicy** basata su una strategia di rilevamento personalizzata di tipo **AdalDetectionStrategy**, come definito nel codice riportato di seguito. Le strategie di rilevamento personalizzate per Topaz implementano l'interfaccia **ITransientErrorDetectionStrategy** e restituiscono true se deve essere eseguito un nuovo tentativo, **false** se l'errore sembra non essere temporaneo e, quindi, non deve essere eseguito un nuovo tentativo.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Net;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

	namespace RetryCodeSamples
	{
	    class ActiveDirectoryCodeSamples
	    {
	        public async static Task Samples()
	        {
	            var authority = "[some authority]";
	            var resourceId = “[some resource id]”;
	            var clientId = “[some client id]”;

	            var authContext = new AuthenticationContext(authority);

	            var uc = new UserCredential(“[user]", "[password]");

	            // Use Topaz with a custom detection strategy to manage retries.
	            var policy =
	                new RetryPolicy<AdalDetectionStrategy>(
	                    new ExponentialBackoff(
	                        retryCount: 5,
	                        minBackoff: TimeSpan.FromSeconds(0),
	                        maxBackoff: TimeSpan.FromSeconds(60),
	                        deltaBackoff: TimeSpan.FromSeconds(2)));

	            var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));

	            // Get the access token
	            var accessToken = result.AccessToken;

	            // Use the result, probably to authorize an API call.
	        }
	    }

	    // TODO: This is sample code that needs validation from the WAAD team!
	    // based on existing detection strategies
	    public class AdalDetectionStrategy : ITransientErrorDetectionStrategy
	    {
	        private static readonly WebExceptionStatus[] webExceptionStatus =
	            new[]
	            {
	                WebExceptionStatus.ConnectionClosed,
	                WebExceptionStatus.Timeout,
	                WebExceptionStatus.RequestCanceled
	            };

	        private static readonly HttpStatusCode[] httpStatusCodes =
	            new[]
	            {
	                HttpStatusCode.InternalServerError,
	                HttpStatusCode.GatewayTimeout,
	                HttpStatusCode.ServiceUnavailable,
	                HttpStatusCode.RequestTimeout
	            };

	        public bool IsTransient(Exception ex)
	        {
	            var adalException = ex as AdalException;
	            if (adalException == null)
	            {
	                return false;
	            }

	            if (adalException.ErrorCode == AdalError.ServiceUnavailable)
	            {
	                return true;
	            }

	            var innerWebException = adalException.InnerException as WebException;
	            if (innerWebException != null)
	            {
	                if (webExceptionStatus.Contains(innerWebException.Status))
	                {
	                    return true;
	                }

	                if (innerWebException.Status == WebExceptionStatus.ProtocolError)
	                {
	                    var response = innerWebException.Response as HttpWebResponse;
	                    return response != null && httpStatusCodes.Contains(response.StatusCode);
	                }
	            }

	            return false;
	        }
	    }
	}

Per informazioni sulle operazioni di ripetizione dei tentativi nell'API Graph di Active Directory e sui codici di errore restituiti, vedere:

* [Esempio di codice Logica di tentativi](http://msdn.microsoft.com/library/azure/dn448547.aspx)
* [Codici di errore di Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974480.aspx)

## Altre informazioni

* [Implementazione di una strategia di rilevamento personalizzata](http://msdn.microsoft.com/library/hh680940.aspx) (Topaz)
* [Implementazione di una strategia di ripetizione dei tentativi personalizzata](http://msdn.microsoft.com/library/hh680943.aspx) (Topaz)
* [Linee guida per il rilascio di token e per la ripetizione dei tentativi](http://msdn.microsoft.com/library/azure/dn168916.aspx)

## Linee guida generali su REST e sulla ripetizione di tentativi

Quando si accede a servizi di Azure o di terze parti, tenere presente quanto segue:

* Usare un approccio sistematico per gestire la ripetizione di tentativi, ad esempio sotto forma di codice riusabile, in modo da poter applicare una metodologia coerente tra tutti i client e le soluzioni.
* Valutare la possibilità di usare un framework di ripetizione dei tentativi, come il Blocco di applicazioni per la gestione degli errori temporanei, per gestire la ripetizione di tentativi se il client o il servizio di destinazione non dispone di un meccanismo di ripetizione dei tentativi incorporato. In questo modo, sarà possibile implementare un comportamento coerente per la gestione dei nuovi tentativi e usufruire di un'adeguata strategia di ripetizione dei tentativi predefinita per il servizio di destinazione. Potrebbe essere necessario, tuttavia, creare un codice di ripetizione dei tentativi personalizzato per i servizi che presentano un comportamento non standard, ovvero non basato sulle eccezioni per stabilire gli errori temporanei, oppure se si desidera usare un metodo **Retry-Response** per gestire il comportamento di ripetizione dei tentativi.
* La logica di rilevamento degli errori temporanei dipenderà dall'API client effettiva usata per richiamare le chiamate REST. Alcuni client, ad esempio la nuova classe **HttpClient**, non genereranno eccezioni per le richieste completate con un codice di stato HTTP di errore. In questo modo, le prestazioni risulteranno migliorate ma non sarà possibile usare il Blocco di applicazioni per la gestione degli errori temporanei. Una soluzione potrebbe essere quella di eseguire il wrapping della chiamata all'API REST con un codice che genera eccezioni per i codici di stato HTTP di errore, che verrebbero quindi elaborati dal blocco. In alternativa, è possibile usare un meccanismo diverso per la gestione dei nuovi tentativi.
* Il codice di stato HTTP restituito dal servizio consente di stabilire se l'errore è temporaneo. È possibile che sia necessario esaminare le eccezioni generate da un client o dal framework di ripetizione dei tentativi per accedere al codice di stato o determinare il tipo di eccezione equivalente. I seguenti codici HTTP indicano, in genere, che è opportuno eseguire un nuovo tentativo:
  * 408 - Timeout richiesta
  * 500 - Errore interno del server
  * 502 - Gateway non valido
  * 503 - Servizio non disponibile
  * 504 - Timeout gateway
* Se si usa una logica di ripetizione dei tentativi basata sulle eccezioni, i valori seguenti indicano un errore temporaneo in cui non è possibile stabilire una connessione:
  * WebExceptionStatus.ConnectionClosed
  * WebExceptionStatus.ConnectFailure
  * WebExceptionStatus.Timeout
  * WebExceptionStatus.RequestCanceled
* In caso di stato non disponibile di un servizio, l'intervallo di tempo appropriato prima di un nuovo tentativo può essere riportato nell'intestazione della risposta **Retry-After** o in un'altra intestazione personalizzata (come nel servizio DocumentDB). Alcuni servizi possono anche inviare informazioni aggiuntive come intestazioni personalizzate o incorporate nel contenuto della risposta. Il Blocco di applicazioni per la gestione degli errori temporanei non può usare intestazioni "retry-after" standard o personalizzate.
* Non eseguire nuovi tentativi in caso di codici di stato che rappresentano errori client (errori nell'intervallo 4xx), ad eccezione di 408 - Timeout richiesta.
* Verificare accuratamente le strategie e i meccanismi di ripetizione dei tentativi in condizioni diverse, ad esempio in vari stati di rete e carichi di sistema.

## Strategie di ripetizione dei tentativi

Di seguito sono riportati i tipi intervallo più comuni nelle strategie di ripetizione dei tentativi:

* **Esponenziale**: criteri di ripetizione dei tentativi che eseguono un numero prestabilito di nuovi tentativi e usano un approccio di backoff esponenziale casuale per determinare l'intervallo di tempo tra i tentativi. Ad esempio:

		var random = new Random();

		var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
		            random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
		            (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
		var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
		               this.maxBackoff.TotalMilliseconds);
		retryInterval = TimeSpan.FromMilliseconds(interval);

* **Incrementale**: strategia di ripetizione dei tentativi con un numero prestabilito di nuovi tentativi e un intervallo di tempo incrementale tra i tentativi. Ad esempio:

		retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
		               (this.increment.TotalMilliseconds * currentRetryCount));

* **Lineare**: criteri di ripetizione dei tentativi che eseguono un numero prestabilito di nuovi tentativi e usano un intervallo di tempo fisso tra i tentativi. Ad esempio:

		retryInterval = this.deltaBackoff;

## Altre informazioni

* [Strategie di interruttore](http://msdn.microsoft.com/library/dn589784.aspx)

## Strategie del Blocco di applicazioni per la gestione degli errori temporanei (Topaz)

Il blocco di applicazioni per la gestione degli errori temporanei presenta la seguenti strategie predefinite.

| **Strategia** | **Impostazione** | **Valore predefinito** | **Significato** |
|-------------------------|-----------------------------------------------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Esponenziale** | retryCount<br />
minBackoff<br />
<br />
maxBackoff<br />
<br />
deltaBackoff<br />
<br />
fastFirstRetry | 10<br />
1 secondo<br />
<br />
30 secondi<br />
<br />
10 secondi<br />
<br />
true | Il numero di tentativi.<br />
Il tempo di backoff minimo. Come intervallo tra i tentativi verrà usato il valore più elevato o il backoff calcolato.<br />
Il tempo di backoff minimo. Come intervallo tra i tentativi verrà usato il valore più basso o il backoff calcolato.<br />
Il valore usato per calcolare un delta casuale per l'intervallo esponenziale tra i tentativi.<br />
Indica se il primo tentativo verrà eseguito immediatamente. |
| **Incrementale** | retryCount<br />
initialInterval<br />
increment<br />
<br />
fastFirstRetry<br />
| 10<br />
1 secondo<br />
1 secondo<br />
<br />
true | Il numero di tentativi.<br />
L'intervallo iniziale che verrà applicato per il primo tentativo.<br />
Il valore di tempo incrementale che verrà usato per calcolare l'intervallo progressivo tra i tentativi.<br />
Indica se il primo tentativo verrà eseguito immediatamente. |
| **Lineare (intervallo fisso)** | retryCount<br />
retryInterval<br />
fastFirstRetry<br />
 | 10<br />
1 secondo<br />
true | Il numero di tentativi.<br />
L'intervallo tra i tentativi.<br />
Indica se il primo tentativo verrà eseguito immediatamente. |
Per esempi di uso del Blocco di applicazioni per la gestione degli errori temporanei, vedere le precedenti sezioni degli esempi per il database SQL di Azure con ADO.NET e Azure Active Directory.

<!---HONumber=August15_HO6-->