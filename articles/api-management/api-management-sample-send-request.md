---
title: Uso del servizio Gestione API per generare richieste HTTP
description: Informazioni sull&quot;uso dei criteri di richiesta e risposta in Gestione API per chiamare servizi esterni dall&quot;API
services: api-management
documentationcenter: 
author: darrelmiller
manager: erikre
editor: 
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5bb92f427a07949d6057553ac8dde309e1a0aa11


---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Uso di servizi esterni dal servizio Gestione API di Azure
I criteri disponibili nel servizio Gestione API di Azure possono essere usati per una vasta gamma di attività basate esclusivamente su richieste in ingresso, risposte in uscita e informazioni di configurazione di base. Tuttavia, la possibilità di interagire con i servizi esterni dai criteri di Gestione API offre molte altre opportunità.

In precedenza è stata analizzata l'interazione con il [servizio Hub eventi di Azure per la registrazione, il monitoraggio e l'analisi](api-management-log-to-eventhub-sample.md). In questo articolo verranno descritti i criteri che consentono di interagire con qualsiasi servizio esterno basato su HTTP. Questi criteri possono essere usati per l'attivazione di eventi remoti o per il recupero di informazioni che verranno usate per gestire la richiesta e la risposta originali.

## <a name="send-one-way-request"></a>Send-One-Way-Request
Probabilmente l'interazione esterna più semplice è lo stile fire-and-forget della richiesta, che consente a un servizio esterno di ricevere una notifica se si verifica un evento importante. È possibile usare i criteri del flusso di controllo `choose` per rilevare qualsiasi tipo di condizione di interesse e quindi, se la condizione è soddisfatta, inviare una richiesta HTTP esterna usando i criteri [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) . Può trattarsi di una richiesta a un sistema di messaggistica, ad esempio Hipchat o Slack, o a un'API di posta elettronica, ad esempio SendGrid o MailChimp, o per eventi che richiedono interventi di supporto critico, ad esempio PagerDuty. Tutti questi sistemi di messaggistica dispongono di API HTTP semplici che possono essere richiamate facilmente.

### <a name="alerting-with-slack"></a>Avvisi con Slack
L'esempio seguente illustra come inviare un messaggio a una chat di Slack, se il codice di stato della risposta HTTP è maggiore o uguale a 500. Un errore di intervallo 500 indica un problema con l'API back-end che il client dell'API non può risolvere automaticamente. In genere richiede un intervento da parte dell'utente.  

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>
```

In Slack sono disponibili gli hook Web in ingresso. Quando si configura un hook Web in ingresso, Slack genera un URL specifico che consente di eseguire una semplice richiesta POST e di passare un messaggio nel canale di Slack. Il corpo JSON creato è basato su un formato definito da Slack.

![Hook Web di Slack](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Lo stile di richiesta fire-and-forget è sufficientemente efficace?
Quando si usa uno stile di richiesta fire-and-forget è necessario tenere presenti alcuni svantaggi. Se per qualche motivo la richiesta ha esito negativo, l'errore non viene segnalato. In questo caso, la complessità di disporre di un sistema secondario di segnalazione dell'errore e il costo aggiuntivo delle prestazioni per l'attesa della risposta non sono garantiti. Per gli scenari in cui è fondamentale verificare la risposta, i criteri [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) rappresentano un'opzione migliore.

## <a name="send-request"></a>send-request
I criteri `send-request` consentono l'utilizzo di un servizio esterno per eseguire funzioni di elaborazione complesse e restituire dati al servizio Gestione API, che può essere usato per un'elaborazione successiva dei criteri.

### <a name="authorizing-reference-tokens"></a>Autorizzazione di token di riferimento
Una funzione fondamentale di Gestione API è la protezione delle risorse back-end. Se il server di autorizzazione usato dall'API crea [token JWT](http://jwt.io/) come parte del flusso OAuth2, in modo analogo ad [Azure Active Directory](../active-directory/active-directory-aadconnect.md), è possibile usare i criteri `validate-jwt` per verificare la validità del token. Tuttavia, alcuni server di autorizzazione creano i cosiddetti [token di riferimento](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) , che non possono essere verificati senza eseguire il callback al server di autorizzazione.

### <a name="standardized-introspection"></a>Introspezione standardizzata
In passato non era disponibile alcuna modalità standardizzata per verificare un token di riferimento con un server di autorizzazione. L'IETF, tuttavia, ha recentemente proposto e pubblicato lo standard [RFC 7662](https://tools.ietf.org/html/rfc7662) , che definisce le modalità con cui un server di risorse può verificare la validità di un token.

### <a name="extracting-the-token"></a>Estrazione del token
Il primo passaggio consiste nell'estrarre il token dall'intestazione di autorizzazione. Il valore dell'intestazione deve essere costituito dallo schema di autorizzazione `Bearer` , uno spazio singolo e il token di autorizzazione, in base allo standard [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Purtroppo esistono casi in cui lo schema di autorizzazione viene omesso. Per questo motivo, durante l'analisi il valore dell'intestazione viene suddiviso su uno spazio e viene selezionata l'ultima stringa dalla matrice di stringhe restituita. Questa rappresenta una soluzione alternativa per le intestazioni di autorizzazione con formato non corretto.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Invio della richiesta di convalida
Dopo aver ottenuto il token di autorizzazione, è possibile inviare la richiesta per la convalida del token. Lo standard RFC 7662 definisce questa procedura introspezione e richiede che venga inviata una richiesta `POST` per un modulo HTML alla risorsa di introspezione. Il modulo HTML deve contenere almeno una coppia chiave/valore con la chiave `token`. La richiesta al server di autorizzazione deve essere autenticata anche per assicurarsi che client non autorizzati non vengano passati come token validi.

```xml
<send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
  <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
  <set-method>POST</set-method>
  <set-header name="Authorization" exists-action="override">
    <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
  </set-header>
  <set-header name="Content-Type" exists-action="override">
    <value>application/x-www-form-urlencoded</value>
  </set-header>
  <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
</send-request>
```

### <a name="checking-the-response"></a>Verifica della risposta
L'attributo `response-variable-name` viene usato per concedere l'accesso alla risposta restituita. Il nome definito in questa proprietà può essere usato come chiave nel dizionario `context.Variables` per accedere all'oggetto `IResponse`.

Dall'oggetto della risposta è possibile recuperare il corpo e lo standard RFC 7622 indica che la risposta deve essere un oggetto JSON e deve contenere almeno una proprietà denominata `active` che rappresenta un valore booleano. Se `active` è true, il token è considerato valido.

### <a name="reporting-failure"></a>Creazione di report sull'errore
Per individuare un token non valido, si usano i criteri `<choose>` e, in questo caso, viene restituita una risposta 401.

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>
```

In base allo standard [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3), in cui viene descritto come usare i token `bearer`, viene inoltre restituita un'intestazione `WWW-Authenticate` con la risposta 401. L'intestazione WWW-Authenticate è progettata per indicare a un client come realizzare una richiesta correttamente autorizzata. A causa dell'ampia gamma di approcci possibili con il framework di OAuth2, è difficile comunicare tutte le informazioni necessarie. Fortunatamente, sono disponibili soluzioni che consentono ai [client di definire le modalità per autorizzare correttamente le richieste a un server di risorse](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Soluzione finale
Combinando tutte le parti descritte, si ottengono i criteri seguenti:

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
          <!-- Check active property in response -->
          <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
              <!-- Return 401 Unauthorized with http-problem payload -->
              <return-response response-variable-name="existing response variable">
                  <set-status code="401" reason="Unauthorized" />
                  <set-header name="WWW-Authenticate" exists-action="override">
                      <value>Bearer error="invalid_token"</value>
                  </set-header>
              </return-response>
          </when>
      </choose>
  <base />
</inbound>
```

Questo è solo uno dei numerosi esempi che illustrano come usare i criteri `send-request` per integrare utili servizi esterni nel processo delle richieste e delle risposte che passano attraverso il servizio Gestione API.

## <a name="response-composition"></a>Composizione della risposta
I criteri `send-request` possono essere usati per l'ottimizzazione di una richiesta primaria a un sistema back-end, come illustrato nell'esempio precedente, oppure come sostituzione completa della chiamata al back-end. Questa tecnica consente di creare facilmente risorse composite che vengono aggregate da più sistemi.

### <a name="building-a-dashboard"></a>Creazione di un dashboard
A volte può essere utile saper esporre le informazioni presenti in più sistemi back-end, ad esempio, per creare un dashboard. Gli indicatori KPI provengono da diversi sistemi back-end, ma può essere opportuno evitare di fornire accesso diretto a questi ultimi e può essere utile recuperare tutte le informazioni in un'unica richiesta. Ad esempio, può essere innanzitutto necessario sezionare e ripulire alcune delle informazioni di back-end. La possibilità di memorizzare nella cache tale risorsa complessa può essere utile per ridurre il carico del back-end, dato che gli utenti tendono a premere F5 per verificare se è possibile modificare le prestazioni limitate delle metriche.    

### <a name="faking-the-resource"></a>Simulazione della risorsa
Il primo passaggio per la creazione della risorsa dashboard consiste nella configurazione di una nuova operazione nel portale di pubblicazione di Gestione API. Si tratta di un'operazione di segnaposto usata per configurare i criteri di composizione per creare la risorsa dinamica.

![Operazione dashboard](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Invio delle richieste
Dopo aver creato l'operazione `dashboard` , è possibile configurare criteri specifici per tale operazione. 

![Operazione dashboard](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Il primo passaggio consiste nell'estrarre eventuali parametri di query dalla richiesta in ingresso, in modo da poterli inoltrare al back-end. In questo esempio il dashboard visualizza informazioni basate su un periodo di tempo e dispone quindi dei parametri `fromDate` e `toDate`. È possibile usare i criteri `set-variable` per estrarre le informazioni dall'URL della richiesta.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Quando le informazioni sono disponibili, è possibile inviare le richieste a tutti i sistemi back-end. Ogni richiesta crea un nuovo URL con le informazioni sul parametro, chiama il rispettivo server e archivia la risposta in una variabile di contesto.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Queste richieste vengono eseguite in sequenza, ma non si tratta della situazione ideale. In una versione futura verranno introdotti nuovi criteri denominati `wait` che consentiranno l'esecuzione in parallelo di tutte le richieste.

### <a name="responding"></a>Invio delle risposte
Per costruire la risposta composita è possibile usare i criteri [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) . L'elemento `set-body` può usare un'espressione per creare un nuovo oggetto `JObject` con tutte le rappresentazioni di componenti incorporate come proprietà.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

I criteri completi saranno simili ai seguenti:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

Nella configurazione dell'operazione segnaposto è possibile impostare la risorsa dashboard in modo che venga memorizzata nella cache per almeno un'ora, perché la natura dei dati implica che anche se scaduta, è comunque sufficientemente efficace per fornire informazioni utili agli utenti.

## <a name="summary"></a>Summary
Il servizio Gestione API di Azure offre criteri flessibili che possono essere applicati in modo selettivo al traffico HTTP e consentono la realizzazione di servizi back-end. Se si desidera migliorare il gateway API con funzioni di avviso, verifica e convalida o creare nuove risorse complesse basate su più servizi back-end, `send-request` e i criteri correlati offrono numerose possibilità.

## <a name="watch-a-video-overview-of-these-policies"></a>Video contenente una panoramica di questi criteri
Per altre informazioni sui criteri [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) e [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) presentati in questo articolo, guardare il video seguente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Send-Request-and-Return-Response-Policies/player]
> 
> 




<!--HONumber=Nov16_HO3-->


