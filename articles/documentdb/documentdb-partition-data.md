<properties 
	pageTitle="Partizionamento e scalabilità in Azure DocumentDB | Microsoft Azure"      
	description="Informazioni su come funziona il partizionamento in Azure DocumentDB, su come configurare il partizionamento e le chiavi di partizione e su come scegliere la chiave di partizione corretta per l'applicazione."         
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/10/2016" 
	ms.author="arramac"/>

# Partizionamento e scalabilità in Azure DocumentDB
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) è progettato per ottenere prestazioni rapide e prevedibili e per eseguire facilmente la scalabilità in base allo sviluppo dell'applicazione. Questo articolo offre una panoramica del funzionamento del partizionamento in DocumentDB e descrive come configurare raccolte di DocumentDB per scalare in modo efficace le applicazioni.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

- Come funziona il partizionamento in Azure DocumentDB?
- Come si configura il partizionamento in DocumentDB?
- Cosa sono le chiavi di partizione e come scegliere la chiave di partizione corretta per l'applicazione?

## Partizionamento in DocumentDB

In DocumentDB è possibile archiviare ed eseguire query di documenti JSON senza schema con tempi di risposta dell'ordine di millisecondi su qualsiasi scala. DocumentDB offre contenitori per l'archiviazione di dati denominati **raccolte**. Le raccolte sono risorse logiche e possono comprendere una o più partizioni fisiche o server. Il numero di partizioni è determinato da DocumentDB in base allo spazio di archiviazione e alla velocità effettiva con provisioning della raccolta. Ogni partizione in DocumentDB dispone di una quantità fissa di archiviazione supportata da unità SSD associata a essa e viene replicata per la disponibilità elevata. Le partizioni vengono completamente gestite da Azure DocumentDB e non è necessario scrivere script di codice complessi o gestire le partizioni. Le raccolte di DocumentDB sono **praticamente illimitate** in termini di spazio di archiviazione e di velocità effettiva.

Il partizionamento è completamente trasparente per l'applicazione. DocumentDB supporta letture e scritture veloci, query SQL e LINQ, una logica transazionale basata su JavaScript, livelli di coerenza e il controllo di accesso con granularità fine tramite chiamate API REST a una singola risorsa della raccolta. Il servizio gestisce la distribuzione dei dati tra le partizioni e il routing delle richieste di query alla partizione corretta.

Come funziona? Quando viene creata una raccolta in DocumentDB, viene visualizzato un valore di configurazione della **proprietà chiave di partizione** che è possibile specificare. Questa è la proprietà (o il percorso) JSON all'interno dei documenti che possono essere usati da DocumentDB per distribuire i dati tra più server o più partizioni. DocumentDB eseguirà l'hashing del valore della chiave di partizione e userà il risultato con hash per determinare la partizione in cui verrà archiviato il documento JSON. Tutti i documenti con la stessa chiave di partizione verranno archiviati nella stessa partizione.

Ad esempio, si consideri un'applicazione che archivia i dati sui dipendenti e i relativi reparti in DocumentDB. Scegliere `"department"` come proprietà chiave di partizione per scalare orizzontalmente i dati in base al reparto. Ogni documento in DocumentDB deve contenere una proprietà `"id"` obbligatoria che deve essere univoca per ogni documento con lo stesso valore della chiave di partizione, ad esempio `"Marketing`". Ogni documento archiviato in una raccolta deve avere una combinazione univoca di chiave di partizione e ID, ad esempio `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }` e `{ "Department": "Sales", "id": "0001" }`. In altre parole, la proprietà composta (chiave di partizione, id) è la chiave primaria per la raccolta.

### Chiavi di partizione
La scelta della chiave di partizione è una decisione importante da prendere in fase di progettazione. È necessario scegliere un nome della proprietà JSON che contiene un'ampia gamma di valori e probabilmente modelli di accesso distribuiti in modo uniforme. La chiave di partizione viene specificata come percorso JSON, ad esempio, `/department` rappresenta la proprietà reparto.

La tabella seguente mostra esempi di definizioni della chiave di partizione e i valori JSON corrispondenti a ciascuna definizione.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Percorso della chiave di partizione</strong></p></td>
            <td valign="top"><p><strong>Descrizione</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Corrisponde al valore JSON doc.department in cui doc è il documento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Corrisponde al valore JSON doc.properties.name in cui doc è il documento (proprietà annidata).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Corrisponde al valore JSON doc.id (ID e la chiave di partizione corrispondono alla stessa proprietà).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"nome reparto"</p></td>
            <td valign="top"><p>Corrisponde al valore JSON doc["nome reparto"] in cui doc è il documento.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] La sintassi del percorso della chiave di partizione è simile alla specifica dei percorsi dei criteri di indicizzazione con la differenza fondamentale che il percorso corrisponde alla proprietà anziché al valore, ovvero non ci sono caratteri jolly alla fine. Ad esempio, per indicizzare i valori nel reparto si specifica /department/?, mentre per la definizione della chiave di partizione si usa /department. Il percorso della chiave di partizione viene indicizzato in modo implicito e non può essere escluso dall'indicizzazione usando gli override dei criteri di indicizzazione.

Di seguito viene esaminato come la scelta della chiave di partizione influisce sulle prestazioni dell'applicazione.

### Partizionamento e velocità effettiva con provisioning
DocumentDB è progettato per prestazioni prevedibili. Quando si crea una raccolta, la velocità effettiva viene riservata in termini di **[unità richiesta](documentdb-request-units.md) al secondo**. A ogni richiesta viene assegnato un addebito delle unità richiesta proporzionato alla quantità di risorse di sistema, come CPU e I/O usati dall'operazione. La lettura di un documento di 1 KB con coerenza di sessione usa 1 unità richiesta. Un'operazione di lettura corrisponde a 1 RU indipendentemente dal numero di elementi archiviati o dal numero di richieste simultanee in esecuzione contemporaneamente. Documenti di dimensioni maggiori richiedono più unità richiesta a seconda delle dimensioni. Se si conoscono le dimensioni delle entità e il numero di letture che è necessario supportare per l'applicazione, è possibile eseguire il provisioning della quantità esatta di velocità effettiva necessaria per le esigenze di lettura dell'applicazione.

Quando DocumentDB archivia i documenti, li distribuisce in modo uniforme tra le partizioni in base al valore della chiave di partizione. Anche la velocità effettiva viene distribuita in modo uniforme tra le partizioni disponibili, ad esempio la velocità effettiva per ogni partizione = (velocità effettiva totale per ogni raccolta)/(numero di partizioni).

> [AZURE.TIP] Per ottenere la velocità effettiva totale della raccolta, è necessario scegliere una chiave di partizione che consenta di distribuire in modo uniforme le richieste tra una serie di valori distinti della chiave di partizione.

## Raccolte a partizione singola e raccolte partizionate
DocumentDB supporta la creazione di raccolte a partizione singola e raccolte partizionate.

- Le **raccolte partizionate** possono comprendere più partizioni e supportare uno spazio di archiviazione e una velocità effettiva molto elevati. È necessario specificare una chiave di partizione per la raccolta.
- Le **raccolte a partizione singola** hanno opzioni di prezzo inferiori e la capacità di eseguire query e transazioni su tutti i dati della raccolta. Hanno i limiti di scalabilità e archiviazione di una partizione singola. Non è necessario specificare una chiave di partizione per queste raccolte. 

![Raccolte partizionate in DocumentDB][2]

Per gli scenari che non richiedono volumi elevati di archiviazione o velocità effettiva, le raccolte a partizione singola rappresentano la soluzione ideale. Si noti che le raccolte a partizione singola hanno la scalabilità e i limiti di archiviazione di una partizione singola, ovvero fino a 10 GB di archiviazione e fino a 10.000 unità richiesta al secondo.

Le raccolte partizionate possono supportare uno spazio di archiviazione e una velocità effettiva molto elevati. Tuttavia, vengono configurate offerte predefinite per archiviare fino a 250 GB e scalare fino a 250.000 unità richiesta al secondo. Se è necessario uno spazio di archiviazione o una velocità effettiva maggiore per ogni raccolta, contattare il [supporto di Azure](documentdb-increase-limits.md) per richiedere tale ampliamento per l'account.

La tabella seguente elenca le differenze nell'uso di raccolte a partizione singola e raccolte partizionate:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Raccolta a partizione singola</strong></p></td>
            <td valign="top"><p><strong>Raccolta partizionata</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chiave di partizione</p></td>
            <td valign="top"><p>None</p></td>
            <td valign="top"><p>Obbligatorio</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chiave primaria per documento</p></td>
            <td valign="top"><p>"id"</p></td>
            <td valign="top"><p>chiave composta &lt;chiave di partizione> e "id"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Archiviazione minima</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Archiviazione massima</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>Senza limiti (250 GB per impostazione predefinita)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva minima</p></td>
            <td valign="top"><p>400 unità richiesta al secondo</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva massima</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>Senza limiti (250.000 unità richiesta al secondo per impostazione predefinita)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versioni dell'API</p></td>
            <td valign="top"><p>Tutti</p></td>
            <td valign="top"><p>API 2015-12-16 e versioni successive</p></td>
        </tr>
    </tbody>
</table>

## Uso degli SDK

Azure DocumentDB ha aggiunto il supporto per il partizionamento automatico con la [versione 2015-12-16 dell'API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx). Per creare raccolte partizionate, è necessario scaricare la versione 1.6.0 dell'SDK o la versione successiva da una delle piattaforme SDK supportate (.NET, Node.js, Java, Python).

### Creazione di raccolte partizionate

L'esempio seguente illustra un frammento di codice .NET per creare una raccolta per l'archiviazione di dati di telemetria del dispositivo a una velocità effettiva di 20.000 unità richiesta al secondo. L'SDK imposta il valore per OfferThroughput, che a sua volta imposta l'intestazione di richiesta `x-ms-offer-throughput` nell'API REST. Qui viene impostato `/deviceId` come chiave di partizione. La scelta della chiave di partizione viene salvata insieme al resto dei metadati della raccolta, come il nome e il criterio di indicizzazione.

Per questo esempio è stato scelto `deviceId` per due motivi: (a) considerato il numero elevato di dispositivi, le scritture possono essere distribuite in modo uniforme tra le partizioni consentendo di scalare il database per l'inserimento di volumi elevati di dati e (b) molte richieste, come ad esempio il recupero della lettura più recente per un dispositivo, sono limitate a un singolo deviceId e possono essere recuperate da una partizione singola.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] Per creare raccolte partizionate, è necessario specificare un valore per la velocità effettiva maggiore di 10.000 unità richiesta al secondo. Poiché la velocità effettiva viene specificata in multipli di 100, questo valore deve essere almeno 10.100.

Questo metodo effettua una chiamata API REST a DocumentDB e il servizio eseguirà il provisioning di una serie di partizioni in base alla velocità effettiva richiesta. È possibile modificare la velocità effettiva di una raccolta se le esigenze in termini di prestazioni cambiano. Vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md) per altri dettagli.

### Lettura e scrittura di documenti

A questo punto si inseriscono i dati in DocumentDB. Di seguito è riportata una classe di esempio contenente un dispositivo di lettura e una chiamata a CreateDocumentAsync per inserire un nuovo dispositivo di lettura in una raccolta.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Si procede alla lettura del documento, all'aggiornamento e infine all'eliminazione in base alla chiave di partizione e all'id. Si noti che le letture includono un valore PartitionKey, corrispondente all'intestazione di richiesta `x-ms-documentdb-partitionkey` nell'API REST.

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### Esecuzione di query sulle raccolte partizionate

Quando viene eseguita una query sui dati delle raccolte partizionate, DocumentDB instrada automaticamente la query alle partizioni corrispondenti ai valori della chiave di partizione specificati nel filtro (se presenti). Ad esempio, questa query viene instradata solo alla partizione contenente la chiave di partizione "XMS-0001".

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    	UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

La query seguente non dispone di un filtro per la chiave di partizione (DeviceId) e viene effettuato il fan-out a tutte le partizioni in cui viene eseguita a fronte dell'indice della partizione. Si noti che è necessario specificare EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` nell'API REST) affinché l'SDK esegua una query tra le partizioni.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### Esecuzione di stored procedure

È anche possibile eseguire transazioni atomiche rispetto a documenti con lo stesso ID dispositivo, ad esempio se si gestiscono aggregazioni o lo stato più recente di un dispositivo in un unico documento.

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

Nella sezione successiva verrà illustrato come passare alle raccolte partizionate da raccolte a partizione singola.

<a name="migrating-from-single-partition"></a>
### Migrazione da raccolte a partizione singola a raccolte partizionate
Quando un'applicazione che usa una raccolta a partizione singola necessita di una velocità effettiva più alta (> 10.000 unità richiesta/secondo) o di uno spazio di archiviazione dati maggiore (> 10 GB), è possibile usare lo [strumento di migrazione dati di DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) per eseguire la migrazione dei dati dalla raccolta a partizione singola a una raccolta partizionata.

Per eseguire la migrazione da una raccolta a partizione singola a una raccolta partizionata

1. Esportare i dati da una raccolta a partizione singola a JSON. Vedere [Esportare in file JSON](documentdb-import-data.md#export-to-json-file) per altre informazioni.
2. Importare i dati in una raccolta partizionata creata con una definizione della chiave di partizione e con più di 10.000 unità richiesta al secondo, come illustrato nell'esempio seguente. Per altre informazioni, vedere [Importare dati in DocumentDB con lo strumento di migrazione del database](documentdb-import-data.md#DocumentDBSeqTarget).

![Migrazione dei dati a una raccolta partizionata in DocumentDB][3]

>[AZURE.TIP] Per accelerare l'importazione, provare ad aumentare il numero di richieste parallele a 100 o a un valore superiore per sfruttare la maggiore velocità effettiva disponibile per le raccolte partizionate.

Dopo aver acquisito le nozioni di base, si esamineranno alcune importanti considerazioni di progettazione relative all'uso delle chiavi di partizione in DocumentDB.

## Progettazione per il partizionamento
La scelta della chiave di partizione è una decisione importante da prendere in fase di progettazione. Questa sezione descrive alcuni dei compromessi da applicare quando si seleziona una chiave di partizione per la raccolta.

### Chiave di partizione come limite delle transazioni
La scelta della chiave di partizione deve bilanciare la necessità di consentire l'uso di transazioni rispetto al requisito di distribuire le entità tra più chiavi di partizione per garantire una soluzione scalabile. Da una parte, è possibile impostare la stessa chiave di partizione per tutti i documenti. Tuttavia, questa scelta potrebbe limitare la scalabilità della soluzione. Dall'altra parte, è possibile assegnare a ogni documento una chiave di partizione univoca. In questo modo, la soluzione risulterebbe altamente scalabile, ma impedirebbe di usare transazioni tra documenti diversi mediante stored procedure e trigger. Una chiave di partizione ideale consente di usare query efficienti e dispone di una quantità sufficiente di cardinalità per garantire la scalabilità della soluzione.

### Come evitare colli di bottiglia per l'archiviazione e le prestazioni 
Un altro elemento importante è scegliere una proprietà che consenta di distribuire le scritture su una serie di valori distinti. Le richieste per la stessa chiave di partizione non possono superare la velocità effettiva di una partizione singola e saranno limitate. Di conseguenza, è importante scegliere una chiave di partizione che non generi **"aree sensibili"** all'interno dell'applicazione. Inoltre, lo spazio di archiviazione totale per i documenti con la stessa chiave di partizione non può superare 10 GB.

### Esempi di chiavi di partizione efficaci
Di seguito sono riportati alcuni esempi che illustrano come scegliere la chiave di partizione per l'applicazione:

* Se si implementa un back-end del profilo utente, l'ID utente rappresenta la scelta ideale per la chiave di partizione.
* Se si archiviano dati IoT, ad esempio lo stato del dispositivo, l'ID dispositivo rappresenta la scelta ideale per la chiave di partizione.
* Se si usa DocumentDB per la registrazione di dati di serie temporali, il nome host o l'ID processo rappresenta la scelta ottimale per la chiave di partizione.
* Se si dispone di un'architettura multi-tenant, l'ID tenant rappresenta la scelta ideale per la chiave di partizione.

Si noti che in alcuni casi d'uso (come l'IoT e i profili utente descritti in precedenza), la chiave di partizione potrebbe corrispondere all'id (chiave del documento). In altri casi, come ad esempio i dati di serie temporali, la chiave di partizione potrebbe essere diversa rispetto all'id.

### Partizionamento e registrazione di dati di serie temporali
Uno dei casi di utilizzo più comuni di DocumentDB è la registrazione e la telemetria. È importante scegliere una chiave di partizione efficace perché potrebbe essere necessario scrivere/leggere grandi volumi di dati. La scelta dipende dalla frequenza di lettura e scrittura e dai tipi di query che si prevede di eseguire. Di seguito sono riportati alcuni suggerimenti su come scegliere una chiave di partizione efficace.

- Se il caso di utilizzo prevede una frequenza ridotta di scritture eseguite in un lungo intervallo di tempo e la necessità di eseguire query in base agli intervalli di timestamp e ad altri filtri, si consiglia l'uso di un rollup del timestamp, ad esempio l'uso della data come chiave di partizione. Ciò consente di eseguire query su tutti i dati per una data da una singola partizione. 
- Se il carico di lavoro prevede molte scritture, che in generale sono più comuni, è opportuno usare una chiave di partizione non basata su timestamp in modo che DocumentDB possa distribuire in modo uniforme le scritture in più partizioni. In questo caso, un nome host, un ID processo, un ID attività o un'altra proprietà con una cardinalità elevata è una scelta efficace. 
- Il terzo è un approccio ibrido in cui si hanno più raccolte, una per ogni giorno/mese e la chiave di partizione è una proprietà granulare, ad esempio un nome host. Il vantaggio di questo approccio riguarda la possibilità di impostare diversi livelli di prestazioni in base alla finestra temporale, ad esempio il provisioning della raccolta per il mese corrente viene eseguita con una velocità effettiva maggiore perché viene usata per letture e scritture, mentre i per i mesi precedenti è possibile ridurre la velocità effettiva perché vengono usati solo per le letture.

### Partizionamento e multi-tenancy
Se si implementa un'applicazione multi-tenant usando DocumentDB, sono disponibili due modelli principali per implementare tenancy con DocumentDB: una chiave di partizione per ogni tenant e una raccolta per ogni tenant. Di seguito sono riportati vantaggi e svantaggi di ogni modello:

* Una chiave di partizione per ogni tenant: in questo modello, i tenant vengono collocati all'interno di una raccolta singola. Tuttavia, le query e gli inserimenti di documenti all'interno di un tenant possono essere eseguiti a fronte di una partizione singola. È anche possibile implementare la logica transazionale su tutti i documenti all'interno di un tenant. Poiché più tenant condividono una raccolta, è possibile risparmiare i costi di archiviazione e velocità effettiva raggruppando le risorse per i tenant all'interno di una raccolta singola anziché eseguendo il provisioning di maggiore capacità aggiuntiva per ogni tenant. Lo svantaggio è che non si ottiene l'isolamento delle prestazioni per ogni tenant. Gli aumenti di prestazioni/velocità effettiva si applicano all'intera raccolta rispetto agli aumenti previsti per i tenant.
* Una raccolta per ogni tenant: ogni tenant dispone di una raccolta propria. In questo modello è possibile riservare le prestazioni per ogni tenant. Grazie al modello di determinazione prezzi basato sull'uso di DocumentDB, questo modello è più conveniente per applicazioni multi-tenant con un numero ridotto di tenant.

È anche possibile usare un approccio combinato/a livelli che colloca tenant di dimensioni ridotte ed esegue la migrazione di tenant di dimensioni maggiori alla relativa raccolta.

## Passaggi successivi
Questo articolo descrive il funzionamento del partizionamento in Azure DocumentDB, come creare raccolte partizionate e come scegliere una chiave di partizione efficace per l'applicazione.

-   Iniziare a programmare con gli [SDK](documentdb-sdk-dotnet.md) o l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   Informazioni sulla [velocità effettiva con provisioning in DocumentDB](documentdb-performance-levels.md)
-   Se si desidera personalizzare il modo in cui l'applicazione esegue il partizionamento, è possibile collegare l'implementazione del partizionamento sul lato client. Vedere il [supporto per il partizionamento lato client](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png

 

<!---HONumber=AcomDC_0511_2016-->