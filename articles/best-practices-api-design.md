<properties
   pageTitle="Linee guida di progettazione dell’API | Microsoft Azure"
   description="Linee guida su come creare un’API ben progettata."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/01/2016"
   ms.author="masashin"/>

# Linee guida per la progettazione di un’API

![](media/best-practices-api-design/pnp-logo.png)

Alcuni argomenti di queste linee guida sono in fase di definizione e possono cambiare in futuro. I commenti degli utenti saranno molto apprezzati.


## Panoramica

Molte soluzioni moderne basate sul Web ricorrono ai servizi Web, ospitati su server Web, per fornire funzionalità per le applicazioni client remote. Le operazioni esposte da un servizio Web costituiscono un'API Web. Un'API Web progettata in modo adeguato deve supportare:

- **Indipendenza dalla piattaforma**. Le applicazioni client devono riuscire a usare l'API fornita dal servizio Web senza richiedere in che modo i dati o le operazioni esposte dall’API vengano implementati fisicamente. A questo scopo, l’API deve rispettare gli standard comuni che consentono a un’applicazione client e a un servizio Web di concordare i formati di dati da usare e la struttura dei dati scambiati tra le applicazioni client e il servizio Web.

- **Evoluzione del servizio**. Il servizio Web deve essere in grado di evolversi e aggiungere o rimuovere funzionalità in modo indipendente dalle applicazioni client. Le applicazioni client esistenti devono continuare a funzionare senza modifiche mentre le funzionalità fornite dal servizio Web cambiano. Tutte le funzionalità devono essere individuabili, per poter essere usate appieno dalle applicazioni client.

Lo scopo di queste linee guida è descrivere i problemi da prendere in considerazione quando si progetta un'API Web.

## Introduzione a REST (Representational State Transfer)

Nella sua dissertazione del 2000, Roy Fielding ha proposto un approccio architetturale alternativo per strutturare le operazioni esposte da servizi Web, denominato REST. REST è uno stile architetturale per la creazione di sistemi distribuiti basati su ipermedia. Uno dei principali vantaggi del modello REST sta nel fatto che è basato su standard aperti e che non vincola l'implementazione del modello o le applicazioni client che vi accedono a nessuna implementazione specifica. Ad esempio, un servizio Web REST può essere implementato usando l’API Web Microsoft ASP.NET e le applicazioni client possono essere sviluppate usando qualsiasi linguaggio e set di strumenti in grado di generare richieste HTTP e analizzare le risposte HTTP.

> [AZURE.NOTE]In realtà REST è indipendente da qualsiasi protocollo sottostante e non è necessariamente legato a HTTP. Tuttavia, le implementazioni più comuni dei sistemi basati su REST usano HTTP come protocollo di applicazione per l'invio e la ricezione di richieste. Questo documento è incentrato sul mapping dei principi REST ai sistemi progettati per funzionare con HTTP.

Il modello di REST utilizza uno schema di spostamento per rappresentare gli oggetti e i servizi, denominati _risorse_, in una rete. Molti sistemi che implementano REST in genere usano il protocollo HTTP per trasmettere le richieste di accesso a queste risorse. In questi sistemi, un'applicazione client invia una richiesta sotto forma di un URI che identifica una risorsa e un metodo HTTP (i più comuni sono GET, POST, PUT o DELETE) che indica l'operazione da eseguire su tale risorsa. Il corpo della richiesta HTTP contiene i dati necessari per eseguire l'operazione. Il punto importante da comprendere è che REST definisce un modello di richiesta senza stato. Le richieste HTTP devono essere indipendenti e possono verificarsi in qualsiasi ordine, per cui il tentativo di mantenere le informazioni di stato temporaneo tra due richieste non è realizzabile. L'unico punto in cui sono memorizzate le informazioni sono le risorse stesse e ogni richiesta deve essere un'operazione atomica. In effetti, un modello REST implementa una macchina a stati finiti in cui una richiesta passa una risorsa da uno stato non temporaneo ben definito a un altro.

> [AZURE.NOTE] La natura senza stato delle singole richieste nel modello REST consente a un sistema costituito dai seguenti principi di essere altamente scalabile. Non è necessario conservare l’affinità tra un'applicazione client che effettua una serie di richieste e i server Web specifici che gestiscono queste richieste.

Un altro punto fondamentale nell'implementazione di un modello REST efficace consiste nel comprendere le relazioni tra le varie risorse a cui il modello fornisce l'accesso. Queste risorse sono in genere organizzate in raccolte e relazioni. Ad esempio, si supponga che una rapida analisi di un sistema di e-commerce mostri che le raccolte a cui le applicazioni client possono essere interessate sono due: ordini e clienti. Ogni ordine e ogni cliente deve avere la propria chiave univoca per l'identificazione. L’URI per accedere alla raccolta di ordini può essere semplice come _/orders_, e allo stesso modo, l'URI per il recupero di tutti i clienti può essere _/customers._. L’invio di una richiesta HTTP GET all’URI _/orders_ deve restituire un elenco che rappresenta tutti gli ordini nella raccolta codificati come risposta HTTP:

```HTTP
GET http://adventure-works.com/orders HTTP/1.1
...
```

Nella risposta mostrata di seguito, gli ordini sono codificati come struttura di elenchi JSON:

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
[{"orderId":1,"orderValue":99.90,"productId":1,"quantity":1},{"orderId":2,"orderValue":10.00,"productId":4,"quantity":2},{"orderId":3,"orderValue":16.60,"productId":2,"quantity":4},{"orderId":4,"orderValue":25.90,"productId":3,"quantity":1},{"orderId":5,"orderValue":99.90,"productId":1,"quantity":1}]
```
Per recuperare un singolo ordine è necessario specificare l'identificatore per l'ordine dalla risorsa _orders_, ad esempio _/orders/2_:

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
```

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
{"orderId":2,"orderValue":10.00,"productId":4,"quantity":2}
```

> [AZURE.NOTE] Per semplicità, questi esempi mostrano le informazioni nelle risposte restituite come dati di testo JSON. Non esiste tuttavia alcuna ragione per cui le risorse non debbano contenere qualsiasi altro tipo di dati supportato da HTTP, ad esempio informazioni binarie o crittografate. Il tipo di contenuto nella risposta HTTP deve specificare il tipo. Un modello REST può anche essere in grado di restituire gli stessi dati in formati diversi, ad esempio XML o JSON. In questo caso, il servizio Web deve essere in grado di eseguire la negoziazione del contenuto con il client che effettua la richiesta. La richiesta può includere un’intestazione _Accept_ che specifica il formato che il client vuole ricevere. Il servizio Web deve tentare di rispettare questo formato, se possibile.

Si noti che la risposta da una richiesta REST standard utilizza i codici di stato HTTP standard. Ad esempio, una richiesta che restituisce dati validi deve includere il codice di risposta HTTP 200 (OK), mentre una richiesta che non riesce a trovare o eliminare una risorsa specificata deve restituire una risposta che include il codice di stato HTTP 404 (Non trovato).

## Progettazione e struttura di un'API Web RESTful

Le chiavi per la progettazione di un'API Web efficace sono semplicità e uniformità. Un'API Web caratterizzata da questi due fattori rende più semplice creare applicazioni client che devono usare l'API.

Un'API Web RESTful è incentrata sull’esposizione di un set di risorse connesse e sulla fornitura delle operazioni principali che consentono a un'applicazione di modificare queste risorse e di spostarsi facilmente tra loro. Per questo motivo, gli URI che costituiscono un'API Web RESTful standard devono essere orientati ai dati che questa espone e utilizzare le funzionalità fornite da HTTP per operare su questi dati. Questo approccio richiede una prospettiva diversa da quella usata in genere quando si progetta un set di classi in un'API orientata agli oggetti che tende a essere più motivata dal comportamento delle classi e degli oggetti. Inoltre, un'API Web RESTful deve essere senza stato e non deve dipendere dalle operazioni richiamate in una particolare sequenza. Le sezioni seguenti riepilogano i punti da considerare quando si progetta un'API Web RESTful.

### Organizzazione dell'API Web in base alle risorse

> [AZURE.TIP] Gli URI esposti da un servizio Web REST devono essere basati sui nomi (i dati a cui l'API Web fornisce l'accesso) e non sui verbi (le operazioni che un'applicazione può eseguire con i dati).

Concentrarsi sulle entità di business esposte dall'API Web. Ad esempio, in un’API Web progettata per supportare il sistema di e-commerce descritto in precedenza, le entità primarie sono customers e orders. Processi quali l'atto di emettere un ordine possono essere eseguiti fornendo un'operazione HTTP POST che aggiunge le informazioni relative all’ordine all'elenco di ordini per il cliente. Internamente, questa operazione POST può eseguire attività quali il controllo dei livelli di scorte e la fatturazione del cliente. La risposta HTTP può indicare se l'ordine è stato inserito correttamente o meno. Si noti inoltre che non è necessario che una risorsa sia basata su un singolo elemento di dati fisici. Ad esempio, una risorsa ordine può essere implementata internamente usano le informazioni aggregate da molte righe distribuite in varie tabelle in un database relazionale, ma presentate al client come una singola entità.

> [AZURE.TIP] Evitare di progettare un'interfaccia REST che rispecchi o dipenda dalla struttura interna dei dati esposti. REST consiste in qualcosa di più della semplice implementazione di operazioni CRUD (Create, Retrieve, Update, Delete) in tabelle separate in un database relazionale. Lo scopo di REST è eseguire il mapping delle entità business e delle operazioni che un'applicazione può eseguire su tali entità all'implementazione fisica di queste entità, ma un client non deve essere esposto a questi dettagli fisici.

L’esistenza di singole entità business in isolamento è rara (sebbene esistano alcuni oggetti singleton), piuttosto queste entità tendono a essere raggruppate in raccolte. Nella terminologia REST, ogni entità e ogni raccolta costituiscono una risorsa. In un’API Web RESTful, ogni raccolta ha un URI specifico all'interno del servizio Web, e l'esecuzione di una richiesta HTTP GET tramite un URI per una raccolta recupera un elenco di elementi in tale raccolta. Anche ogni singolo elemento ha un URI specifico, e un'applicazione può inviare un'altra richiesta HTTP GET con lo stesso URI per recuperare i dettagli di tale elemento. È necessario organizzare gli URI per le raccolte e gli elementi in modo gerarchico. Nel sistema di e-commerce l'URI _/customers_ indica la raccolta del cliente e _/customers/5_ recupera i dettagli per il singolo cliente con ID 5 da questa raccolta. Questo approccio consente di mantenere l'API Web intuitiva.

> [AZURE.TIP] Adottare una convenzione di denominazione coerente all’interno degli URI. In generale, per gli URI che fanno riferimento alle raccolte è utile usare nomi plurali.

È anche necessario prendere in considerazione le relazioni tra diversi tipi di risorse e il modo in cui esporre queste associazioni. Ad esempio, i clienti possono effettuare zero o più ordini. Un modo semplice per rappresentare questa relazione è un URI, ad esempio _/customers/5/orders_ per trovare tutti gli ordini per il cliente 5. È inoltre possibile prendere in considerazione l’idea di rappresentare l’associazione da un ordine a un cliente specifico tramite un URI come _/orders/99/customer_ per trovare il cliente dell’ordine 99. Tuttavia l’estensione eccessiva di questo modello può risultare complessa da implementare. Una soluzione migliore consiste nel fornire collegamenti esplorabili alle risorse associate, ad esempio il cliente, nel corpo del messaggio di risposta HTTP restituito quando viene eseguita una query sull'ordine. Questo meccanismo viene descritto in maggiore dettaglio nella sezione Uso dell’approccio HATEOAS per consentire lo spostamento a risorse correlate, più avanti in queste linee guida.

Nei sistemi più complessi possono essere presenti molti altri tipi di entità e si può essere tentati di fornire URI che consentono a un'applicazione client di spostarsi tra diversi livelli di relazioni, ad esempio _/customers/1/orders/99/products_ per ottenere l'elenco di prodotti nell’ordine 99 eseguito dal cliente 1. Tuttavia questo livello di complessità può essere difficile da mantenere e non è flessibile se le relazioni tra le risorse cambiano in futuro. Cercare invece di mantenere gli URI relativamente semplici. Tenere presente che, una volta che un'applicazione contiene un riferimento a una risorsa, è possibile usare questo riferimento per trovare gli elementi correlati a tale risorsa. La query precedente può essere sostituita con l'URI _/customers/1/orders_ per trovare tutti gli ordini per il cliente 1 e quindi eseguire una query all'URI _/orders/99/products_ per trovare i prodotti in questo ordine (presupponendo che l’ordine 99 sia stato eseguito dal cliente 1).

> [AZURE.TIP] Evitare di richiedere URI di risorse più complessi di _collection/item/collection_.

Un altro punto da considerare è che tutte le richieste Web impongono un carico sul server Web e che il carico aumenta in modo proporzionale alle richieste. È necessario provare a definire le risorse in modo da evitare API Web "frammentate" che espongono un numero elevato di risorse piccole. Questo tipo di API può richiedere che un'applicazione client invii più richieste per trovare tutti i dati che richiede. Può essere utile denormalizzare i dati e combinare le informazioni correlate tra loro in risorse più grandi che possono essere recuperate tramite l'esecuzione di una singola richiesta. Tuttavia, è necessario bilanciare questo approccio rispetto al sovraccarico del recupero di dati che potrebbero non essere richiesti spesso dal client. Il recupero di oggetti di grandi dimensioni può aumentare la latenza di una richiesta e comportare costi di larghezza di banda aggiuntivi a fronte di un piccolo vantaggio se i dati aggiuntivi non vengono utilizzati spesso.

Evitare di introdurre dipendenze tra l'API Web e la struttura, il tipo o il percorso delle origini dati sottostanti. Ad esempio, se i dati si trovano in un database relazionale, non è necessario che l'API Web esponga ogni tabella come raccolta di risorse. Considerare l'API Web come un'astrazione del database e, se necessario, introdurre un livello di mapping tra il database e l'API Web. In questo modo, se viene modificata la progettazione o l’implementazione del database (ad esempio, si passa da un database relazionale contenente una raccolta di tabelle normalizzate a un sistema di archiviazione NoSQL denormalizzato, come un database di documenti) le applicazioni client sono isolate da tali modifiche.
> [AZURE.TIP] Non è necessario che l'origine dei dati che supporta un'API Web sia un archivio dati. Potrebbe essere un altro servizio o un’applicazione line-of-business o persino un'applicazione legacy in esecuzione in locale all'interno di un’organizzazione.

Infine, potrebbe non essere possibile eseguire il mapping di ogni operazione implementata da un'API Web a una risorsa specifica. È possibile gestire tali scenari _non di risorsa_ tramite richieste HTTP GET che richiamano una parte di funzionalità e restituiscono i risultati come messaggio di risposta HTTP. Un’API Web che implementa semplici operazioni in stile calcolatrice, ad esempio addizioni e sottrazioni, potrebbe fornire URI che espongono queste operazioni come pseudo risorse e utilizzare la stringa di query per specificare i parametri richiesti. Ad esempio, una richiesta GET all’URI _/add?operand1=99&operand2=1_ può restituire un messaggio di risposta con un valore 100 all’interno del corpo e una richiesta GET all’URI _/subtract?operand1=50&operand2=20_ può restituire un messaggio di risposta con un valore 30 all’interno del corpo. Utilizzare tuttavia questi formati di URI solo in casi limitati.

### Definizione delle operazioni in termini di metodi HTTP

Il protocollo HTTP definisce una serie di metodi che assegnano un significato semantico a una richiesta. I metodi HTTP usati dalla maggior parte delle API Web RESTful sono:

- **GET**, per recuperare una copia della risorsa nell’URI specificato. Il corpo del messaggio di risposta contiene i dettagli della risorsa richiesta.

- **POST**, per creare una nuova risorsa nell’URI specificato. Il corpo del messaggio di richiesta fornisce i dettagli della nuova risorsa. Si noti che POST può anche essere usato per avviare operazioni che in realtà non creano risorse.

- **PUT**, per sostituire o aggiornare la risorsa nell’URI specificato. Il corpo del messaggio di richiesta specifica la risorsa da modificare e i valori da applicare.

- **DELETE**, per rimuovere la risorsa nell’URI specificato.

> [AZURE.NOTE] Il protocollo HTTP definisce anche altri metodi usati meno comunemente, ad esempio PATCH che viene usato per richiedere aggiornamenti selettivi a una risorsa, HEAD che viene utilizzato per richiedere una descrizione di una risorsa, OPTIONS che consente a un client di ottenere informazioni sulle opzioni di comunicazione supportate dal server e TRACE che consente a un client di richiedere informazioni utilizzabili a scopo di test e di diagnostica.

L'effetto di una richiesta specifica è diverso a seconda che la risorsa a cui viene applicata sia una raccolta o un singolo elemento. La tabella seguente riepiloga le convenzioni comuni adottate dalla maggior parte delle implementazioni RESTful usando l’esempio dell’e-commerce. Si noti che potrebbe non essere possibile implementare tutte queste richieste. L’implementazione dipende dallo scenario specifico.

| **Risorsa** | **POST** | **GET** | **PUT** | **DELETE** |
|--------------|----------|---------|---------|------------|
| /customers | Creare un nuovo cliente | Recuperare tutti i clienti | Eseguire l’aggiornamento di massa dei clienti (_se implementato_) | Rimuovere tutti i clienti |
| /customers/1 | Errore | Recuperare i dettagli per il cliente 1 | Aggiornare i dettagli del cliente 1 se esiste, in caso contrario restituire un errore | Rimuovere il cliente 1 |
| /customers/1/orders | Creare un nuovo ordine per il cliente 1 | Recuperare tutti gli ordini per il cliente 1 | Eseguire l’aggiornamento di massa degli ordini per il cliente 1 (_se implementato_) | Rimuovere tutti gli ordini per il cliente 1(_se implementato_) |

Sebbene lo scopo delle richieste GET e DELETE sia relativamente semplice, la finalità e gli effetti delle richieste POST e PUT possono generare confusione.

Una richiesta POST ha lo scopo di creare una nuova risorsa con dati forniti nel corpo della richiesta. Nel modello REST spesso le richieste POST si applicano alle risorse costituite da raccolte. La nuova risorsa viene aggiunta alla raccolta.

> [AZURE.NOTE] È anche possibile definire richieste POST che attivano alcune funzionalità e che non necessariamente restituiscono dati. Questi tipi di richieste possono essere applicati alle raccolte. È possibile, ad esempio, usare una richiesta POST per passare una scheda attività a un servizio di elaborazione delle retribuzioni e ottenere le tasse calcolate come risposta.

Lo scopo di una richiesta PUT è modificare una risorsa esistente. Se la risorsa specificata non esiste, la richiesta PUT può restituire un errore (in alcuni casi potrebbe effettivamente creare la risorsa). Le richieste PUT vengono applicate con maggiore frequenza a risorse costituite da singoli elementi, ad esempio un cliente o un ordine specifico, ma possono essere applicate anche alle raccolte, sebbene questo scenario sia meno frequente. Si noti che le richieste PUT sono idempotenti, mentre le richieste POST non lo sono. Se un'applicazione invia la stessa richiesta PUT più volte i risultati saranno gli stessi (la stessa risorsa verrà modificata con gli stessi valori), ma se un'applicazione ripete la stessa richiesta POST il risultato sarà la creazione di più risorse.

> [AZURE.NOTE] In modo più specifico, una richiesta HTTP PUT sostituisce una risorsa esistente con la risorsa specificata nel corpo della richiesta. Se si intende modificare una selezione di proprietà in una risorsa, lasciando però invariate le altre proprietà, questa operazione deve essere implementata mediante una richiesta HTTP PATCH. Tuttavia, molte implementazioni RESTful non applicano questa regola in modo rigido e usano PUT per entrambi i casi.

### Elaborazione di richieste HTTP
I dati inclusi da un'applicazione client in molte richieste HTTP e i corrispondenti messaggi di risposta dal server Web possono essere presentati in diversi formati o tipi di supporti. I dati che specificano i dettagli per un cliente o un ordine, ad esempio, possono essere forniti come XML, JSON o un altro formato codificato e compresso. Un'API Web RESTful deve supportare tipi di supporti diversi, come richiesto dall'applicazione client che invia una richiesta.

Quando un'applicazione client invia una richiesta che restituisce i dati nel corpo di un messaggio, può specificare i tipi di supporti che è in grado di gestire nell'intestazione Accept della richiesta. Il codice seguente illustra una richiesta HTTP GET che recupera i dettagli del cliente 1 e richiede che il risultato sia restituito nel formato JSON (il client deve comunque esaminare il tipo di supporto dei dati nella risposta per verificare il formato dei dati restituiti):

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
Accept: application/json
...
```

Se il server Web supporta questo tipo di supporto, può rispondere con una risposta che include l'intestazione Content-Type che specifica il formato dei dati nel corpo del messaggio:

> [AZURE.NOTE] Per garantire la massima interoperabilità, i tipi di supporti cui si fa riferimento nelle intestazioni Accept e Content-Type devono essere tipi MIME riconosciuti piuttosto che un tipo di supporto personalizzato.

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
```

Se il server Web non supporta il tipo di supporto richiesto, può inviare i dati in un formato diverso. In tutti i casi è necessario specificare il tipo di supporto (ad esempio _application/json_) nell'intestazione Content-Type. È responsabilità dell'applicazione client analizzare il messaggio di risposta e interpretare in modo appropriato i risultati nel corpo del messaggio.

Si noti che in questo esempio, il server Web recupera correttamente i dati richiesti e indica l'esito positivo mediante il passaggio di un codice di stato 200 nell'intestazione della risposta. Se non vengono trovati dati corrispondenti, restituisce invece un codice di stato 404 (non trovato) e il corpo del messaggio di risposta può contenere informazioni aggiuntive. Il formato di queste informazioni è specificato dall'intestazione Content-Type, come illustrato nell'esempio seguente:

```HTTP
GET http://adventure-works.com/orders/222 HTTP/1.1
...
Accept: application/json
...
```

L’ordine 222 non esiste, quindi il messaggio di risposta è simile al seguente:

```HTTP
HTTP/1.1 404 Not Found
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"message":"No such order"}
```

Quando un'applicazione invia una richiesta HTTP PUT per aggiornare una risorsa, specifica l'URI della risorsa e fornisce i dati da modificare nel corpo del messaggio di richiesta. Deve anche specificare il formato di questi dati tramite l'intestazione Content-Type. Un formato comune usato per le informazioni basate su testo è _application/x-www-form-urlencoded_, che comprende un set di coppie nome/valore separate dal carattere &. L'esempio seguente illustra una richiesta PUT HTTP che consente di modificare le informazioni nell'ordine 1:

```HTTP
PUT http://adventure-works.com/orders/1 HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
ProductID=3&Quantity=5&OrderValue=250
```

Se la modifica ha esito positivo, teoricamente la risposta dovrebbe essere un codice di stato HTTP 204, che indica che il processo è stato gestito correttamente, ma che il corpo della risposta non contiene altre informazioni. L'intestazione Location nella risposta contiene l'URI della risorsa appena aggiornata:

```HTTP
HTTP/1.1 204 No Content
...
Location: http://adventure-works.com/orders/1
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

> [AZURE.TIP] Se i dati in un messaggio di richiesta HTTP PUT includono informazioni su data e ora, verificare che il servizio Web accetti la data e l’ora formattate in base allo standard ISO 8601.

Se la risorsa da aggiornare non esiste, il server Web può rispondere con una risposta Non trovato, come descritto in precedenza. In alternativa, se il server crea effettivamente l'oggetto stesso può restituire i codici di stato HTTP 200 (OK) o HTTP 201 (Creato) e il corpo della risposta può contenere i dati per la nuova risorsa. Se nell’intestazione Content-Type della richiesta è specificato un formato di dati che il server Web non è in grado di gestire, risponderà con il codice di stato HTTP 415 (Tipo di supporto non utilizzabile).

> [AZURE.TIP] Prendere in considerazione l’implementazione di operazioni HTTP PUT in blocco in grado di eseguire aggiornamenti in batch di più risorse in una raccolta. La richiesta PUT deve specificare l'URI della raccolta e il corpo della richiesta deve specificare i dettagli delle risorse da modificare. Questo approccio consente di ridurre la frammentarietà e migliorare le prestazioni.

Il formato delle richieste HTTP POST che consentono di creare nuove risorse è simile a quello delle richieste PUT. Il corpo del messaggio contiene i dettagli della nuova risorsa da aggiungere. Tuttavia, in genere l’URI specifica la raccolta a cui deve essere aggiunta la risorsa. L'esempio seguente crea un nuovo ordine che viene aggiunto alla raccolta di ordini:

```HTTP
POST http://adventure-works.com/orders HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
productID=5&quantity=15&orderValue=400
```

Se la richiesta ha esito positivo, il server Web deve rispondere con un codice di messaggio con codice di stato HTTP 201 (Creato). L'intestazione Location deve contenere l'URI della risorsa appena creata e il corpo della risposta deve contenere una copia della nuova risorsa; l'intestazione Content-Type specifica il formato di questi dati:

```HTTP
HTTP/1.1 201 Created
...
Content-Type: application/json; charset=utf-8
Location: http://adventure-works.com/orders/99
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"orderID":99,"productID":5,"quantity":15,"orderValue":400}
```

> [AZURE.TIP] Se i dati forniti da una richiesta PUT o POST non sono validi, il server Web deve rispondere con un messaggio con codice di stato HTTP 400 (Richiesta errata). Il corpo di questo messaggio può contenere informazioni aggiuntive sul problema con la richiesta e i formati attesi oppure può contenere un collegamento a un URL in cui vengono forniti altri dettagli.

Per rimuovere una risorsa, una richiesta HTTP DELETE fornisce semplicemente l'URI della risorsa da eliminare. L'esempio seguente prova a rimuovere l’ordine 99:

```HTTP
DELETE http://adventure-works.com/orders/99 HTTP/1.1
...
```

Se l'operazione di eliminazione ha esito positivo, il server Web deve rispondere con codice di stato HTTP 204, che indica che il processo è stato gestito correttamente, ma che il corpo della risposta non contiene ulteriori informazioni. Si tratta della stessa risposta restituita da un'operazione PUT completata correttamente, ma senza un'intestazione Location, in quanto la risorsa non esiste più. È anche possibile, per una richiesta DELETE, restituire il codice di stato HTTP 200 (OK) o 202 (Accettato) se l'eliminazione viene eseguita in modo asincrono.

```HTTP
HTTP/1.1 204 No Content
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

Se la risorsa non viene trovata, il server Web deve restituire, invece, un messaggio 404 (Non trovato).

> [AZURE.TIP] Se tutte le risorse in una raccolta devono essere eliminate, consentire di specificare una richiesta HTTP DELETE per l'URI della raccolta piuttosto che forzare un'applicazione a rimuovere ciascuna risorsa, a turno, dalla raccolta.

### Filtro e paginazione dei dati

È necessario cercare di mantenere gli URI semplici e intuitivi. L’esposizione di una raccolta di risorse tramite un unico URI aiuta a questo proposito, ma può causare il recupero di grandi quantità di dati da parte delle applicazioni quando è necessario solo un sottoinsieme di informazioni. La generazione di un grande volume di traffico influisce non solo sulle prestazioni e sulla scalabilità del server Web, ma compromette anche la velocità di risposta delle applicazioni client che richiedono i dati.

Ad esempio, se gli ordini contengono il prezzo pagato per l'ordine, per un'applicazione client che deve recuperare tutti gli ordini con un costo al di sopra di un valore specifico potrebbe essere necessario recuperare tutti gli ordini dall’URI_/orders_ e quindi applicare il filtro a questi ordini localmente. Ovviamente questo processo è particolarmente inefficiente, perché comporta uno spreco di potenza di elaborazione e larghezza di banda di rete nel server che ospita l'API Web.

Una soluzione può consistere nel fornire uno schema URI, come _/orders/ordervalue\_greater\_than\_n_, in cui _n_ è il prezzo dell’ordine, sebbene per tutte le risorse, tranne un numero limitato, tale approccio non sia pratico. Inoltre, se è necessario eseguire query sugli ordini in base ad altri criteri, potrebbe essere necessario fornire un lungo elenco di URI con nomi probabilmente non intuitivi.

Una strategia migliore per il filtro dei dati consiste nel fornire criteri di filtro nella stringa di query che viene passata all’API Web, ad esempio _/orders?ordervaluethreshold=n_. In questo esempio, l'operazione corrispondente nell'API Web è responsabile della gestione e dell'analisi del `ordervaluethreshold` parametro nella stringa di query e della restituzione dei risultati filtrati nella risposta HTTP.

Alcune richieste HTTP GET semplici sulle risorse della raccolta potrebbero restituire un numero elevato di elementi. Per evitare la possibilità di tale inconveniente, è necessario progettare l'API Web in modo da limitare la quantità di dati restituiti da ogni singola richiesta. È possibile ottenere questo risultato mediante il supporto di stringhe di query che consentano all'utente di specificare il numero massimo di elementi da recuperare (che potrebbe essere soggetto a un limite estremo superiore per impedire attacchi di tipo Denial of Service) e un offset iniziale nella raccolta. Ad esempio, la stringa di query nell'URI _/orders?limit=25&offset=50_ deve recuperare 25 ordini a partire dal cinquantesimo ordine trovato nella raccolta degli ordini. Come accade con il filtro dei dati, l’operazione che implementa la richiesta GET nell’API Web è responsabile dell’analisi e della gestione dei parametri `limit` e `offset` nella stringa di query. Per agevolare le applicazioni client, le richieste GET che restituiscono i dati impaginati devono includere anche un tipo di metadati che indichino il numero totale delle risorse disponibili nella raccolta. È inoltre possibile prendere in considerazione altre strategie di paging intelligente. Per ulteriori informazioni, vedere l’articolo sulle [note sulla progettazione delle API e il paging intelligente](http://bizcoder.com/api-design-notes-smart-paging)

È possibile seguire una strategia simile per l'ordinamento dei dati nel momento in cui vengono recuperati. È possibile fornire un parametro Sort che accetta un nome di campo come valore, ad esempio _/orders?sort=ProductID_. Si noti, tuttavia, che questo approccio può avere un effetto deleterio sulla memorizzazione nella cache (i parametri della stringa di query formano parte dell'identificatore di risorsa utilizzato da molte implementazioni della cache come chiave per i dati memorizzati nella cache).

È possibile estendere questo approccio per limitare (proiettare) i campi restituiti se un singolo elemento risorsa contiene una grande quantità di dati. Ad esempio, è possibile utilizzare un parametro di stringa di query che accetta un elenco di campi delimitato da virgole, come _/orders?fields=ProductID,Quantity_.

> [AZURE.TIP] Assegnare valori predefiniti significativi a tutti i parametri facoltativi nelle stringhe di query. Ad esempio, impostare il parametro `limit` su 10 e il parametro `offset` su 0 se si implementa la paginazione, impostare il parametro Sort sulla chiave della risorsa se si implementa l’ordinamento e impostare il parametro `fields` su tutti i campi della risorsa se si supportano le proiezioni.

### Gestione di risorse binarie di grandi dimensioni

Una singola risorsa può contenere campi binari di grandi dimensioni, come file o immagini. Per superare i problemi causati da connessioni non affidabili e intermittenti e per migliorare i tempi di risposta, considerare la possibilità di fornire operazioni che consentano a tali risorse di essere recuperate in blocchi dall'applicazione client. A questo scopo, l'API Web deve supportare l'intestazione Accept-Ranges per le richieste GET per le risorse di grandi dimensioni e possibilmente implementare le richieste HTTP HEAD per tali risorse. L’intestazione Accept-Ranges indica che l’operazione GET supporta risultati parziali e che un’applicazione client può inviare richieste GET che restituiscono un sottoinsieme di una risorsa specificata come intervallo di byte. Una richiesta HEAD è simile a una richiesta GET tranne per il fatto che restituisce solo un’intestazione che descrive la risorsa e un corpo del messaggio vuoto. Un'applicazione client può inviare una richiesta HEAD per determinare se recuperare una risorsa tramite richieste GET parziali. Nell'esempio seguente viene illustrata una richiesta HEAD che ottiene informazioni su un'immagine del prodotto:

```HTTP
HEAD http://adventure-works.com/products/10?fields=productImage HTTP/1.1
...
```

Il messaggio di risposta contiene un’intestazione che include le dimensioni della risorsa (4580 byte) e l’intestazione Accept-Ranges, in cui l’operazione GET corrispondente supporta risultati parziali:

```HTTP
HTTP/1.1 200 OK
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 4580
...
```

L'applicazione client può usare queste informazioni per costruire una serie di operazioni GET per recuperare l'immagine in blocchi più piccoli. La prima richiesta recupera i primi 2500 byte mediante l'intestazione Range:

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=0-2499
...
```

Il codice di stato HTTP 206 restituito nel messaggio di risposta indica che si tratta di una risposta parziale. L'intestazione Content-Length specifica il numero effettivo di byte restituiti nel corpo del messaggio (non le dimensioni della risorsa) e l'intestazione Content-Range indica di quale parte della risorsa si tratta (byte 0-2499 di 4580):

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2500
Content-Range: bytes 0-2499/4580
...
_{binary data not shown}_
```

Una successiva richiesta dall'applicazione client può recuperare la parte restante della risorsa tramite un'intestazione Range appropriata:

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=2500-
...
```

Il messaggio del risultato corrispondente dovrebbe essere simile al seguente:

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2080
Content-Range: bytes 2500-4580/4580
...
```

## Utilizzo dell'approccio HATEOAS per consentire lo spostamento a risorse correlate

Una delle principali motivazioni alla base dell’approccio REST è che dovrebbe essere possibile spostarsi nell'intero set di risorse senza conoscere a priori lo schema URI. Ogni richiesta HTTP GET deve restituire le informazioni necessarie a trovare le risorse correlate direttamente all'oggetto richiesto tramite collegamenti ipertestuali inclusi nella risposta e deve anche contenere informazioni che descrivono le operazioni disponibili in ciascuna di queste risorse. Tale principio è noto come HATEOAS o Hypertext as the Engine of Application State. Il sistema è in realtà una macchina a stati finiti e la risposta a ogni richiesta contiene le informazioni necessarie per passare da uno stato all’altro. Non dovrebbero essere necessarie altre informazioni.

> [AZURE.NOTE] Attualmente non esistono standard o specifiche che definiscono come modellare il principio HATEOAS. Gli esempi mostrati in questa sezione illustrano una possibile soluzione.

Ad esempio, per gestire la relazione tra clienti e ordini, i dati restituiti nella risposta per un ordine specifico devono contenere gli URI sotto forma di un collegamento ipertestuale che identifica il cliente che ha effettuato l'ordine e le operazioni che possono essere eseguite su tale cliente.

```HTTP
GET http://adventure-works.com/orders/3 HTTP/1.1
Accept: application/json
...
```

Il corpo del messaggio di risposta contiene una matrice `links` (evidenziata nell’esempio di codice) in cui sono specificati la natura della relazione (_Customer_), l’URI del cliente (\__http://adventure-works.com/customers/3_), come recuperare i dettagli di questo cliente (_GET_) e i tipi MIME supportati dal server Web per il recupero di queste informazioni (_text/xml_ e _application/json_). Si tratta di tutte le informazioni di cui necessita un'applicazione client per essere in grado di recuperare i dettagli del cliente. La matrice Links include anche collegamenti per le altre operazioni che è possibile eseguire, come PUT (per modificare il cliente, insieme al formato che il server Web prevede che il client fornisca), e DELETE.

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"orderID":3,"productID":2,"quantity":4,"orderValue":16.60,"links":[(some links omitted){"rel":"customer","href":" http://adventure-works.com/customers/3", "action":"GET","types":["text/xml","application/json"]},{"rel":"
customer","href":" http://adventure-works.com /customers/3", "action":"PUT","types":["application/x-www-form-urlencoded"]},{"rel":"customer","href":" http://adventure-works.com /customers/3","action":"DELETE","types":[]}]}
```

Per motivi di completezza, la matrice Links deve includere anche informazioni autoreferenziali riguardanti la risorsa che è stata recuperata. Questi collegamenti sono stati omessi dall'esempio precedente, ma vengono evidenziati nel codice seguente. Si noti che in questi collegamenti, la relazione _self_ è stata usata per indicare che si tratta di un riferimento alla risorsa restituita dall'operazione:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"orderID":3,"productID":2,"quantity":4,"orderValue":16.60,"links":[{"rel":"self","href":" http://adventure-works.com/orders/3", "action":"GET","types":["text/xml","application/json"]},{"rel":" self","href":" http://adventure-works.com /orders/3", "action":"PUT","types":["application/x-www-form-urlencoded"]},{"rel":"self","href":" http://adventure-works.com /orders/3", "action":"DELETE","types":[]},{"rel":"customer",
"href":" http://adventure-works.com /customers/3", "action":"GET","types":["text/xml","application/json"]},{"rel":" customer" (customer links omitted)}]}
```

Affinché l'approccio sia efficace, le applicazioni client devono essere preparate a recuperare e analizzare le informazioni aggiuntive.

## Controllo delle versioni di un'API Web RESTful

È molto improbabile che un'API Web rimanga statica in tutte le situazioni, tranne le più semplici. Man mano che i requisiti aziendali cambiano, è possibile che vengano aggiunte nuove raccolte di risorse, che le relazioni tra le risorse cambino e che la struttura dei dati nelle risorse venga modificata. Sebbene l'aggiornamento di un'API Web per gestire requisiti nuovi o diversi sia un processo relativamente semplice, è necessario considerare gli effetti delle modifiche sulle applicazioni client che utilizzano l'API Web. Il problema è che nonostante lo sviluppatore che progetta e implementa un'API Web abbia il controllo completo su tale API, non ha lo stesso livello di controllo sulle applicazioni client che possono essere compilate da organizzazioni di terze parti che operano in modalità remota. L’imperativo è consentire alle applicazioni client esistenti di continuare a funzionare senza modifiche e allo stesso tempo permettere alle nuove applicazioni client di avvalersi di nuove funzionalità e risorse.

Il controllo delle versioni consente a un’API Web di indicare le funzionalità e le risorse esposte e a un’applicazione client di inviare richieste dirette a una versione specifica di una funzionalità o di una risorsa. Le sezioni seguenti descrivono diversi approcci differenti, ognuno dei quali presenta vantaggi e compromessi.

### Nessun controllo delle versioni

Si tratta dell'approccio più semplice e può essere accettabile per alcune API interne. Grandi cambiamenti potrebbero essere rappresentati come nuove risorse o nuovi collegamenti. L’aggiunta di contenuto alle risorse esistenti potrebbe non presentare una modifica sostanziale in quanto le applicazioni client che non prevedono di visualizzare che questo contenuto lo ignoreranno semplicemente.

Ad esempio, una richiesta all'URI \__http://adventure-works.com/customers/3_ deve restituire i dettagli di un singolo cliente contenente i campi `id`, `name`, e `address` previsti dall'applicazione client:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

> [AZURE.NOTE] Ai fini della semplicità e della chiarezza, le risposte di esempio illustrate in questa sezione non includono collegamenti HATEOAS.

Se il campo `DateCreated` viene aggiunto allo schema della risorsa customer, la risposta sarà simile alla seguente:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":"1 Microsoft Way Redmond WA 98053"}
```

Le applicazioni client esistenti potrebbero continuare a funzionare correttamente se riescono a ignorare i campi non riconosciuti, mentre le nuove applicazioni client possono essere progettate per gestire questo nuovo campo. Tuttavia, modifiche più radicali allo schema di risorse (ad esempio, la rimozione o la ridenominazione dei campi) o cambiamenti nelle relazioni tra le risorse possono costituire modifiche sostanziali che impediscono alle applicazioni client esistenti di funzionare correttamente. In tali situazioni è necessario considerare uno degli approcci seguenti.

### Controllo delle versioni tramite l’URI

Ogni volta che si modifica l'API Web o che si cambia lo schema di risorse, viene aggiunto un numero di versione all'URI per ciascuna risorsa. Gli URI già esistenti continueranno a funzionare come prima, restituendo risorse conformi al relativo schema originale.

Estendendo l'esempio precedente, se il campo `address` viene ristrutturato in campi secondari contenenti ogni parte dell'indirizzo (ad esempio `streetAddress`, `city`, `state` e `zipCode`), questa versione della risorsa può essere esposta tramite un URI che contiene un numero di versione, ad esempio http://adventure-works.com/v2/customers/3:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

Questo meccanismo di controllo delle versioni è molto semplice, ma dipende dal server che esegue il routing della richiesta all'endpoint appropriato. Tuttavia, può diventare scomodo man mano che l’API Web matura attraverso diverse iterazioni e il server deve supportare un numero elevato di versioni diverse. Inoltre, dal punto di vista di un purista, in tutti i casi le applicazioni client recuperano gli stessi dati (cliente 3), quindi l'URI non deve essere effettivamente diverso a seconda della versione. Questo schema complica anche l'implementazione di HATEOAS perché tutti i collegamenti dovranno includere il numero di versione nei relativi URI.

### Controllo delle versioni tramite la stringa di query

Anziché fornire più URI, è possibile specificare la versione della risorsa utilizzando un parametro all'interno della stringa di query aggiunta alla richiesta HTTP, ad esempio \__http://adventure-works.com/customers/3?version=2_. Il parametro della versione deve essere impostato su un valore predefinito significativo, ad esempio 1 se viene omesso dalle applicazioni client meno recenti.

Questo approccio ha il vantaggio semantico che la stessa risorsa viene sempre recuperata dallo stesso URI, ma dipende dal codice che gestisce la richiesta analizzare la stringa di query e inviare la risposta HTTP appropriata. Questo approccio presenta anche le stesse complicazioni per l'implementazione di HATEOAS del meccanismo di controllo delle versioni tramite URI.

> [AZURE.NOTE] Alcuni Web browser e proxy Web meno recenti non memorizzano nella cache le risposte per le richieste che includono una stringa di query nell'URL. Ciò può avere un impatto negativo sulle prestazioni per le applicazioni Web che utilizzano un'API Web e che vengono eseguite dall'interno di questo tipo di Web browser.

### Controllo delle versioni tramite l’intestazione

Anziché aggiungere il numero di versione come parametro di stringa di query, è possibile implementare un'intestazione personalizzata che indica la versione della risorsa. Questo approccio richiede l’aggiunta, da parte dell'applicazione client, dell'intestazione appropriata a tutte le richieste, nonostante il codice che gestisce la richiesta del client possa usare un valore predefinito (versione 1) se l'intestazione della versione viene omessa. Nell’esempio seguente viene utilizzata un’intestazione personalizzata denominata _Custom-Header_. Il valore di questa intestazione indica la versione dell'API Web.

Versione 1:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=1
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

Versione 2:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=2
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

Si noti che, come con i due approcci precedenti, l’implementazione di HATEOAS richiede l’inclusione dell'intestazione personalizzata appropriata in tutti i collegamenti.

### Controllo delle versioni tramite il tipo di supporto

Quando un'applicazione client invia una richiesta HTTP GET a un server Web, è necessario stabilire il formato del contenuto che è possibile gestire tramite un'intestazione Accept, come descritto in precedenza in queste linee guida. Spesso lo scopo dell’intestazione _Accept_ è quello di consentire all'applicazione client di specificare se il formato del corpo della risposta deve essere XML, JSON o un altro formato comune che il client può analizzare. Tuttavia, è possibile definire tipi di supporti personalizzati che includono informazioni che consentono all'applicazione client di indicare la versione di una risorsa prevista.163 Nell'esempio seguente viene illustrata una richiesta che specifica un’intestazione _Accept_ con il valore _application/vnd.adventure-works.v1+json_. L’elemento _vnd.adventure works.v1_ indica al server Web che deve restituire la versione 1 della risorsa, mentre l’elemento _json_ specifica che il formato del corpo della risposta deve essere JSON:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Accept: application/vnd.adventure-works.v1+json
...
```

Il codice che gestisce la richiesta è responsabile dell'elaborazione dell’intestazione _Accept_ rispettandola per quanto possibile (l'applicazione client può specificare più formati nell’intestazione _Accept_, e in questo caso il server Web può scegliere il formato più appropriato per il corpo della risposta). Il server Web conferma il formato dei dati nel corpo della risposta mediante l'intestazione Content-Type:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/vnd.adventure-works.v1+json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

Se l'intestazione Accept non specifica alcun tipo di supporto noto, il server Web potrebbe generare un messaggio di risposta HTTP 406 (Pagina non valida) o restituire un messaggio con un tipo di supporto predefinito.

Questo approccio è senza dubbio il meccanismo di controllo delle versioni più puro e si presta naturalmente a HATEOAS, che può includere il tipo MIME dei dati correlati nei collegamenti alle risorse.

> [AZURE.NOTE] Quando si seleziona una strategia di controllo delle versioni, è consigliabile tenere in considerazione anche le implicazioni sulle prestazioni, soprattutto la memorizzazione nella cache sul server Web. Il controllo delle versioni tramite URI e gli schemi di controllo delle versioni tramite la stringa di query sono adatti alla cache perché la stessa combinazione URI/stringa di query fa riferimento ogni volta agli stessi dati.

> I meccanismi di controllo delle versioni tramite intestazione e tipo di supporto in genere richiedono logica aggiuntiva per esaminare i valori nell’intestazione personalizzata o nell'intestazione Accept. In un ambiente su larga scala, la presenza di molti client che usano versioni diverse di un'API Web può comportare una notevole quantità di dati duplicati in una cache lato server. Questo problema può diventare evidente se un'applicazione client comunica con un server Web tramite un proxy che implementa la memorizzazione nella cache e che inoltra una richiesta al server Web solo se attualmente non mantiene una copia dei dati richiesti nella propria cache.

## Altre informazioni

- La [guida di riferimento dettagliata relativa a RESTful ](http://restcookbook.com/) contiene un’introduzione alla creazione delle API RESTful.
- L’[elenco di controllo delle API Web](https://mathieu.fenniak.net/the-api-checklist/) contiene un utile elenco di elementi da considerare durante la progettazione e l’implementazione di un'API Web.

<!---HONumber=AcomDC_0406_2016-->