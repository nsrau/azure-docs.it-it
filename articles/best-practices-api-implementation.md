---
title: Linee Guida all'implementazione API | Microsoft Docs
description: Indicazioni su come implementare un'API.
services: ''
documentationcenter: na
author: dragon119
manager: christb
editor: ''
tags: ''

ms.service: best-practice
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2016
ms.author: masashin

---
# Guida all'implementazione API
[!INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

Alcuni argomenti di queste linee guida sono in fase di definizione e possono cambiare in futuro. I commenti degli utenti saranno molto apprezzati.

## Overview
UN’API web RESTful progettata attentamente definisce le risorse, relazioni e gli schemi di navigazione che sono accessibili alle applicazioni del client. Quando implementi e distribuisci un’API web, è necessario considerare i requisiti fisici dell'ambiente di hosting web API e il modo in cui viene creata l'API web, anziché la struttura logica dei dati. Questa guida illustra le procedure consigliate per l'implementazione e la pubblicazione di un'API web al fine di renderla disponibile alle applicazioni client. I problemi di sicurezza sono descritti separatamente nel documento Linee guida alla Sicurezza API. Informazioni dettagliate sulla progettazione di un’API web sono reperibili nel documento Linee guida alla progettazione di un’API.

## Considerazioni per l'implementazione di un'API Web RESTful
Le sezioni seguenti illustrano la procedura consigliata per l'utilizzo del modello di API Web ASP.NET per creare un'API web RESTful. Per informazioni dettagliate sull'utilizzo del modello di API Web, visitare la pagina[Informazioni su API Web ASP.NET](http://www.asp.net/web-api) del sito Web Microsoft.

## Considerazioni per l'implementazione del routing delle richieste
In un servizio implementato utilizzando l'API Web ASP.NET, ogni richiesta viene indirizzata a un metodo di una classe *controller*. Il framework API Web fornisce due opzioni principali per l'implementazione del routing: routing *basato su convenzioni* e routing_basato su attributi_. Quando si determina il modo migliore per indirizzare le richieste dell’API Web, tenere presente quanto segue:

* **Comprendere le limitazioni e i requisiti del routing basato su convenzioni**.
  
    Per impostazione predefinita, il framework API Web utilizza il routing basato su convenzioni. Il framework API Web crea una tabella di routing iniziale che contiene la voce seguente:
  
    ```C#
    config.Routes.MapHttpRoute(
          name: "DefaultApi",
          routeTemplate: "api/{controller}/{id}",
          defaults: new { id = RouteParameter.Optional }
    );
    ```
  
    Le route possono essere generiche e comprendere valori letterali, ad esempio *api*, e variabili, ad esempio *{controller}* e *{id}*. Il routing basato su convenzioni prevede che alcuni elementi della route possano essere facoltativi. Il framework API Web determina quale metodo richiamare nel controller creando una corrispondenza tra il metodo HTTP nella richiesta per la parte iniziale del nome del metodo dell'API, quindi creando una corrispondenza tra i parametri facoltativi. Ad esempio, se un controller denominato *ordini* contiene i metodi *GetAllOrders()* o *GetOrderByInt (id int)* la richiesta GET \_http://www.adventure-works.com/api/orders/* verrà indirizzata al metodo _GetAlllOrders()* e la richiesta GET \_http://www.adventure-works.com/api/orders/99_ verrà indirizzata al metodo *GetOrderByInt(int id)*. Se non esiste alcun metodo corrispondente che inizia con il prefisso Get nel controller, il framework API Web risponde con un messaggio HTTP 405 (metodo non consentito). Inoltre, il nome del parametro (id) specificato nella tabella di routing deve essere lo stesso nome del parametro per il metodo *GetOrderById*, in caso contrario il framework API Web risponderà con un messaggio HTTP 404 (non trovato).
  
    Le stesse regole si applicano alle richieste POST, PUT e DELETE HTTP: una richiesta PUT che aggiorna i dettagli dell'ordine 101 verrebbe indirizzata all'URI \_http://www.adventure-works.com/api/orders/101_, il corpo del messaggio conterrà i nuovi dettagli dell'ordine e queste informazioni verranno passate come parametro a un metodo nel controller degli ordini con un nome che inizia con il prefisso *Put*, ad esempio *PutOrder*.
  
    La tabella di routing predefinito non corrisponderà a una richiesta che fa riferimento a risorse figlio di un’API Web RESTful API, ad esempio \_http://www.adventure-works.com/api/customers/1/orders_ (trovare i dettagli di tutti gli ordini effettuati dal cliente 1). Per gestire questi casi, è possibile aggiungere route personalizzate alla tabella di routing:
  
    ```C#
    config.Routes.MapHttpRoute(
        name: "CustomerOrdersRoute",
        routeTemplate: "api/customers/{custId}/orders",
        defaults: new { controller="Customers", action="GetOrdersForCustomer" })
    );
    ```
  
    Questa route indirizza le richieste che corrispondono all'URI al metodo *GetOrdersForCustomer* nel controller_clienti_. Questo metodo deve accettare un singolo parametro denominato *custI*:
  
    ```C#
    public class CustomersController : ApiController
    {
        ...
        public IEnumerable<Order> GetOrdersForCustomer(int custId)
        {
            // Find orders for the specified customer
            var orders = ...
            return orders;
        }
        ...
    }
    ```
  
  > [!TIP]
  > Utilizzare il routing predefinito quando possibile ed evitare di definire molte route personalizzate complicate, perché possono derivarne problemi di vulnerabilità (è molto semplice aggiungere metodi a un controller che causano route ambigue) e riduzione delle prestazioni (più grande è la tabella di routing, più lavoro il framework API Web deve svolgere per scoprire quale route corrisponde a un determinato URI). Mantenere semplici l'API e le route. Per ulteriori informazioni, vedere la sezione Organizzare un’API Web in base allea risorse nella Guida di progettazione API. Se è necessario definire route personalizzate, è preferibile utilizzare il routing basato su attributi descritto più avanti in questa sezione.
  > 
  > 
  
    Per ulteriori informazioni sul routing basato su convenzioni, vedere la pagina [Routing in API Web ASP.NET](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) del sito Web Microsoft.
* **Evitare ambiguità nel routing**.
  
    Il routing basato su convenzioni può causare percorsi ambigui se più metodi in un controller corrispondono alla stessa route. In questi casi, il framework API Web risponde con un messaggio HTTP 500 (errore interno del server) contenente il testo "sono state trovate più azioni che corrispondono alla richiesta".
* **Preferenza per il routing basato su attributi**.
  
    Il routing basato su attributi fornisce un'alternativa per la connessione delle route ai metodi del controller. È possibile annotare in modo esplicito i metodi del controller con i dettagli della route alla quale dovrebbero essere associati, anziché basarsi sui criteri di corrispondenza del routing basato su convenzioni. Questo approccio consente di rimuovere possibili ambiguità. Inoltre, poiché le route esplicite sono definite in fase di progettazione, questo approccio è più efficiente del routing basato su convenzioni in fase di esecuzione. Il codice seguente mostra come applicare l’attributo *Route* ai metodi del controller clienti. Questi metodi utilizzano inoltre l'attributo HttpGet per indicare che devono rispondere alle richieste *HTTP GET*. Questo attributo consente di denominare i metodi utilizzando qualsiasi schema di denominazione pratico e non quello previsto dal routing basato su convenzioni. È inoltre possibile annotare i metodi con gli attributi *HttpPost*, *HttpPut*, e *HttpDelete* per definire metodi che rispondono ad altri tipi di richieste HTTP.
  
    ```C#
    public class CustomersController : ApiController
    {
        ...
        [Route("api/customers/{id}")]
        [HttpGet]
        public Customer FindCustomerByID(int id)
        {
            // Find the matching customer
            var customer = ...
            return customer;
        }
        ...
        [Route("api/customers/{id}/orders")]
        [HttpGet]
        public IEnumerable<Order> FindOrdersForCustomer(int id)
        {
            // Find orders for the specified customer
            var orders = ...
            return orders;
        }
        ...
    }
    ```
  
    Il routing basato su attributi ha inoltre l'utile effetto collaterale di fungere da documentazione per gli sviluppatori che hanno la necessità di gestire il codice in futuro. In questo modo è immediatamente chiaro quale metodo appartiene a quale route e l’attributo_HttpGet_ chiarisce il tipo di richiesta HTTP alla quale il metodo risponde.
  
    Il routing basato su attributi consente di definire i vincoli che limitano le modalità di corrispondenza dei parametri. I vincoli possono specificare il tipo del parametro e in alcuni casi possono inoltre indicare l'intervallo di valori di parametro accettabile. Nell'esempio seguente, il parametro id per il metodo *FindCustomerByID* deve essere un numero intero non negativo. Se un'applicazione invia una richiesta HTTP GET con un numero cliente negativo, il framework API Web risponderà con un messaggio HTTP 405 (metodo non consentito):
  
    ```C#
    public class CustomersController : ApiController
    {
        ...
        [Route("api/customers/{id:int:min(0)}")]
        [HttpGet]
        public Customer FindCustomerByID(int id)
        {
            // Find the matching customer
            var customer = ...
            return customer;
        }
        ...
    }
    ```
  
    Per ulteriori informazioni sul routing basato su convenzioni, vedere la pagina [Routing mediante attributi n API Web 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) del sito Web Microsoft.
* **Supporto dei caratteri Unicode nelle route**.
  
    Le chiavi utilizzate per identificare le risorse nelle richieste GET potrebbero essere stringhe. Pertanto, in un'applicazione globale, potrebbe essere necessario supportare gli URI che contengono caratteri non inglesi.
* **Distinguere i metodi che non devono essere indirizzati**.
  
    Se si utilizza il routing basato su convenzioni, è opportuno indicare i metodi che non corrispondono alle azioni HTTP assegnando l’attributo *NonAction*. In genere ciò si applica ai metodi helper definiti per l'utilizzo da parte di altri metodi all'interno del controller. Questo attributo eviterà che questi metodi siano associati e richiamati da una richiesta HTTP non corretta.
* **Considerare vantaggi e compromessi della collocazione dell'API in un sottodominio**.
  
    Per impostazione predefinita, l'API Web ASP.NET consente di organizzare le API nella directory */api* in un dominio, ad esempio \_http://www.adventure-works.com/api/orders_ Questa directory si trova nello stesso dominio come tutti gli altri servizi esposti dallo stesso host. Può essere utile suddividere l'API Web in un proprio sottodominio in esecuzione su un host separato, con URI come \_http://api.adventure-works.com/orders_. Questa separazione consente di partizionare e scalare in modo più efficace l'API Web senza influire sulle altre applicazioni web o servizi in esecuzione nel dominio *www.adventure-works.com*.
  
    La collocazione di un'API Web in un sottodominio diverso può causare problemi di sicurezza. Tutte le applicazioni web o i servizi ospitati in *www.adventure-works.com* che richiamano un'API web in esecuzione altrove, potrebbero violare i criteri della stessa origine di molti browser web. In questo caso, sarà necessario abilitare la condivisione di risorse tra origini (CORS) tra gli host. Per ulteriori informazioni, vedere il documento Linee guida alla sicurezza API.

## Considerazioni sull'elaborazione delle richieste
Una volta che una richiesta è stata inviata correttamente da un’applicazione client a un metodo in un'API Web, tale richiesta deve essere elaborata nel modo più efficiente possibile. Quando si implementa il codice per gestire le richieste, tenere presente quanto segue:

* **Le azioni GET, PUT, DELETE, HEAD e PATCH devono essere idempotenti**.
  
    Il codice che implementa queste richieste non deve causare effetti collaterali. La stessa richiesta ripetuta sulla stessa risorsa deve produrre lo stesso stato. Ad esempio, inviare più richieste DELETE allo stesso URI deve avere lo stesso effetto, anche se il codice di stato HTTP nei messaggi di risposta può essere diverso. La prima richiesta DELETE potrebbe restituire il codice di stato 204, nessun contenuto, mentre una successiva richiesta DELETE potrebbe restituire il codice di stato 404, non trovato.

> [!NOTE]
> L'articolo [Modelli di idempotenza](http://blog.jonathanoliver.com/idempotency-patterns/) sul blog di Oliver Jonathan fornisce una panoramica sull’idempotenza e sul modo in cui è correlata alle operazioni di gestione dati.
> 
> 

* **Le azioni POST che creano nuove risorse non devono avere effetti collaterali non correlati**.
  
    Se si tratta di una richiesta POST per creare una nuova risorsa, gli effetti di tale richiesta devono essere limitati alla nuova risorsa (e possibilmente a tutte le risorse direttamente correlate se presente un collegamento). Ad esempio, in un sistema di e-commerce, una richiesta POST che crea un nuovo ordine per un cliente potrebbe anche modificare i livelli di inventario e generare informazioni di fatturazione, ma non deve modificare le informazioni non direttamente correlate all'ordine o avere eventuali altri effetti collaterali sullo stato complessivo del sistema.
* **Evitare l'implementazione delle operazioni POST, PUT e DELETE**.
  
    Supporto delle richieste POST, PUT e DELETE su raccolte di risorse. Una richiesta POST può contenere i dettagli per più risorse nuove e aggiungerle tutte alla stessa raccolta; una richiesta PUT può sostituire l'intero set di risorse in una raccolta e una richiesta DELETE può rimuovere un'intera raccolta.
  
    Si noti che il supporto di OData incluso in API Web ASP.NET 2 offre la possibilità di richieste batch. Un'applicazione client può impacchettare diverse richieste di API Web, inviarle al server in una singola richiesta HTTP e ricevere una singola risposta HTTP che contiene le risposte a ogni richiesta. Per ulteriori informazioni, vedere la pagina [Introduzione al supporto Batch in API Web e API Web OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) del sito Web Microsoft.
* **Rispettare il protocollo HTTP quando si invia una risposta a un'applicazione client**.
  
    Un'API Web deve restituire i messaggi che contengono il codice di stato HTTP corretto per consentire al client di stabilire come gestire il risultato, le intestazioni HTTP appropriate in modo che il client sia in grado di comprendere la natura del risultato e un corpo formattato adeguatamente per consentire al client di analizzare il risultato. Se si utilizza il modello di API Web ASP.NET, la strategia predefinita per l'implementazione di metodi che rispondono alle richieste HTTP POST è semplicemente la restituzione di una copia della risorsa appena creata, come illustrato nell'esempio seguente:
  
    ```C#
    public class CustomersController : ApiController
    {
        ...
        [Route("api/customers")]
        [HttpPost]
        public Customer CreateNewCustomer(Customer customerDetails)
        {
            // Add the new customer to the repository
            // This method returns a customer with a unique ID allocated
            // by the repository
            var newCust = repository.Add(customerDetails);
            // Return the newly added customer
            return newCust;
        }
        ...
    }
    ```
  
    Se l'operazione POST ha esito positivo, il framework API Web crea una risposta HTTP contenente il codice di stato 200 (OK) e i dettagli del cliente come corpo del messaggio. Tuttavia, in questo caso, secondo il protocollo HTTP, un'operazione POST deve restituire il codice di stato 201 (creato) e il messaggio di risposta deve includere l'URI della risorsa appena creata nell'intestazione Location del messaggio di risposta.
  
    Per fornire queste funzionalità, è necessario restituire il messaggio di risposta HTTP utilizzando l’ `IHttpActionResult` interfaccia. Questo approccio offre un controllo accurato sul codice di stato HTTP, sulle intestazioni nel messaggio di risposta e perfino sul formato dei dati nel corpo del messaggio di risposta, come illustrato nell'esempio di codice riportato di seguito. Questa versione del `CreateNewCustomer` metodo è conforme alle aspettative del client che segue il protocollo HTTP. Il `Created` metodo della `ApiController` classe costruisce il messaggio di risposta dai dati specificati e aggiunge l'intestazione Location ai risultati:
  
    ```C#
    public class CustomersController : ApiController
    {
        ...
        [Route("api/customers")]
        [HttpPost]
        public IHttpActionResult CreateNewCustomer(Customer customerDetails)
        {
            // Add the new customer to the repository
            var newCust = repository.Add(customerDetails);
  
            // Create a value for the Location header to be returned in the response
            // The URI should be the location of the customer including its ID,
            // such as http://adventure-works.com/api/customers/99
            var location = new Uri(...);
  
            // Return the HTTP 201 response,
            // including the location and the newly added customer
            return Created(location, newCust);
        }
        ...
    }
    ```
* **Negoziazione dei contenuti di supporto**.
  
    Il corpo di un messaggio di risposta può contenere dati in diversi formati. Ad esempio, una richiesta HTTP GET potrebbe restituire dati in formato JSON o XML. Quando il client invia una richiesta, questa può includere un'intestazione Accept che specifica i formati di dati che il client è in grado di gestire. Questi formati vengono specificati come tipi di supporto. Un client che invia una richiesta GET che recupera un'immagine può specificare un'intestazione Accept che elenca i tipi di supporto che il client è in grado di gestire, ad esempio "image/jpeg, image/gif, image/png". Quando l'API Web restituisce il risultato, è necessario formattare i dati utilizzando uno di questi tipi di supporto e specificare il formato nell'intestazione Content-Type della risposta.
  
    Se il client non specifica un'intestazione Accept, è necessario utilizzare un formato predefinito adeguato per il corpo della risposta. Ad esempio, il framework API Web ASP.NET impostato su JSON per i dati basati su testo.
  
  > [!NOTE]
  > Il framework API Web ASP.NET esegue alcuni rilevamenti automatici delle intestazioni Accept e le gestisce automaticamente in base al tipo di dati contenuti nel corpo del messaggio di risposta. Ad esempio, se il corpo di un messaggio di risposta contiene un oggetto CLR (common language runtime), l'API Web ASP.NET formatta automaticamente la risposta nel formato JSON con l'intestazione Content-Type della risposta impostata su "application/json", a meno che il client richieda i risultati in formato XML, nel qual caso il framework API Web ASP.NET formatta la risposta come XML e imposta l'intestazione Content-Type della risposta su "text/xml". Tuttavia, potrebbe essere necessario gestire intestazioni Accept che specificano diversi tipi di supporto in modo esplicito nel codice di implementazione per un'operazione.
  > 
  > 
* **Fornire collegamenti per supportare la navigazione di tipo HATEOAS e l'individuazione di risorse**.
  
    Le Linee guida alla progettazione di un’API descrivono come seguire l‘approccio HATEOAS consente a un client di esplorare e individuare le risorse da un punto di partenza iniziale. Questo avviene utilizzando i collegamenti che contengono URI. Quando un client invia una richiesta HTTP GET per ottenere una risorsa, la risposta deve contenere gli URI che consentono a un'applicazione client di individuare rapidamente tutte le risorse direttamente correlate. In un'API Web che supporta una soluzione e-commerce, ad esempio, un cliente può avere molti ordini posizionati. Quando un'applicazione client recupera i dettagli per un cliente, la risposta deve includere i collegamenti che consentono all'applicazione client di inviare richieste HTTP GET che possono recuperare questi ordini. Inoltre, i collegamenti di tipo HATEOAS devono descrivere le altre operazioni (POST, PUT, DELETE e così via) che ciascuna risorsa collegata supporta, insieme all'URI corrispondente, per eseguire ogni richiesta. Questo approccio è descritto più dettagliatamente nel documento Linee Guida alla progettazione di un'API.
  
    Attualmente non esistono standard che regolano l'implementazione di HATEOAS, ma nell'esempio seguente viene illustrato uno degli approcci possibili. In questo esempio, una richiesta HTTP GET che consente di trovare i dettagli di un cliente, restituisce una risposta che include i collegamenti HATEOAS che fanno riferimento agli ordini di quel cliente:
  
    ```HTTP
    GET http://adventure-works.com/customers/2 HTTP/1.1
    Accept: text/json
    ...
    ```
  
    ```HTTP
    HTTP/1.1 200 OK
    ...
    Content-Type: application/json; charset=utf-8
    ...
    Content-Length: ...
    {"CustomerID":2,"CustomerName":"Bert","Links":[
      {"rel":"self",
       "href":"http://adventure-works.com/customers/2",
       "action":"GET",
       "types":["text/xml","application/json"]},
      {"rel":"self",
       "href":"http://adventure-works.com/customers/2",
       "action":"PUT",
       "types":["application/x-www-form-urlencoded"]},
      {"rel":"self",
       "href":"http://adventure-works.com/customers/2",
       "action":"DELETE",
       "types":[]},
      {"rel":"orders",
       "href":"http://adventure-works.com/customers/2/orders",
       "action":"GET",
       "types":["text/xml","application/json"]},
      {"rel":"orders",
       "href":"http://adventure-works.com/customers/2/orders",
       "action":"POST",
       "types":["application/x-www-form-urlencoded"]}
    ]}
    ```
  
    In questo esempio, i dati del cliente sono rappresentati dalla `Customer` classe illustrata nel frammento di codice riportato di seguito. I collegamenti HATEOAS vengono archiviati nella `Links` proprietà di raccolta:
  
    ```C#
    public class Customer
    {
        public int CustomerID { get; set; }
        public string CustomerName { get; set; }
        public List<Link> Links { get; set; }
        ...
    }
  
    public class Link
    {
        public string Rel { get; set; }
        public string Href { get; set; }
        public string Action { get; set; }
        public string [] Types { get; set; }
    }
    ```
  
    L'operazione HTTP GET recupera i dati del cliente dalla memoria, costruisce un `Customer` oggetto e quindi li inserisce nella `Links` raccolta. Il risultato viene formattato come un messaggio di risposta JSON. Ogni collegamento comprende i seguenti campi:
  
  * La relazione tra l'oggetto restituito e l'oggetto descritto dal collegamento. In questo caso "self" indica che il collegamento è un riferimento all'oggetto stesso (simile a un `this` puntatore in molti linguaggi orientati agli oggetti), e "orders" è il nome di una raccolta contenente le informazioni sugli ordini correlati.
  * Il collegamento ipertestuale (`Href`) per l'oggetto viene descritto dal collegamento sotto forma di un URI.
  * Il tipo di richiesta HTTP (`Action`) che può essere inviata a questo URI.
  * Il formato dei dati (`Types`) che deve essere fornito nella richiesta HTTP o che può essere restituito nella risposta, a seconda del tipo di richiesta.
    
    I collegamenti HATEOAS illustrati nell'esempio di risposta HTTP indicano che un'applicazione client è in grado di eseguire le operazioni seguenti:
  * Una richiesta HTTP GET all'URI \_http://adventure-works.com/customers/2_ per recuperare i dettagli del cliente (di nuovo). I dati possono essere restituiti come XML o JSON.
  * Una richiesta HTTP PUT all'URI \_http://adventure-works.com/customers/2_ per modificare i dettagli del cliente. I nuovi dati devono essere forniti nel messaggio di richiesta nel formato x-www-form-urlencoded.
  * Una richiesta HTTP DELETE all'URI \_http://adventure-works.com/customers/2_ per eliminare il cliente. La richiesta non prevede informazioni aggiuntive o la restituzione dei dati nel corpo del messaggio di risposta.
  * Una richiesta HTTP GET all'URI \_http://adventure-works.com/customers/2/orders_ per trovare tutti gli ordini del cliente. I dati possono essere restituiti come XML o JSON.
  * Una richiesta HTTP PUT all'URI \_http://adventure-works.com/customers/2/orders_ per creare un nuovo ordine per il cliente. I dati devono essere forniti nel messaggio di richiesta nel formato x-www-form-urlencoded.

## Considerazioni sulla gestione delle eccezioni
Per impostazione predefinita, nel framework API Web ASP.NET, se un'operazione genera un'eccezione non rilevata, il framework restituisce un messaggio di risposta con codice di stato HTTP 500 (errore interno del server). In molti casi, questo approccio semplicistico non è utile in isolamento e rende difficile determinare la causa dell'eccezione. Pertanto è consigliabile adottare un approccio più completo per la gestione delle eccezioni e considerare i seguenti punti:

* **Acquisire le eccezioni e restituire una risposta significativa ai client**.
  
    Il codice che implementa un'operazione HTTP deve fornire l’eccezione completa gestendo le eccezioni non rilevate anziché consentirne la propagazione al framework API Web. Se un'eccezione non consente di completare correttamente l'operazione, l'eccezione può essere passata nuovamente al messaggio di risposta, che deve includere una descrizione significativa dell'errore che ha causato l’eccezione. L'eccezione deve includere anche il codice di stato HTTP appropriato anziché restituire semplicemente il codice di stato 500 per ogni situazione. Ad esempio, se la richiesta di un utente provoca un aggiornamento del database che viola un vincolo (ad esempio, il tentativo di eliminare un cliente che ha degli ordini in sospeso), è necessario restituire lo stato del codice 409 (conflitto) e un corpo del messaggio che indica il motivo del conflitto. Se un'altra condizione rende la richiesta irrealizzabile, è possibile restituire il codice di stato 400 (richiesta non valida). È possibile trovare un elenco completo dei codici di stato HTTP nella pagina [Definizioni dei codici di stato](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) sul sito Web W3C.
  
    Il codice seguente mostra un esempio che blocca diverse condizioni e restituisce una risposta appropriata.
  
    ```C#
    [HttpDelete]
    [Route("customers/{id:int}")]
    public IHttpActionResult DeleteCustomer(int id)
    {
        try
        {
            // Find the customer to be deleted in the repository
            var customerToDelete = repository.GetCustomer(id);
  
            // If there is no such customer, return an error response
            // with status code 404 (Not Found)
            if (customerToDelete == null)
            {
                    return NotFound();
            }
  
            // Remove the customer from the repository
            // The DeleteCustomer method returns true if the customer
            // was successfully deleted
            if (repository.DeleteCustomer(id))
            {
                // Return a response message with status code 204 (No Content)
                // To indicate that the operation was successful
                return StatusCode(HttpStatusCode.NoContent);
            }
            else
            {
                // Otherwise return a 400 (Bad Request) error response
                return BadRequest(Strings.CustomerNotDeleted);
            }
        }
        catch
        {
            // If an uncaught exception occurs, return an error response
            // with status code 500 (Internal Server Error)
            return InternalServerError();
        }
    }
    ```
  
  > [!TIP]
  > Non includere informazioni che potrebbero essere utili a un utente malintenzionato che tenta di introdursi nell'API Web. Per ulteriori informazioni, visitare la pagina [Gestione delle eccezioni in API Web ASP.NET](http://www.asp.net/web-api/overview/error-handling/exception-handling) del sito Web Microsoft.
  > 
  > [!NOTE]
  > Molti server Web bloccano le condizioni di errore prima che raggiungano l'API Web. Ad esempio, se si configura l'autenticazione a un sito Web e l'utente non riesce a fornire le informazioni di autenticazione corrette, il server web deve rispondere con il codice di stato 401 (non autorizzato). Una volta che un client è stato autenticato, il codice può eseguire i propri controlli per verificare che il client sia in grado di accedere alla risorsa richiesta. Se questa autorizzazione ha esito negativo, è necessario restituire il codice di stato 403 (accesso negato).
  > 
  > 
* **Gestire le eccezioni in modo coerente e registrare le informazioni sugli errori**.
  
    Per gestire le eccezioni in modo coerente, è necessario considerare l’implementazione di una strategia globale di gestione degli errori su tutta la API Web. È possibile raggiungere parzialmente questo obiettivo creando un filtro delle eccezioni che viene eseguito ogni volta che un controller genera un'eccezione non gestita che non è un’ `HttpResponseException` eccezione. Questo approccio è descritto nella pagina [Gestione delle eccezioni in API Web ASP.NET](http://www.asp.net/web-api/overview/error-handling/exception-handling) del sito Web Microsoft.
  
    Tuttavia, esistono diverse situazioni in cui un filtro delle eccezioni non rileva un'eccezione, tra cui:
  
  * Eccezioni generate dai costruttori dei controller.
  * Eccezioni generate dai gestori di messaggi.
  * Eccezioni generate durante il routing.
  * Eccezioni generate durante la serializzazione del contenuto di un messaggio di risposta.
    
    Per gestire questi casi, potrebbe essere necessario implementare un approccio più personalizzato. È inoltre necessario incorporare la registrazione di errori che permette di rilevare informazioni dettagliate su ogni eccezione. La registrazione degli errori può contenere informazioni dettagliate, purché non sia accessibile sul web ai client. L'articolo [Gestione globale degli errori dell’Api Web](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) sul sito Web Microsoft mostra come eseguire questa operazione.
* **Distinzione tra errori sul lato client ed errori sul lato server**.
  
    Il protocollo HTTP consente di distinguere tra errori che si verificano a causa dell'applicazione client (codici di stato HTTP 4xx) ed errori che sono causati da problemi sul server (codici di stato HTTP 5xx). È necessario assicurarsi di rispettare questa convenzione in tutti i messaggi di risposta di errore.

<a name="considerations-for-optimizing"></a>

## Considerazioni sull'ottimizzazione dell'accesso ai dati sul lato client
In un ambiente distribuito, che coinvolge, ad esempio, un server Web e applicazioni client, l’origine principale di problemi è la rete. Questo può fungere da notevole collo di bottiglia, soprattutto se un'applicazione client invia richieste o riceve dati frequentemente. È pertanto consigliabile ridurre al minimo la quantità di traffico che passa attraverso la rete. Quando si implementa il codice per recuperare le richieste, tenere presente quanto segue:

* **Supporto di memorizzazione nella cache sul lato client**.
  
    Il protocollo HTTP 1.1 supporta la memorizzazione nella cache nei client e nei server intermedi attraverso i quali una richiesta viene indirizzata dall'utilizzo dell'intestazione Cache-Control. Quando un'applicazione client invia una richiesta GET HTTP richiesta all'API Web, la risposta può includere un'intestazione Cache-Control che indica se i dati nel corpo della risposta possono essere memorizzati in modo sicuro nella cache dal client o da un server intermedio attraverso i quali è stata inviata la richiesta e per quanto prima che la richiesta scada e sia considerata non più valida. Nell'esempio seguente viene mostrata una richiesta HTTP GET e la relativa risposta che include un'intestazione Cache-Control:
  
    ```HTTP
    GET http://adventure-works.com/orders/2 HTTP/1.1
    ...
    ```
  
    ```HTTP
    HTTP/1.1 200 OK
    ...
    Cache-Control: max-age=600, private
    Content-Type: text/json; charset=utf-8
    Content-Length: ...
    {"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
    ```
  
    In questo esempio, l'intestazione Cache-Control specifica che i dati restituiti scadono dopo 600 secondi, è adatta solo per un singolo client e non deve essere archiviata in una cache condivisa utilizzata da altri client (è *privata*). L'intestazione Cache-Control può specificare *pubblica* anziché *privata* se i dati possono essere archiviati in una cache condivisa. Può specificare *no-store* se i dati **non** devono essere memorizzati nella cache dal client. Il seguente esempio di codice mostra come costruire un'intestazione Cache-Control in un messaggio di risposta:
  
    ```C#
    public class OrdersController : ApiController
    {
        ...
        [Route("api/orders/{id:int:min(0)}")]
        [HttpGet]
        public IHttpActionResult FindOrderByID(int id)
        {
            // Find the matching order
            Order order = ...;
            ...
            // Create a Cache-Control header for the response
            var cacheControlHeader = new CacheControlHeaderValue();
            cacheControlHeader.Private = true;
            cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
            ...
  
            // Return a response message containing the order and the cache control header
            OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
            {
                CacheControlHeader = cacheControlHeader
            };
            return response;
        }
        ...
    }
    ```
  
    Questo codice utilizza una `IHttpActionResult` classe denominata `OkResultWithCaching` personalizzata. Questa classe consente al controller di impostare i contenuti dell’intestazione cache:
  
    ```C#
    public class OkResultWithCaching<T> : OkNegotiatedContentResult<T>
    {
        public OkResultWithCaching(T content, ApiController controller)
            : base(content, controller) { }
  
        public OkResultWithCaching(T content, IContentNegotiator contentNegotiator, HttpRequestMessage request, IEnumerable<MediaTypeFormatter> formatters)
            : base(content, contentNegotiator, request, formatters) { }
  
        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }
  
        public override async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = await base.ExecuteAsync(cancellationToken);
  
            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = ETag;
  
            return response;
        }
    }
    ```
  
  > [!NOTE]
  > Il protocollo HTTP definisce inoltre la direttiva *no-cache* per l'intestazione Cache-Control. Questa direttiva non significa "non memorizzare nella cache" ma "riconvalidare le informazioni nella cache con il server prima di restituirle". I dati possono comunque essere memorizzati nella cache, che viene verificata ad ogni utilizzo per verificare che sia sempre valida.
  > 
  > 
  
    La gestione della cache è responsabilità dell’applicazione client o del server intermedio. Se implementata correttamente, può risparmiare larghezza di banda e migliorare le prestazioni, eliminando la necessità di recuperare i dati che sono già stati salvati.
  
    Il valore *max-age* nell'intestazione Cache-Control è solo un riferimento e non garantisce che i dati corrispondenti non verranno modificati nel tempo specificato. L'API Web deve impostare il valore max-age su un valore adatto a seconda della volatilità prevista dei dati. Quando questo periodo scade, il client deve eliminare l'oggetto dalla cache.
  
  > [!NOTE]
  > La maggior parte dei moderni Web browser supportano la memorizzazione nella cache sul lato client mediante l’aggiunta di appropriate intestazioni cache-control alle richieste ed esaminando le intestazioni dei risultati, come descritto. Tuttavia, alcuni browser meno recenti non memorizzano nella cache i valori restituiti da un URL che include una stringa di query. Non si tratta in genere di un problema per le applicazioni client personalizzate che implementano la propria strategia di gestione cache basata sul protocollo descritto di seguito.
  > 
  > Alcuni proxy meno recenti hanno lo stesso comportamento e non sono in grado di memorizzare nella cache le richieste basate su URL con stringhe di query. Potrebbe trattarsi di un problema per le applicazioni client personalizzate che si connettono a un server Web tramite un proxy di questo tipo.
  > 
  > 
* **Fornire valori ETag per ottimizzare l'elaborazione delle Query**.
  
    Quando un'applicazione client recupera un oggetto, il messaggio di risposta può includere anche un *ETag* (Tag di entità). Un valore ETag è una stringa opaca che indica la versione di una risorsa. Ogni volta che una risorsa cambia, viene modificato anche il valore Etag. Questo ETag deve essere memorizzato nella cache come parte dei dati. Il seguente esempio di codice mostra come aggiungere un ETag come parte della risposta a una richiesta HTTP GET. Questo codice utilizza il `GetHashCode` metodo di un oggetto per generare un valore numerico che identifica l'oggetto (è possibile sostituire questo metodo se necessario e generare il proprio hash utilizzando un algoritmo, ad esempio MD5):
  
    ```C#
    public class OrdersController : ApiController
    {
        ...
        public IHttpActionResult FindOrderByID(int id)
        {
            // Find the matching order
            Order order = ...;
            ...
  
            var hashedOrder = order.GetHashCode();
            string hashedOrderEtag = String.Format(""{0}"", hashedOrder);
            var eTag = new EntityTagHeaderValue(hashedOrderEtag);
  
            // Return a response message containing the order and the cache control header
            OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
            {
                ...,
                ETag = eTag
            };
            return response;
        }
        ...
    }
    ```
  
    Il messaggio di risposta inviato utilizzando l'API Web è simile al seguente:
  
    ```HTTP
    HTTP/1.1 200 OK
    ...
    Cache-Control: max-age=600, private
    Content-Type: text/json; charset=utf-8
    ETag: "2147483648"
    Content-Length: ...
    {"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
    ```
  
  > [!TIP]
  > Per motivi di sicurezza, non autorizzare dati riservati o dati restituiti tramite una connessione (HTTPS) autenticata da memorizzare nella cache.
  > 
  > 
  
    Un'applicazione client può inviare una richiesta GET successiva per recuperare la risorsa stessa in qualsiasi momento. Se la risorsa è stata modificata (con un ETag diverso), la versione memorizzata nella cache deve essere eliminata e la nuova versione deve essere aggiunta alla cache. Se una risorsa è di grandi dimensioni e richiede una notevole quantità di larghezza di banda per trasmettere al client, le richieste ripetute per recuperare i dati stessi possono diventare inefficienti. Per evitare questo inconveniente, il protocollo HTTP definisce il seguente processo per l'ottimizzazione delle richieste GET che devono essere supportate in un'API Web:
  
  * Il client crea una richiesta GET che contiene il valore ETag per la versione attualmente memorizzata nella cache della risorsa a cui fa riferimento in un'intestazione HTTP If-None-Match:
    
    ```HTTP
    GET http://adventure-works.com/orders/2 HTTP/1.1
    If-None-Match: "2147483648"
    ...
    ```
  * L'operazione GET nell'API Web ottiene l’attuale valore ETag per i dati richiesti (ordine 2 nell'esempio precedente) e lo confronta con il valore dell'intestazione If-None-Match.
  * Se l’attuale valore ETag per i dati richiesti corrisponde al valore ETag fornito dalla richiesta, la risorsa non è stata modificata e l'API Web deve restituire una risposta HTTP con un corpo del messaggio vuoto e un codice di stato di 304 (non modificato).
  * Se l’attuale valore ETag per i dati richiesti non corrisponde al valore ETag fornito dalla richiesta, i dati sono stati modificati e l'API Web deve restituire una risposta HTTP che contiene i nuovi dati nel corpo del messaggio e un codice di stato 200 (OK).
  * Se i dati richiesti non esistono più, l'API Web deve restituire una risposta HTTP con il codice di stato 404 (non trovato).
  * Il client utilizza il codice di stato per gestire la cache. Se i dati non sono stati modificati (codice di stato 304), l'oggetto può rimanere memorizzato nella cache e l'applicazione client deve continuare a utilizzare questa versione dell'oggetto. Se i dati sono stati modificati (codice di stato 200), l'oggetto memorizzato nella cache deve essere eliminato e deve essere inserito quello nuovo. Se i dati non sono più disponibili (codice di stato 404), l'oggetto deve essere rimosso dalla cache.
    
    > [!NOTE]
    > Se l'intestazione della risposta contiene l'intestazione Cache-Control no-store, l'oggetto deve sempre essere rimosso dalla cache indipendentemente dal codice di stato HTTP.
    > 
    > 
    
    Il codice seguente mostra il `FindOrderByID` metodo esteso per supportare l'intestazione If-None-Match. Si noti che, se si omette l'intestazione If-None-Match, viene recuperato sempre l'ordine specificato:
    
    ```C#
    public class OrdersController : ApiController
    {
         ...
      [Route("api/orders/{id:int:min(0)}")]
      [HttpGet]
      public IHttpActionResult FindOrderById(int id)
      {
          try
          {
              // Find the matching order
              Order order = ...;
    
              // If there is no such order then return NotFound
              if (order == null)
              {
                  return NotFound();
              }
    
              // Generate the ETag for the order
              var hashedOrder = order.GetHashCode();
              string hashedOrderEtag = String.Format(""{0}"", hashedOrder);
    
              // Create the Cache-Control and ETag headers for the response
              IHttpActionResult response = null;
              var cacheControlHeader = new CacheControlHeaderValue();
              cacheControlHeader.Public = true;
              cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
              var eTag = new EntityTagHeaderValue(hashedOrderEtag);
    
              // Retrieve the If-None-Match header from the request (if it exists)
              var nonMatchEtags = Request.Headers.IfNoneMatch;
    
              // If there is an ETag in the If-None-Match header and
              // this ETag matches that of the order just retrieved,
              // then create a Not Modified response message
              if (nonMatchEtags.Count > 0 &&
                  String.Compare(nonMatchEtags.First().Tag, hashedOrderEtag) == 0)
              {
                  response = new EmptyResultWithCaching()
                  {
                      StatusCode = HttpStatusCode.NotModified,
                      CacheControlHeader = cacheControlHeader,
                      ETag = eTag
                  };
              }
              // Otherwise create a response message that contains the order details
              else
              {
                  response = new OkResultWithCaching<Order>(order, this)
                  {
                      CacheControlHeader = cacheControlHeader,
                      ETag = eTag
                  };
              }
    
              return response;
          }
          catch
          {
              return InternalServerError();
          }
      }
    ...
    }
    ```
    
    Questo esempio incorpora una `IHttpActionResult` classe denominata `EmptyResultWithCaching` aggiuntiva personalizzata. Questa classe funge semplicemente da wrapper per un `HttpResponseMessage` oggetto che non contiene il corpo della risposta:
    
    ```C#
    public class EmptyResultWithCaching : IHttpActionResult
    {
      public CacheControlHeaderValue CacheControlHeader { get; set; }
      public EntityTagHeaderValue ETag { get; set; }
      public HttpStatusCode StatusCode { get; set; }
      public Uri Location { get; set; }
    
      public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
      {
          HttpResponseMessage response = new HttpResponseMessage(StatusCode);
          response.Headers.CacheControl = this.CacheControlHeader;
          response.Headers.ETag = this.ETag;
          response.Headers.Location = this.Location;
          return response;
      }
    }
    ```
    
    > [!TIP]
    > In questo esempio, il valore ETag per i dati viene generato eseguendo l'hashing dei dati recuperati dall'origine dati sottostante. Se il valore ETag può essere calcolato in modo diverso, è possibile ottimizzare ulteriormente il processo e solo i dati devono essere recuperati dall'origine dati se questa è stata modificata. Questo approccio è particolarmente utile se i dati sono di grandi dimensioni o l'accesso all'origine dati può comportare una latenza significativa (ad esempio, se l'origine dati è un database remoto).
    > 
    > 
* **Utilizzare il valore ETag per il supporto della concorrenza ottimistica**.
  
    Per abilitare gli aggiornamenti su dati memorizzati nella cache in precedenza, il protocollo HTTP supporta una strategia di concorrenza ottimistica. Se, dopo il recupero e la memorizzazione nella cache di una risorsa, l'applicazione client invia successivamente una richiesta PUT o DELETE per modificare o rimuovere la risorsa, tale richiesta deve includere nell'intestazione If-Match il riferimento al valore ETag. L'API Web può quindi utilizzare queste informazioni per determinare se la risorsa è già stata modificata da un altro utente quando è stata recuperata e per inviare una risposta appropriata all'applicazione client, come illustrato di seguito:
  
  * Il client crea una richiesta GET che contiene i nuovi dettagli della risorsa e il valore ETag per la versione attualmente memorizzata nella cache della risorsa a cui fa riferimento in un'intestazione HTTP If-None-Match. Il seguente esempio mostra una richiesta PUT che aggiorna un ordine:
    
    ```HTTP
    PUT http://adventure-works.com/orders/1 HTTP/1.1
    If-Match: "2282343857"
    Content-Type: application/x-www-form-urlencoded
    ...
    Date: Fri, 12 Sep 2014 09:18:37 GMT
    Content-Length: ...
    productID=3&quantity=5&orderValue=250
    ```
  * L'operazione PUT nell'API Web ottiene l’attuale valore ETag per i dati richiesti (ordine 1 nell'esempio precedente) e lo confronta con il valore dell'intestazione If-None-Match.
  * Se l’attuale valore ETag per i dati richiesti corrisponde al valore ETag fornito dalla richiesta, la risorsa non è stata modificata e l'API Web deve eseguire l’aggiornamento, restituendo un messaggio con codice di stato HTTP 204 (nessun contenuto) se l’operazione ha esito positivo. La risposta può includere intestazioni ETag e Cache-Control per la versione aggiornata della risorsa. La risposta deve sempre includere l'intestazione Location che fa riferimento all'URI della risorsa appena aggiornata.
  * Se l’attuale valore ETag per i dati richiesti non corrisponde al valore ETag fornito dalla richiesta, i dati sono stati modificati da un altro utente dal momento del loro recupero e l'API Web deve restituire una risposta HTTP con un corpo del messaggio vuoto e un codice di stato 412 (Condizione preliminare non riuscita).
  * Se la risorsa che deve essere aggiornata non esiste più, l'API Web deve restituire una risposta HTTP con il codice di stato 404 (non trovato).
  * Il client utilizza il codice di stato e le intestazioni della risposta per gestire la cache. Se i dati sono stati aggiornati (codice di stato 204), l'oggetto può rimanere memorizzato nella cache (fino a quando l'intestazione Cache-Control non specifica alcun archivio), ma il valore ETag che deve essere aggiornato. Se i dati sono stati modificati da un altro utente modificato (codice di stato 412) o non trovato (codice di stato 404), l'oggetto memorizzato nella cache deve essere eliminato.
    
    Il successivo esempio di codice mostra un'implementazione dell'operazione PUT per il controller degli Ordini:
    
    ```C#
    public class OrdersController : ApiController
    {
         ...
      [HttpPut]
      [Route("api/orders/{id:int}")]
              public IHttpActionResult UpdateExistingOrder(int id, DTOOrder order)
      {
          try
          {
              var baseUri = Constants.GetUriFromConfig();
              var orderToUpdate = this.ordersRepository.GetOrder(id);
              if (orderToUpdate == null)
              {
                  return NotFound();
              }
    
              var hashedOrder = orderToUpdate.GetHashCode();
              string hashedOrderEtag = String.Format(""{0}"", hashedOrder);
    
              // Retrieve the If-Match header from the request (if it exists)
              var matchEtags = Request.Headers.IfMatch;
    
              // If there is an Etag in the If-Match header and
              // this etag matches that of the order just retrieved,
              // or if there is no etag, then update the Order
              if (((matchEtags.Count > 0 &&
                   String.Compare(matchEtags.First().Tag, hashedOrderEtag) == 0)) ||
                   matchEtags.Count == 0)
              {
                  // Modify the order
                  orderToUpdate.OrderValue = order.OrderValue;
                  orderToUpdate.ProductID = order.ProductID;
                  orderToUpdate.Quantity = order.Quantity;
    
                  // Save the order back to the data store
                  // ...
    
                  // Create the No Content response with Cache-Control, ETag, and Location headers
                  var cacheControlHeader = new CacheControlHeaderValue();
                  cacheControlHeader.Private = true;
                  cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
    
                  hashedOrder = order.GetHashCode();
                  hashedOrderEtag = String.Format(""{0}"", hashedOrder);
                  var eTag = new EntityTagHeaderValue(hashedOrderEtag);
    
                  var location = new Uri(string.Format("{0}/{1}/{2}", baseUri, Constants.ORDERS, id));
                  var response = new EmptyResultWithCaching()
                  {
                      StatusCode = HttpStatusCode.NoContent,
                      CacheControlHeader = cacheControlHeader,
                      ETag = eTag,
                      Location = location
                  };
    
                  return response;
              }
    
              // Otherwise return a Precondition Failed response
              return StatusCode(HttpStatusCode.PreconditionFailed);
          }
          catch
          {
              return InternalServerError();
          }
      }
      ...
    }
    ```
    
    > [!TIP]
    > L’utilizzo dell'intestazione If-Match è completamente facoltativo; se viene omesso l’API Web tenterà sempre di aggiornare l'ordine specificato, sovrascrivendo alla cieca un aggiornamento effettuato da un altro utente. Per evitare problemi dovuti alla perdita di aggiornamenti, è necessario fornire sempre un'intestazione If-Match.
    > 
    > 

<a name="considerations-for-handling-large"></a>

## Considerazioni per la gestione di risposte e richieste di grandi dimensioni
Si possono verificare casi in cui un’applicazione client deve emettere richieste di invio o ricezione dati delle dimensioni di diversi megabyte (o maggiori). L’applicazione potrebbe bloccarsi mentre questa quantità di dati viene trasmessa. Quando è necessario gestire le richieste che includono quantità elevate di dati, considerare quanto riportato di seguito:

* **Ottimizzare richieste e risposte contenenti oggetti di grandi dimensioni**.
  
    Alcune risorse potrebbero essere oggetti di grandi dimensioni o includere campi di grandi dimensioni, ad esempio immagini grafiche o altri tipi di dati binari. Un'API Web deve supportare lo streaming per abilitare il caricamento e il download ottimizzati di queste risorse.
  
    Il protocollo HTTP fornisce il meccanismo di codifica di trasferimento in blocchi per eseguire lo streaming di oggetti di dati di grandi dimensioni a un client. Quando il client invia una richiesta HTTP GET per un oggetto di grandi dimensioni, l'API Web può inviare la risposta in *blocchi* frammentari su una connessione HTTP. La lunghezza dei dati nella risposta potrebbe non essere nota inizialmente (potrebbe essere generata), in modo che il server che ospita l'API Web debba inviare un messaggio di risposta con ogni blocco che specifica la codifica di trasferimento: blocchi di intestazione anziché un'intestazione Content-Length. L'applicazione client può ricevere, a sua volta, ogni blocco per costruire la risposta completa. Il trasferimento dei dati viene completato quando il server restituisce un blocco finale di dimensioni pari a zero. È possibile implementare la suddivisione in blocchi nell'API Web ASP.NET utilizzando la `PushStreamContent` classe.
  
    Il seguente esempio mostra un'operazione che risponde alle richieste HTTP GET per le immagini del prodotto:
  
    ```C#
    public class ProductImagesController : ApiController
    {
        ...
        [HttpGet]
        [Route("productimages/{id:int}")]
        public IHttpActionResult Get(int id)
        {
            try
            {
                var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);
  
                if (!BlobExists(container, string.Format("image{0}.jpg", id)))
                {
                    return NotFound();
                }
                else
                {
                    return new FileDownloadResult()
                    {
                        Container = container,
                        ImageId = id
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
        ...
    }
    ```
  
    In questo esempio, `ConnectBlobToContainer` è un metodo helper che si connette a un contenitore specificato (nome non visualizzato) nell'archiviazione Blob di Azure. `BlobExists` è un altro metodo di supporto che restituisce un valore booleano che indica se un blob con il nome specificato è presente nel contenitore di archiviazione blob.
  
    Ogni prodotto dispone di un'immagine contenuta nell'archiviazione blob. La `FileDownloadResult` classe è una`IHttpActionResult` classe personalizzata che utilizza un `PushStreamContent` oggetto per leggere i dati dell'immagine dal blob appropriato e per trasmetterlo in modo asincrono come contenuto del messaggio di risposta:
  
    ```C#
    public class FileDownloadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }
  
        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            response.Content = new PushStreamContent(async (outputStream, _, __) =>
            {
                try
                {
                    CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
                    await blockBlob.DownloadToStreamAsync(outputStream);
                }
                finally
                {
                    outputStream.Close();
                }
            });
  
            response.StatusCode = HttpStatusCode.OK;
            response.Content.Headers.ContentType = new MediaTypeHeaderValue("image/jpeg");
            return response;
        }
    }
    ```
  
    È inoltre possibile applicare lo streaming per operazioni di caricamento se un client deve PUBBLICARE una nuova risorsa che include un oggetto di grandi dimensioni. Il seguente esempio mostra il metodo Post per il `ProductImages` controller. Questo metodo consente al client di caricare una nuova immagine del prodotto:
  
    ```C#
    public class ProductImagesController : ApiController
    {
        ...
        [HttpPost]
        [Route("productimages")]
        public async Task<IHttpActionResult> Post()
        {
            try
            {
                if (!Request.Content.Headers.ContentType.MediaType.Equals("image/jpeg"))
                {
                    return StatusCode(HttpStatusCode.UnsupportedMediaType);
                }
                else
                {
                    var id = new Random().Next(); // Use a random int as the key for the new resource. Should probably check that this key has not already been used
                    var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);
                    return new FileUploadResult()
                    {
                        Container = container,
                        ImageId = id,
                        Request = Request
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
        ...
    }
    ```
  
    Questo codice utilizza un’altra `IHttpActionResult` classe denominata `FileUploadResult` personalizzata. Questa classe contiene la logica per caricare i dati in modo asincrono:
  
    ```C#
    public class FileUploadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }
        public HttpRequestMessage Request { get; set; }
  
        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
            await blockBlob.UploadFromStreamAsync(await Request.Content.ReadAsStreamAsync());
            var baseUri = string.Format("{0}://{1}:{2}", Request.RequestUri.Scheme, Request.RequestUri.Host, Request.RequestUri.Port);
            response.Headers.Location = new Uri(string.Format("{0}/productimages/{1}", baseUri, ImageId));
            response.StatusCode = HttpStatusCode.OK;
            return response;
        }
    }
    ```
  
  > [!TIP]
  > Il volume dei dati che è possibile caricare in un servizio Web non è vincolato dallo streaming e una singola richiesta presumibilmente potrebbe determinare un oggetto notevole che utilizza risorse considerevoli. Se, durante il processo di streaming, l'API Web determina che la quantità di dati in una richiesta ha superato alcuni limiti accettabili, può interrompere l'operazione e restituire un messaggio di risposta con codice di stato 413 (Entità richiesta troppo grande).
  > 
  > 
  
    È possibile ridurre le dimensioni di oggetti di grandi dimensioni trasmessi in rete utilizzando la compressione HTTP. Questo approccio aiuta a ridurre la quantità di traffico di rete e la latenza di rete associati, ma a costo di richiedere ulteriori elaborazioni al client e al server che ospita l'API Web. Ad esempio, un'applicazione client che si aspetta di ricevere dati compressi può includere un’intestazione Accept-Encoding: gzip della richiesta (è inoltre possibile specificare altri per la compressione dei dati). Se il server supporta la compressione deve rispondere con il contenuto in formato gzip nel corpo del messaggio e l’intestazione Content-Encoding: gzip della risposta.
  
  > [!TIP]
  > È possibile combinare la compressione codificata con la trasmissione; comprimere i dati prima della loro trasmissione e specificare la codifica del contenuto gzip e la codifica del trasferimento in blocchi nelle intestazioni del messaggio. Si noti inoltre che alcuni server Web (ad esempio Internet Information Server) possono essere configurati per comprimere automaticamente le risposte HTTP indipendentemente dal fatto che l'API Web comprima i dati o meno.
  > 
  > 
* **Implementare risposte parziali per i client che non supportano le operazioni asincrone**.
  
    In alternativa al flusso asincrono, un'applicazione client può richiedere in modo esplicito dati per oggetti di grandi dimensioni in blocchi, noti come risposte parziali. L'applicazione client invia una richiesta HEAD HTTP per ottenere informazioni sull'oggetto. Se l’API Web supporta risposte parziali se deve rispondere alla richiesta HEAD con un messaggio di risposta che contiene un'intestazione Accept-Ranges e un'intestazione Content-Length che indica la dimensione totale dell'oggetto, ma il corpo del messaggio deve essere vuoto. L'applicazione client può utilizzare queste informazioni per costruire una serie di richieste GET che specificano un intervallo di byte da ricevere. L'API Web deve restituire un messaggio di risposta con stato HTTP 206 (contenuto parziale), un'intestazione Content-Length che specifica l’attuale quantità di dati inclusi nel corpo del messaggio di risposta e un'intestazione Content-Range che indica quale parte (ad esempio, 4000 e 8000 byte) dell'oggetto rappresenta questi dati.
  
    Le richieste HTTP HEAD e le risposte parziali sono descritte in dettaglio nel documento Linee Guida alla progettazione di un’API.
* **Evitare l’invio di inutili messaggi di stato nelle applicazioni client**.
  
    Un'applicazione client che è sul punto di inviare una grande quantità di dati a un server può determinare innanzitutto se il server è effettivamente disposto ad accettare la richiesta. Prima di inviare i dati, l'applicazione client può inviare una richiesta HTTP con un’intestazione Expect: 100-Continue, un'intestazione Content-Length che indica le dimensioni dei dati, ma un corpo del messaggio vuoto. Se il server è disposto a gestire la richiesta, deve rispondere con un messaggio che specifica lo stato HTTP 100 (Continua). L'applicazione client può quindi procedere e inviare la richiesta completa, inclusi i dati nel corpo del messaggio.
  
    Se si ospita un servizio tramite IIS, il driver HTTP. sys rileva automaticamente e gestisce le intestazioni Expect: 100-continuare prima di passare le richieste all'applicazione Web. Ciò significa che è improbabile visualizzare queste intestazioni nel codice dell'applicazione e si può presupporre che IIS abbia già filtrato tutti i messaggi che ritiene non idonei o di dimensioni troppo grandi.
  
    Se si creano applicazioni client utilizzando .NET Framework, tutti i messaggi POST e PUT invieranno innanzitutto messaggi con intestazioni Expect: 100-Continue per impostazione predefinita. Come con il lato server, il processo viene gestito in modo trasparente da .NET Framework. Tuttavia, questo processo causa, in ogni richiesta POST e PUT, 2 round trip al server, anche per le richieste di piccole dimensioni. Se l'applicazione non invia richieste con grandi quantità di dati, è possibile disattivare questa funzionalità utilizzando la `ServicePointManager` classe per creare `ServicePoint` oggetti nell'applicazione client. Un `ServicePoint` oggetto gestisce le connessioni che il client effettua a un server basato sui frammenti di URI di schema e l'host che identificano le risorse del server. È quindi possibile impostare la `Expect100Continue` proprietà dell’ `ServicePoint` oggetto su false. Tutte le successive richieste POST e PUT effettuate dal client tramite un URI che corrisponde a frammenti di schema e host dell’ `ServicePoint` oggetto saranno inviate senza le intestazioni Expect: 100-Continue. Il codice seguente mostra come configurare un `ServicePoint` oggetto che consente di configurare tutte le richieste inviate a URI con uno schema di `http` e un host di `www.contoso.com`.
  
    ```C#
    Uri uri = new Uri("http://www.contoso.com/");
    ServicePoint sp = ServicePointManager.FindServicePoint(uri);
    sp.Expect100Continue = false;
    ```
  
    È inoltre possibile impostare il metodo statico `Expect100Continue` proprietà della `ServicePointManager` classe per specificare il valore predefinito di questa proprietà per tutti gli`ServicePoint` oggetti creati successivamente. Per ulteriori informazioni, vedere la pagina [ServicePoint Class](https://msdn.microsoft.com/library/system.net.servicepoint.aspx) del sito Web Microsoft.
* **Supporto per l'impaginazione di richieste che possono restituire un numero elevato di oggetti**.
  
    Se una raccolta contiene un numero elevato di risorse, l’esecuzione di una richiesta GET all'URI corrispondente potrebbe determinare un’elaborazione significativa sul server delle prestazioni che interessano l’API Web e generare una quantità significativa di traffico di rete determinando un aumento della latenza.
  
    Per gestire questi casi, l'API Web deve supportare le stringhe di query che consentono all'applicazione client di ridefinire le richieste o recuperare i dati in blocchi (o pagine) più gestibili o discreti. Il framework API Web ASP.NET analizza le stringhe di query e le suddivide in una serie di coppie parametro/valore che vengono passate al metodo appropriato, seguendo le regole di routing descritte in precedenza. Il metodo deve essere implementato per accettare questi parametri utilizzando lo stesso nome specificato nella stringa di query. Inoltre, questi parametri devono essere facoltativi (nel caso in cui il client consenta di omettere la stringa di query da una richiesta) e presentare valori predefiniti significativi. Il codice seguente mostra il `GetAllOrders` metodo del `Orders` controller. Questo metodo recupera i dettagli di ordini. Se questo metodo è stato vincolato, potrebbe restituire presumibilmente una grande quantità di dati. I parametri`limit` e `offset` consentono di ridurre il volume dei dati a un sottoinsieme più piccolo, in questo caso, solo i primi 10 ordini per impostazione predefinita:
  
    ```C#
    public class OrdersController : ApiController
    {
        ...
        [Route("api/orders")]
        [HttpGet]
        public IEnumerable<Order> GetAllOrders(int limit=10, int offset=0)
        {
            // Find the number of orders specified by the limit parameter
            // starting with the order specified by the offset parameter
            var orders = ...
            return orders;
        }
        ...
    }
    ```
  
    Un'applicazione client può emettere una richiesta per recuperare 30 ordini a partire dall'offset 50 utilizzando l'URI \_http://www.adventure-works.com/api/orders?limit=30&offset=50_.
  
  > [!TIP]
  > Evitare di abilitare le applicazioni client per specificare le stringhe di query che generano un URI con più di 2000 caratteri. Molti client Web e server non possono gestire URI di questa lunghezza.
  > 
  > 

<a name="considerations-for-maintaining-responsiveness"></a>

## Considerazioni sulla gestione della disponibilità, scalabilità e velocità di risposta
La stessa API Web può essere utilizzata da numerose applicazioni client in esecuzione ovunque nel mondo. È importante assicurarsi che l'API Web sia implementata per mantenere la velocità di risposta con un carico pesante, per essere scalabile per supportare un carico di lavoro estremamente variabile e per garantire la disponibilità per i client che eseguono operazioni critiche. Per determinare le modalità per soddisfare tali requisiti, tenere presente quanto segue:

* **Fornire supporto asincrono per le richieste a esecuzione prolungata**.
  
    Una richiesta che potrebbe richiedere molto tempo per l'elaborazione deve essere eseguita senza bloccare il client che ha inviato tale richiesta. L'API Web può eseguire un controllo iniziale per convalidare la richiesta, avviare un'attività separata per eseguire il lavoro e quindi restituire un messaggio di risposta con codice HTTP 202 (Accettato). L'attività può essere eseguita in modo asincrono come parte dell'elaborazione dell’API Web, oppure può essere delegata a un processo Web di Azure (se l'API Web è ospitata da un sito Web di Azure) o a un ruolo di lavoro (se l'API web viene implementata come un servizio cloud di Azure).
  
  > [!NOTE]
  > Per ulteriori informazioni sull'utilizzo di processi Web con il sito Web di Azure, visitare la pagina [Utilizzare i processi Web per l'esecuzione di attività in background in siti Web di Microsoft Azure](app-service-web/web-sites-create-web-jobs.md) del sito Web Microsoft.
  > 
  > 
  
    L'API Web deve inoltre fornire un meccanismo per restituire i risultati dell'elaborazione all'applicazione client. È possibile ottenere questo risultato, fornendo un meccanismo di polling per le applicazioni client per chiedere periodicamente se l'elaborazione è terminata e ottenere il risultato o l'abilitazione di un’Api Web per inviare una notifica quando l'operazione è stata completata.
  
    È possibile implementare un semplice meccanismo di polling, fornendo un *polling* dell’URI che funge da risorsa virtuale utilizzando l'approccio seguente:
  
  1. L'applicazione client invia la richiesta iniziale all'API Web.
  2. L'API Web archivia informazioni sulla richiesta in una tabella conservata nell'archiviazione tabella o nella Cache di Microsoft Azure e genera una chiave univoca per questa voce, possibilmente in forma di GUID.
  3. L'API Web avvia l'elaborazione come un'attività separata. L'API web registra lo stato dell'attività nella tabella come *In esecuzione*.
  4. L'API Web restituisce un messaggio di risposta con codice di stato HTTP 202 (Accettato) e il GUID della voce della tabella nel corpo del messaggio.
  5. Quando l'attività è completata, l'API Web archivia i risultati nella tabella e imposta lo stato dell'attività su *Operazione completata*. Si noti che se l'attività ha esito negativo, anche l'API Web può archiviare le informazioni sull'errore e impostare lo stato su *Operazione non riuscita*.
  6. Mentre l'attività è in esecuzione, il client può continuare a eseguire la propria elaborazione. Periodicamente può inviare una richiesta all'URI */polling/ {guid}* dove *{guid}* è il GUID restituito nel messaggio di 202 risposta dall’API Web.
  7. L'API Web nell'URI */polling/{guid}* esegue query sullo stato dell'attività corrispondente nella tabella e restituisce un messaggio di risposta con codice di stato HTTP 200 (OK) che contiene lo stato *In esecuzione*, *Operazione completata* oppure *Operazione non riuscita*. Se l'attività è stata completato o ha avuto esito negativo, il messaggio di risposta può includere anche i risultati dell'elaborazione oppure le informazioni disponibili relative al motivo dell'esito negativo.
     
     Se si preferisce implementare le notifiche, le opzioni disponibili includono:
  8. Utilizzo di un Hub di notifica di Azure per spingere le risposte asincrone alle applicazioni client. La pagina [Notifica agli utenti dell’hub di notifica di Microsoft Azure i](notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) sul sito Web Microsoft fornisce ulteriori dettagli.
  9. L’utilizzo del modello Comet per mantenere una connessione di rete permanente tra il client e server che ospita l'API Web e l’utilizzo di questa connessione per spingere i messaggi dal server fino al client. L'articolo di MSDN Magazine [Creazione di una semplice applicazione Comet in Microsoft .NET Framework](https://msdn.microsoft.com/magazine/jj891053.aspx) descrive una soluzione di esempio.
  10. L’utilizzo di SignalR per spingere i dati in tempo reale dal server Web al client su una connessione di rete permanente. SignalR è disponibile per le applicazioni Web ASP.NET come pacchetto NuGet. È possibile trovare ulteriori informazioni sul sito Web[ASP.NET SignalR](http://signalr.net/).
      
      > [!NOTE]
      > Comet e SignalR utilizzano entrambi connessioni di rete permanenti tra il server Web e l'applicazione client. Questo può influire sulla scalabilità come un numero elevato di client può richiedere un numero altrettanto elevato di connessioni simultanee.
      > 
      > 
* **Assicurarsi che ogni richiesta sia senza stato**.
  
    Ogni richiesta deve essere considerata atomica. Non deve esistere alcuna dipendenza tra una richiesta effettuata da un'applicazione client e le successive richieste inviate dal client stesso. Questo approccio semplifica la scalabilità; le istanze del servizio Web possono essere distribuite su un certo numero di server. Le richieste client possono essere indirizzate a una qualsiasi istanza e i risultati devono rimanere invariati. Inoltre, migliora la disponibilità: se un server Web smette di funzionare, le richieste possono essere indirizzate a un'altra istanza, tramite la Gestione traffico di Azure, mentre il server viene riavviato senza effetti negativi sulle applicazioni client.
* **Tenere traccia dei client e implementare la limitazione per ridurre il rischio di attacchi DOS**.
  
    Se un client specifico esegue un numero elevato di richieste entro un determinato periodo di tempo potrebbe monopolizzare il servizio e influire sulle prestazioni degli altri client. Per evitare questo problema, un'API Web può monitorare le chiamate dalle applicazioni client o registrando l'indirizzo IP di tutte le richieste in ingresso, o registrando ogni accesso autenticato. È possibile utilizzare queste informazioni per limitare l'accesso alle risorse. Se un client supera un limite definito, l'API Web può restituire un messaggio di risposta con stato 503 (Servizio non disponibile) e può includere un'intestazione Retry-After che specifica quando il client può inviare la richiesta successiva senza che questa venga rifiutata. Questa strategia può contribuire a ridurre le probabilità di un attacco Denial Of Service (DOS) da un set di client che provocano l’arresto del sistema.
* **Gestire attentamente le connessioni HTTP permanenti**.
  
    Il protocollo HTTP supporta le connessioni HTTP permanenti dove sono disponibili. Le specifiche HTTP 1.0 hanno aggiunto l’intestazione: Connection: Keep-Alive che consente a un'applicazione client di indicare al server che è possibile utilizzare la stessa connessione per inviare le richieste successive, anziché aprire nuove connessioni. La connessione verrà chiusa automaticamente se il client non riutilizzare la connessione all'interno di un periodo definito dall'host. Questo comportamento è l'impostazione predefinita in HTTP 1.1 come utilizzato dai servizi di Azure, pertanto non è necessario includere le intestazioni Keep-Alive nei messaggi.
  
    Mantenere aperta una connessione può contribuire a migliorare la velocità di risposta riducendo la latenza e la congestione della rete, ma può avere effetti negativi sulla scalabilità, mantenendo aperte connessioni inutili più a lungo del necessario e limitando la possibilità di connessione ad altri client simultanei. Può inoltre influire sulla durata della batteria se l'applicazione client è in esecuzione su un dispositivo mobile. Se l'applicazione effettua solo occasionali richieste al server, la gestione di una connessione aperta può scaricare più velocemente la batteria. Per garantire che una connessione non venga resa persistente con HTTP 1.1, il client può includere un'intestazione Connection:Close con i messaggi per eseguire l'override del comportamento predefinito. Analogamente, se un server gestisce un numero molto elevato di client può includere un'intestazione di Connection:Close nei messaggi di risposta che devono chiudere la connessione e salvare le risorse del server.
  
  > [!NOTE]
  > Le connessioni HTTP permanenti sono una funzionalità facoltativa esclusivamente per ridurre il sovraccarico di rete associato al tentativo di stabilire ripetutamente un canale di comunicazione. Né L'API Web né l'applicazione client devono dipendere da una connessione HTTP permanente disponibile. Non utilizzare connessioni HTTP permanenti per implementare sistemi di notifica Comet stile; è invece consigliabile utilizzare Socket (o WebSocket, se disponibile) a livello di TCP. Si noti infine che le intestazioni Keep-Alive hanno un utilizzo limitato se un'applicazione client comunica con un server tramite un proxy. Solo la connessione con il client e il proxy è permanente.
  > 
  > 

## Considerazioni sulla pubblicazione e sulla gestione di un'API Web
Per rendere disponibile un’API Web per le applicazioni client, l'API Web deve essere distribuito in un ambiente host. Questo ambiente è in genere un server Web, sebbene possa essere un altro tipo di processo host. Quando si pubblica un'API Web, è necessario considerare quanto riportato di seguito:

* Tutte le richieste devono essere autenticate e autorizzate e deve essere applicato il livello di controllo di accesso appropriato.
* Un'API web commerciale potrebbe essere soggetta a vari garanzie di qualità relativamente ai tempi di risposta. È importante assicurarsi che l'ambiente host sia scalabile se il carico può variare in modo significativo nel corso del tempo.
* Se può essere necessario controllare le richieste per scopi di monetizzazione.
* Potrebbe essere necessario regolare il flusso del traffico all'API Web e implementare la limitazione delle richieste per i client specifici che hanno esaurito le loro quote.
* Requisiti normativi potrebbero imporre la registrazione e il controllo di tutte le richieste e risposte.
* Per garantire la disponibilità, è necessario monitorare l'integrità del server che ospita l'API Web e riavviare se necessario.

È utile essere in grado di separare questi problemi dai problemi tecnici riguardanti l'implementazione dell'API Web. Per questo motivo, è consigliabile creare un’ [interfaccia](http://en.wikipedia.org/wiki/Facade_pattern), in esecuzione come un processo separato e che indirizzi le richieste all’API Web. L’interfaccia può fornire le operazioni di gestione e inoltrare le richieste convalidate all'API Web. Utilizzare un’interfaccia può portare inoltre molti vantaggi funzionali, tra cui:

* Agire come un punto di integrazione per più API Web.
* Trasformare i messaggi e convertire i protocolli di comunicazione per i client creati utilizzando diverse tecnologie.
* Memorizzare nella cache richieste e risposte per ridurre il carico sul server che ospita l'API Web.

## Considerazioni sui test per un'API Web
Un'API Web deve essere testata accuratamente come qualsiasi altro elemento del software. È necessario considerare la creazione di unit test per convalidare la funzionalità di ogni operazione, come si farebbe con qualsiasi altro tipo di applicazione. Per ulteriori informazioni, vedere la pagina [Verifica codice utilizzando Unit test ](https://msdn.microsoft.com/library/dd264975.aspx) del sito Web Microsoft.

> [!NOTE]
> L’ esempio di API Web disponibile in questa guida include un progetto di test che mostra come eseguire unit test su operazioni selezionate.
> 
> 

La natura di un'API Web introduce requisiti aggiuntivi specifici per verificare che funzioni correttamente. È necessario prestare particolare attenzione ai seguenti aspetti:

* Testare tutte le route per verificare che richiamino le operazioni corrette. Fare particolare attenzione se il codice di stato HTTP 405 (Metodo non consentito) viene restituito in modo imprevisto, poiché questo può indicare una mancata corrispondenza tra una route e i metodi HTTP (GET, POST, PUT, DELETE) che possono essere indirizzati a tale route.
  
    Inviare richieste HTTP alla route che non le supporta, ad esempio inviare una richiesta POST a una risorsa specifica (le richieste POST devono essere inviate solo alle raccolte di risorse). In questi casi, l’unica risposta valida *dovrebbe* essere codice di stato 405 (Non consentito).
* Verificare che tutte le route siano protette correttamente e siano soggette ai controlli di autenticazione e autorizzazione appropriati.
  
  > [!NOTE]
  > Alcuni aspetti della sicurezza, ad esempio l'autenticazione degli utenti, sono molto probabilmente responsabilità dell'ambiente host anziché dell'API Web, ma è comunque necessario includere test di sicurezza come parte del processo di distribuzione.
  > 
  > 
* Testare la gestione delle eccezioni eseguite da ogni operazione e verificare che una risposta HTTP appropriata e significativa sia passata all'applicazione client.
* Verificare che i messaggi di richiesta e risposta siano ben formati. Ad esempio, se una richiesta POST HTTP contiene i dati per una nuova risorsa nel formato x-www-form-urlencoded, verificare che l'operazione corrispondente analizzi correttamente i dati, crei le risorse e restituisca una risposta contenente i dettagli della nuova risorsa, compresa l'intestazione del percorso corretto.
* Verificare tutti i collegamenti e gli URI nei messaggi di risposta. Ad esempio, un messaggio POST HTTP deve restituire l'URI della risorsa appena creata. Tutti i collegamenti HATEOAS devono essere validi.
  
  > [!IMPORTANT]
  > Se si pubblica l'API Web tramite un servizio di gestione API, questi URI devono riflettere l'URL del servizio di gestione e non quello del server Web che ospita l'API Web.
  > 
  > 
* Assicurarsi che ogni operazione restituisca i codici di stato corretti per diverse combinazioni di input. Ad esempio:
  
  * Se una query ha esito positivo, deve restituire il codice di stato 200 (OK)
  * Se una risorsa non viene trovata, l'operazione deve restituire il codice di stato HTTP 404 (Non trovato).
  * Se il client invia una richiesta che consente di eliminare una risorsa, il codice di stato deve essere 204 (Nessun contenuto).
  * Se il client invia una richiesta che crea una nuova risorsa, il codice di stato deve essere 201 (Creato).

Fare attenzione alle risposte non previste per i codici di stato compresi nell'intervallo 5xx. Questi messaggi vengono in genere segnalati dal server host per indicare che non è riuscito a soddisfare una richiesta valida.

* Testare le diverse combinazioni di richiesta di intestazione che un'applicazione client può specificare e assicurarsi che l'API Web restituisca le informazioni previste nei messaggi di risposta.
* Testare le stringhe di query. Se un'operazione può richiedere parametri facoltativi (ad esempio richieste di impaginazione), testare le diverse combinazioni e l'ordine dei parametri.
* Verificare che le operazioni asincrone siano state completate correttamente. Se l'API Web supporta il flusso per le richieste che restituiscono oggetti binari di grandi dimensioni (come video o audio), assicurarsi che le richieste client non siano bloccate durante il trasferimento dei dati. Se l'API Web implementa il polling per operazioni di modifica dei dati a esecuzione prolungata, verificare che le operazioni riportino il proprio stato correttamente durante l'esecuzione.

È necessario creare ed eseguire test delle prestazioni per verificare che l'API Web funzioni in modo soddisfacente sotto costrizione. È possibile creare un progetto di test di carico e delle prestazioni web utilizzando Visual Studio Ultimate. Per ulteriori informazioni, vedere la pagina [Eseguire test delle prestazioni su un'applicazione prima del suo rilascio](https://msdn.microsoft.com/library/dn250793.aspx) del sito Web Microsoft.

## Pubblicare e gestire un'API Web mediante il servizio di gestione API di Azure
Azure fornisce il [Servizio di gestione API](https://azure.microsoft.com/documentation/services/api-management/) che è utilizzabile per pubblicare e gestire un'API Web. Questa funzionalità consente di generare un servizio che funge da interfaccia per una o più API Web. Il servizio è a sua volta un servizio Web scalabile che è possibile creare e configurare tramite il portale di gestione di Azure. È possibile utilizzare questo servizio per pubblicare e gestire un'API Web come indicato di seguito:

1. Distribuire l'API Web su un sito Web, un servizio cloud di Azure o una macchina virtuale di Azure.
2. Connettere il Servizio di gestione API all'API web. Le richieste inviate all'URL della gestione API vengono associate agli URI nell'API Web. Lo stesso Servizio di gestione API può indirizzare le richieste a più API Web. Questo consente di aggregare più API Web in un servizio di gestione singolo. Allo stesso modo, alla stessa API Web può fare riferimento più di un Servizio di gestione API se è necessario limitare o partizionare le funzionalità disponibili per applicazioni diverse.
   
   > [!NOTE]
   > Gli URI nei collegamenti HATEOAS generati come parte della risposta per le richieste HTTP GET devono fare riferimento all'URL del Servizio di gestione API e non al server Web che ospita l'API Web.
   > 
   > 
3. Per ogni API Web, specificare le operazioni HTTP che espone l'API Web insieme a eventuali parametri facoltativi che un'operazione può accettare come input. È inoltre possibile configurare se il Servizio di gestione API deve memorizzare nella cache la risposta ricevuta dall'API Web per ottimizzare le richieste ripetute per gli stessi dati. Registrare i dettagli di ogni risposta HTTP che ogni operazione può generare. Queste informazioni vengono utilizzate per generare la documentazione per gli sviluppatori, pertanto è importante che siano accurate e complete.
   
    È possibile definire le operazioni manualmente mediante le procedure guidate disponibili nel portale di gestione di Azure o è possibile importarle da un file contenente le definizioni in formato WADL o Swagger.
4. Configurare le impostazioni di sicurezza per le comunicazioni tra il Servizio di gestione API e il server Web che ospita l'API Web. Il Servizio di gestione API supporta attualmente l'autenticazione di base e l'autenticazione reciproca utilizzando i certificati e l'autorizzazione utente OAuth 2.0.
5. Creare un prodotto. Un prodotto è l'unità di pubblicazione. È necessario aggiungere le API Web precedentemente connesse al servizio di gestione al prodotto. Quando il prodotto è pubblicato, le API Web diventano disponibili per gli sviluppatori.
   
   > [!NOTE]
   > Prima della pubblicazione di un prodotto, è inoltre possibile definire gruppi utenti che possono accedere al prodotto e aggiungere utenti a questi gruppi. Questo consente di controllare gli sviluppatori e le applicazioni che possono utilizzare l'API Web. Se un’API Web è soggetta ad approvazione, prima di essere in grado di accedervi, uno sviluppatore deve inviare una richiesta all'amministratore del prodotto. L'amministratore può concedere o negare l'accesso allo sviluppatore. Gli sviluppatori esistenti possono anche essere bloccati se cambiano le circostanze.
   > 
   > 
6. Configurare i criteri per ogni API Web. I criteri determinano gli aspetti, ad esempio se devono essere consentite chiamate tra domini, le modalità di autenticazione dei client, se convertire i formati di dati tra JSON e XML in modo trasparente, se si desidera limitare le chiamate da un intervallo IP specificato, le quote di utilizzo e se si desidera limitare la frequenza di chiamata. I criteri possono essere applicati a livello globale sull’intero prodotto, per una singola API Web in un prodotto o per singole operazioni in un API Web.

Ulteriori informazioni che descrivono come eseguire queste attività sono reperibili alla pagina [Gestione API](https://azure.microsoft.com/services/api-management/) del sito Web Microsoft. Il Servizio di gestione API di Azure fornisce inoltre la propria interfaccia REST, consentendo di creare un'interfaccia personalizzata per semplificare il processo di configurazione di un'API Web. Per ulteriori informazioni, visitare la pagina [Riferimento API REST alla gestione API di Azure](https://msdn.microsoft.com/library/azure/dn776326.aspx) del sito Web Microsoft.

> [!TIP]
> Azure offre la Gestione traffico di Azure che consente di implementare il failover e il bilanciamento del carico e ridurre la latenza tra più istanze di un sito Web ospitato in aree geografiche diverse. È possibile utilizzare la Gestione traffico di Azure in combinazione con il Servizio di gestione API. il Servizio di gestione API può indirizzare le richieste a istanze di un sito Web tramite la Gestione traffico di Azure. Per altre informazioni, visitare la pagina [Metodi di routing di Gestione traffico](traffic-manager/traffic-manager-routing-methods.md) sul sito Web Microsoft.
> 
> In questa struttura, se si utilizzano nomi DNS personalizzati per i siti Web, è necessario configurare il record CNAME appropriato per ogni sito Web per indicare il nome DNS del sito web di Gestione traffico di Azure.
> 
> 

## Supporto di sviluppatori che creano applicazioni client
Gli sviluppatori che creano applicazioni client in genere richiedono informazioni su come accedere alle API Web e la documentazione relativa a parametri, tipi di dati, tipi restituiti e codici restituiti che descrivono le diverse richieste e risposte tra il servizio Web e l'applicazione client.

### Documentare le operazioni REST per un'API Web
Il Servizio di gestione API di Azure include un portale per sviluppatori che descrive le operazioni REST esposte da un'API Web. Quando un prodotto è stato pubblicato viene visualizzato nel portale. Gli sviluppatori possono utilizzare il portale di registrazione per l'accesso; l'amministratore può quindi approvare o rifiutare la richiesta. Se lo sviluppatore viene approvato, gli viene assegnata una chiave di sottoscrizione che viene usata per autenticare le chiamate dalle applicazioni client che sviluppa. Questa chiave deve essere fornita con ogni chiamata di API Web; in caso contrario, verrà rifiutata.

Questo portale fornisce inoltre:

* Documentazione del prodotto, elenco delle operazioni che espone, i parametri richiesti e le diverse risposte che possono essere restituite. Si noti che queste informazioni vengono generate dai dettagli forniti nel passaggio 3 nell'elenco nella sezione [Pubblicazione di un'API web tramite il Servizio di gestione API di Microsoft Azure](#publishing-a-web-API).
* Frammenti di codice mostrano come richiamare le operazioni da diversi linguaggi, tra cui JavaScript, C#, Java, Ruby, Python e PHP.
* Console degli sviluppatori che consente agli sviluppatori di inviare una richiesta HTTP per testare ogni operazione nel prodotto e visualizzare i risultati.
* Una pagina in cui lo sviluppatore può segnalare ogni problema rilevato.

Il portale di gestione di Azure consente di personalizzare il portale per sviluppatori per modificare lo stile e il layout in modo che corrisponda alla personalizzazione dell'organizzazione.

### Implementazione di un client SDK
Creare un'applicazione client che richiama le richieste REST per accedere a un’API Web richiede la scrittura di una quantità significativa di codice per creare ogni richiesta e formattarla in modo appropriato, inviare la richiesta al server che ospita il servizio Web e analizzare la risposta da elaborare se la richiesta ha avuto esito positivo o negativo ed estrarre i dati restituiti. Per isolare l'applicazione client da questi problemi, è possibile fornire un SDK che racchiude l'interfaccia REST e consente di astrarre i dettagli di basso livello all'interno di un set di metodi più funzionale. Un'applicazione client utilizza questi metodi, che consentono di convertire in modo trasparente le chiamate in richieste REST e quindi di convertire le risposte in valori restituiti dal metodo. Si tratta di una tecnica comune che viene implementata da molti servizi, tra cui Azure SDK.

La creazione di un SDK lato client è un'impresa considerevole poiché deve essere implementata in modo coerente e testata con attenzione. Tuttavia, gran parte di questo processo può essere resa meccanica e molti fornitori forniscono strumenti in grado di automatizzare molte di queste attività.

## Monitoraggio di un'API Web
A seconda della modalità di pubblicazione e distribuzione dell’API Web, è possibile monitorare l'API Web direttamente oppure è possibile raccogliere informazioni di utilizzo e integrità analizzando il traffico che passa attraverso il Servizio di gestione API.

### Monitoraggio diretto di un'API Web
Se l’API Web è stata implementata utilizzando il modello API Web ASP.NET (in un progetto di API Web o come ruolo Web in un servizio cloud di Azure) e Visual Studio 2013, è possibile raccogliere dati di disponibilità, prestazioni e utilizzo mediante ASP.NET Application Insights. Application Insights è un pacchetto che traccia in modo trasparente e registra le informazioni sulle richieste e risposte quando l'API Web viene distribuita nel cloud; una volta che il pacchetto viene installato e configurato, non è necessario modificare il codice presente in API Web per utilizzarlo. Quando si distribuisce l'API Web in un sito Web di Azure, tutto il traffico viene esaminato e vengono raccolte le statistiche seguenti:

* Tempo di risposta del server.
* Numero di richieste del server e i dettagli di ogni richiesta.
* Le richieste più lente in termini di tempo medio di risposta.
* I dettagli delle richieste non riuscite.
* Il numero di sessioni avviate da browser diversi e gli agenti utente.
* Le pagine visualizzate più frequentemente (utile soprattutto per le applicazioni Web anziché le API Web).
* I diversi ruoli utente di accesso all'API Web.

È possibile visualizzare questi dati in tempo reale dal portale di gestione di Azure. È inoltre possibile creare un test Web per monitorare l'integrità dell'API Web. Un test Web invia una richiesta periodica a un URI specificato nell'API Web e acquisisce la risposta. È possibile specificare la definizione di una risposta corretta (ad esempio, il codice di stato HTTP 200) e se la richiesta non restituisce questa risposta è possibile disporre di un avviso da inviare a un amministratore. Se necessario, l'amministratore può riavviare il server che ospita l'API Web se l’esito è negativo.

Altre informazioni sono disponibili nella pagina [Application Insights: introduzione ad ASP.NET](application-insights/app-insights-asp-net.md) del sito Web Microsoft.

### Monitoraggio di un'API Web tramite il Servizio di gestione API
Se l'API Web è stata pubblicata mediante il Servizio di gestione API, la pagina di gestione API nel portale di gestione di Azure contiene un dashboard che consente di visualizzare le prestazioni complessive del servizio. La pagina di analisi consente di eseguire il drill-down nei dettagli dell'utilizzo del prodotto. Questa pagina contiene le schede seguenti:

* **Utilizzo**. Questa scheda fornisce informazioni sul numero di chiamate API effettuate e la larghezza di banda utilizzata per gestire queste chiamate nel tempo. È possibile filtrare i dettagli di utilizzo per prodotto, API e operazione.
* **Integrità**. Questa scheda consente di visualizzare il risultato di richieste di API (codici di stato HTTP restituiti), l'efficacia dei criteri di memorizzazione nella cache, il tempo di risposta dell’API e il tempo di risposta del servizio. È possibile filtrare di nuovo i dettagli di integrità per prodotto, API e operazione.
* **Attività**. Questa scheda fornisce un riepilogo del testo dei numeri di chiamate completate, non riuscite, bloccate, tempo medio di risposta e tempi di risposta per ogni prodotto, API Web e operazione. Questa pagina elenca anche il numero di chiamate effettuate da ogni sviluppatore.
* **Riepilogo**. Questa scheda visualizza un riepilogo dei dati sulle prestazioni, tra cui gli sviluppatori responsabili del maggior numero di chiamate API, e i prodotti, API Web e le operazioni che hanno ricevuto queste chiamate.

È possibile utilizzare queste informazioni per determinare se una determinata API Web o un'operazione causa un collo di bottiglia e, se necessario, ridurre l’ambiente host e aggiungere più server. È inoltre possibile verificare se una o più applicazioni utilizzano una quantità sproporzionata di risorse e applicano i criteri appropriati per impostare le quote e limitare la frequenza delle chiamate.

> [!NOTE]
> È possibile modificare i dettagli di un prodotto pubblicato e le modifiche vengono applicate immediatamente. Ad esempio, è possibile aggiungere o rimuovere un’operazione da un’API Web senza l’obbligo di pubblicare di nuovo il prodotto che contiene l’API Web.
> 
> 

## Modelli correlati
* Il modello di [interfaccia](http://en.wikipedia.org/wiki/Facade_pattern) descrive come fornire un'interfaccia a un'API Web.

## Altre informazioni
* La pagina [informazioni su API Web ASP.NET](http://www.asp.net/web-api) sul sito Web Microsoft fornisce un'introduzione dettagliata alla creazione di servizi Web RESTful con l'API Web.
* La pagina [Routing in API Web ASP.NET](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) sul sito Web Microsoft descrive il funzionamento del routing basato su convenzione nel framework API Web ASP.NET.
* Per ulteriori informazioni sul routing basato su convenzioni, vedere la pagina [Routing mediante attributi n API Web 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) del sito Web Microsoft.
* La pagina [Esercitazione di base](http://www.odata.org/getting-started/basic-tutorial/) sul sito Web OData fornisce un'introduzione alle funzionalità del protocollo OData.
* La pagina[API Web ASP.NET OData](http://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api)sul sito Web Microsoft contiene esempi e ulteriori informazioni sull'implementazione di un'API web di OData tramite ASP.NET.
* La pagina [Introdurre il supporto Batch in API Web e API Web OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) sul sito Web Microsoft descrive come implementare le operazioni batch in un'API Web tramite OData.
* L'articolo [Modelli di idempotenza](http://blog.jonathanoliver.com/idempotency-patterns/) sul blog di Oliver Jonathan fornisce una panoramica sull’idempotenza e sul modo in cui è correlata alle operazioni di gestione dati.
* La pagina [Definizioni dei codici di stato](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)sul sito Web W3C contiene un elenco completo dei codici di stato e le relative descrizioni.
* Per informazioni dettagliate sulla gestione delle eccezioni HTTP con l'API Web ASP.NET, visitare la pagina [Gestione delle eccezione in API Web ASP.NET](http://www.asp.net/web-api/overview/error-handling/exception-handling) sul sito Web Microsoft.
* L'articolo [Gestione globale degli errori API Web](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) sul sito Web Microsoft descrive come implementare una gestione degli errori globale e una strategia di registrazione per un'API Web.
* La pagina [Eseguire attività in background con processi Web](app-service-web/web-sites-create-web-jobs.md) sul sito Web Microsoft fornisce informazioni ed esempi sull'uso di Processi Web per eseguire operazioni in background su un sito Web di Azure.
* La pagina [Azure Notification hub di notifica utenti](notification-hubs/notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) sul sito Web Microsoft mostra come è possibile utilizzare un Hub di notifica di Azure per spingere le risposte asincrone alle applicazioni client.
* La pagina[Gestione API](https://azure.microsoft.com/services/api-management/) sul sito Web Microsoft descrive come pubblicare un prodotto che fornisce un accesso sicuro e controllato a un'API Web.
* La pagina[Riferimento API REST gestione API di Azure](https://msdn.microsoft.com/library/azure/dn776326.aspx) sul sito Web Microsoft descrive come utilizzare l'API REST di gestione per creare applicazioni di gestione personalizzate.
* La pagina [Metodi di routing di Gestione traffico](traffic-manager/traffic-manager-routing-methods.md) sul sito Web Microsoft riepiloga come Gestione traffico di Azure può essere usato per le richieste di bilanciamento del carico tra più istanze di un sito Web che ospita un'API Web.
* La pagina [Application Insights: introduzione ad ASP.NET](application-insights/app-insights-asp-net.md) del sito Web Microsoft fornisce informazioni dettagliate sull'installazione e sulla configurazione di Application Insights in un progetto Web API.
* La pagina [Verifica codice utilizzando Unit test ](https://msdn.microsoft.com/library/dd264975.aspx) sul sito Web Microsoft fornisce informazioni dettagliate sulla creazione e gestione di unit test utilizzando Visual Studio.
* La pagina [Eseguire test delle prestazioni in un'applicazione prima del rilascio](https://msdn.microsoft.com/library/dn250793.aspx) sul sito Web Microsoft descrive come utilizzare Visual Studio Ultimate per creare un prestazioni Web e caricare il progetto di test.

<!---HONumber=AcomDC_0907_2016-->