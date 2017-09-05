---
title: 'Gestione delle eccezioni: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
description: soluzioni di prevenzione per le minacce esposte in Threat Modeling Tool
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 1be54552d236e1808116d9ff22eda793af920617
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---

# <a name="security-frame-exception-management--mitigations"></a>Infrastruttura di sicurezza: gestione delle eccezioni | soluzioni di prevenzione 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - non includere il nodo serviceDebug nel file di configurazione](#servicedebug)</li><li>[WCF - non includere il nodo serviceMetadata nel file di configurazione](#servicemetadata)</li></ul> |
| **API Web** | <ul><li>[Assicurare una gestione appropriata delle eccezioni in API Web ASP.NET](#exception)</li></ul> |
| **Applicazione Web** | <ul><li>[Non esporre informazioni di sicurezza nei messaggi di errore](#messages)</li><li>[Implementare la pagina di gestione degli errori predefiniti ](#default)</li><li>[Impostare il metodo di distribuzione al dettaglio in IIS](#deployment)</li><li>[Le eccezioni devono avere esito negativo in modo sicuro](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF - non includere il nodo serviceDebug nel file di configurazione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, .NET Framework 3 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passaggi** | I servizi Windows Communication Framework (WCF) possono essere configurati per esporre le informazioni di debug. Le informazioni di debug non devono essere usate in ambienti di produzione. Il tag `<serviceDebug>` definisce se è abilitata la funzionalità di informazioni di debug per un servizio WCF. Se l'attributo includeExceptionDetailInFaults è impostato su true, le informazioni di eccezione dell'applicazione saranno restituite ai client. Gli utenti malintenzionati possono sfruttare le informazioni aggiuntive che acquisiscono dall'output di debug per sferrare attacchi su framework, database o altre risorse usate dall'applicazione. |

### <a name="example"></a>Esempio
Il file di configurazione seguente include il tag `<serviceDebug>`: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Disabilitare le informazioni di debug nel servizio. È possibile eseguire questa operazione rimuovendo il tag `<serviceDebug>` dal file di configurazione dell'applicazione. 

## <a id="servicemetadata"></a>WCF - non includere il nodo serviceMetadata nel file di configurazione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Generico, .NET Framework 3 |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passaggi** | Esponendo pubblicamente informazioni su un servizio è possibile consentire agli hacker di comprendere in che modo possono sfruttare il servizio stesso. Il tag `<serviceMetadata>` abilita la funzionalità di pubblicazione dei metadati. I metadati del servizio potrebbero contenere informazioni riservate che non devono essere accessibili pubblicamente. Come minimo, consentire solo agli utenti attendibili di accedere ai metadati e assicurarsi che le informazioni non necessarie non siano esposte. Ancora meglio, disabilitare completamente la possibilità di pubblicare metadati. Una configurazione di WCF sicura non conterrà il tag `<serviceMetadata>`. |

## <a id="exception"></a>Assicurare una gestione appropriata delle eccezioni in API Web ASP.NET

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Gestione delle eccezioni in API Web ASP.NET](http://www.asp.net/web-api/overview/error-handling/exception-handling), [convalida del modello in API Web ASP.NET](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passaggi** | Per impostazione predefinita, la maggior parte delle eccezioni non rilevate in API Web ASP.NET vengono convertite in una risposta HTTP con codice di stato `500, Internal Server Error`|

### <a name="example"></a>Esempio
Per controllare il codice di stato restituito dall'API, è possibile usare `HttpResponseException` come illustrato di seguito: 
```C#
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Esempio
Per controllare ulteriormente la risposta di eccezione, è possibile usare la classe `HttpResponseMessage` come illustrato di seguito: 
```C#
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Per intercettare le eccezioni non gestite che non sono del tipo `HttpResponseException`, è possibile usare filtri eccezioni. I filtri eccezioni implementano l'interfaccia `System.Web.Http.Filters.IExceptionFilter`. Il modo più semplice per scrivere un filtro eccezioni è derivare la classe `System.Web.Http.Filters.ExceptionFilterAttribute` ed eseguire l'override del metodo OnException. 

### <a name="example"></a>Esempio
Ecco un filtro che converte le eccezioni `NotImplementedException` nel codice di stato HTTP `501, Not Implemented`: 
```C#
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Esistono diversi modi per registrare un filtro eccezioni API Web:
- Tramite un'azione
- Tramite un controller
- A livello globale

### <a name="example"></a>Esempio
Per applicare il filtro a un'azione specifica, aggiungere il filtro come attributo per l'azione: 
```C#
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Esempio
Per applicare il filtro a tutte le azioni in un `controller`, aggiungere il filtro come attributo per la classe `controller`: 

```C#
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Esempio
Per applicare il filtro a livello globale per tutti i controller API Web, aggiungere un'istanza del filtro alla raccolta `GlobalConfiguration.Configuration.Filters`. I filtri eccezioni in questa raccolta si applicano a qualsiasi azione del controller API Web. 
```C#
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Esempio
Per la convalida del modello, lo stato del modello può essere passato al metodo CreateErrorResponse come illustrato di seguito: 
```C#
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Controllare i collegamenti nella sezione Riferimenti per altre informazioni sulla gestione delle eccezioni e la convalida del modello in API Web ASP.Net 

## <a id="messages"></a>Non esporre informazioni di sicurezza nei messaggi di errore

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>I messaggi di errore generici vengono forniti direttamente all'utente senza includere dati sensibili dell'applicazione. Esempi di dati sensibili:</p><ul><li>Nomi dei server</li><li>Stringhe di connessione</li><li>Nomi utente</li><li>Password</li><li>Procedure SQL</li><li>Dettagli di errori SQL dinamici</li><li>Analisi dello stack e righe di codice</li><li>Variabili archiviate in memoria</li><li>Percorsi di unità e cartelle</li><li>Punti di installazione dell'applicazione</li><li>Impostazioni di configurazione dell'host</li><li>Altri dettagli di un'applicazione interna</li></ul><p>Intercettando gli errori all'interno di un'applicazione e presentando messaggi di errore generici, nonché abilitando gli errori personalizzati all'interno di IIS, è possibile evitare la divulgazione di informazioni. La gestione delle eccezioni del database di SQL Server e .NET, tra le altre architetture, di gestione degli errori, è particolarmente dettagliata ed estremamente utile a un utente malintenzionato che esegue la profilatura dell'applicazione. Non visualizzare direttamente il contenuto di una classe derivata dalla classe di eccezione .NET e assicurarsi di disporre della gestione delle eccezioni appropriata in modo che un'eccezione imprevista non venga generata direttamente a disposizione dell'utente.</p><ul><li>Fornire messaggi di errore generici direttamente all'utente che omettono dettagli specifici rilevati direttamente nel messaggio di eccezione o errore</li><li>Non consentire direttamente all'utente di visualizzare il contenuto di una classe di eccezione .NET</li><li>Intercettare tutti i messaggi di errore e se necessario informare l'utente tramite un messaggio di errore generico inviato al client dell'applicazione</li><li>Non esporre il contenuto della classe di eccezione direttamente all'utente, in particolare il valore restituito da `.ToString()`, oppure i valori delle proprietà del messaggio o dell'analisi dello stack. Registrare in modo sicuro queste informazioni e mostrare un messaggio più innocuo all'utente</li></ul>|

## <a id="default"></a>Implementare la pagina di gestione degli errori predefiniti

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Modificare la finestra di dialogo delle impostazioni pagine di errore ASP.NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Passaggi** | <p>Quando un'applicazione ASP.NET ha esito negativo e causa un errore Server interno HTTP/1.x 500 o una configurazione di funzionalità (ad esempio, il filtro richieste) impedisce la visualizzazione di una pagina, verrà generato un messaggio di errore. Gli amministratori possono scegliere se nell'applicazione viene visualizzato un messaggio descrittivo per il client, il messaggio di errore dettagliato per il client o il messaggio di errore dettagliato solo a localhost. Il tag <customErrors> in web.config ha tre modalità:</p><ul><li>**On:** specifica che gli errori personalizzati sono attivati. Se non viene specificato alcun attributo defaultRedirect, gli utenti visualizzato un errore generico. Gli errori personalizzati vengono visualizzati sui client remoti e sull'host locale</li><li>**Off:** specifica che gli errori personalizzati sono disattivati. Gli errori ASP.NET dettagliati vengono visualizzati sui client remoti e sull'host locale</li><li>**RemoteOnly:** specifica che gli errori personalizzati vengono visualizzati solo sui client remoti e gli errori ASP.NET vengono visualizzati sull'host locale. Si tratta del valore predefinito</li></ul><p>Aprire il file `web.config` per il sito/applicazione e assicurarsi che per il tag sia definito `<customErrors mode="RemoteOnly" />` o `<customErrors mode="On" />`.</p>|

## <a id="deployment"></a>Impostare il metodo di distribuzione al dettaglio in IIS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Element distribuzione (schema impostazioni ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Passaggi** | <p>Lo switch `<deployment retail>` deve essere usato dai server IIS di produzione. Questo switch viene usato per gestire applicazioni con le migliori prestazioni possibili e minime fughe di informazioni di sicurezza disabilitando la capacità dell'applicazione per generare l'output di traccia in una pagina, disattivando la possibilità di visualizzare messaggi di errore dettagliati per gli utenti finali e disattivando l'opzione di debug.</p><p>Spesso, gli switch e le opzioni che sono destinati agli sviluppatori, ad esempio traccia delle richieste no riuscite e debug, sono abilitati durante lo sviluppo attivo. È consigliabile che il metodo di distribuzione in qualsiasi server di produzione sia impostato su vendita al dettaglio. aprire il file machine.config e assicurarsi che `<deployment retail="true" />` resti impostato su true.</p>|

## <a id="fail"></a>Le eccezioni devono avere esito negativo in modo sicuro

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Esito negativo in modo sicuro](https://www.owasp.org/index.php/Fail_securely) |
| **Passaggi** | L'applicazione deve avere esito negativo in modo sicuro. Per qualsiasi metodo che restituisce un valore booleano, in base al quale vengono prese determinate decisioni, è necessario creare con attenzione un blocco delle eccezioni. Esistono molti errori logici che causano problemi di sicurezza quando il blocco delle eccezioni è scritto senza fare attenzione.|

### <a name="example"></a>Esempio
```C#
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
Il metodo sopra indicato restituirà sempre True, se si verificano alcune eccezioni. Se l'utente finale fornisce un URL in formato non valido, che il browser rispetta, ma il costruttore `Uri()` no, verrà generata un'eccezione e la vittima riuscirà ad accedere all'URL valido, ma in formato non valido. 
