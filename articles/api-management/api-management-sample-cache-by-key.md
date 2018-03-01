---
title: Memorizzazione nella cache personalizzata in Gestione API di Azure
description: Informazioni su come memorizzare elementi nella cache in base alla chiave in Gestione API di Azure
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 838850d38c9df51fabcf620831371bed401e9492
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="custom-caching-in-azure-api-management"></a>Memorizzazione nella cache personalizzata in Gestione API di Azure
Il servizio Gestione API di Azure prevede il supporto incorporato per la [memorizzazione nella cache delle risposte HTTP](api-management-howto-cache.md) usando l'URL della risorsa come chiave. La chiave può essere modificata dalle intestazioni della richiesta usando le proprietà `vary-by`. Questo risulta utile per la memorizzazione nella cache di intere risposte HTTP (note anche come rappresentazioni), ma talvolta è utile memorizzare nella cache anche solo una parte di una rappresentazione. I nuovi criteri [cache-lookup-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) e [cache-store-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) consentono di archiviare e recuperare singoli dati arbitrari all'interno di definizioni dei criteri. Questa possibilità migliora anche il criterio [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) introdotto in precedenza, dal momento che ora è possibile memorizzare nella cache le risposte provenienti da servizi esterni.

## <a name="architecture"></a>Architecture
Il servizio Gestione API usa una cache di dati condivisa per tenant. In questo modo, man mano che aumenta il numero di unità, è sempre possibile accedere agli stessi dati memorizzati nella cache. Quando si adotta una distribuzione in più aree, tuttavia, sono presenti cache indipendenti all'interno di ogni area. È importante non considerare la cache come un archivio dati e l'unica fonte di alcune informazioni. Così facendo, se successivamente si decide di usare la distribuzione in più aree, i clienti con utenti che viaggiano posso perdere l'accesso ai dati memorizzati nella cache.

## <a name="fragment-caching"></a>Memorizzazione di frammenti
Esistono casi in cui le risposte restituite contengono una parte di dati costosa da determinare ma che rimane aggiornata per un periodo di tempo ragionevole. Si consideri, ad esempio, un servizio creato da una compagnia aerea che fornisce informazioni sulla prenotazione dei voli, lo stato dei voli e così via. Se l'utente è membro del programma a punti della compagnia aerea, dovrà anche avere informazioni sul proprio stato e sulle miglia accumulate. Le informazioni relative agli utenti possono essere archiviate in un sistema diverso, ma potrebbe essere utile includerle nelle risposte restituite con le informazioni sulle prenotazioni e lo stato dei voli. Questa operazione può essere eseguita attraverso un processo denominato memorizzazione di frammenti. La rappresentazione primaria può essere restituita dal server di origine usando un token per indicare la posizione per l'inserimento delle informazioni relative agli utenti. 

Si consideri la seguente risposta JSON da un'API back-end.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

E la risorsa secondaria `/userprofile/{userid}` che presenta un aspetto simile al seguente.

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Per determinare quali informazioni dell'utente includere, Gestione API deve identificare l'utente finale. Questo meccanismo dipende dall'implementazione. Nell'esempio viene usata l'attestazione `Subject` di un token `JWT`. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Gestione API archivia il valore `enduserid` in una variabile di contesto per usarlo in seguito. Il passaggio successivo consiste nel determinare se una richiesta precedente ha già recuperato le informazioni utente e le ha archiviate nella cache. A tale scopo, Gestione API usa i criteri `cache-lookup-value`.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Se non sono presenti voci nella cache che corrispondono al valore della chiave, non viene creata alcuna variabile di contesto `userprofile`. Per verificare l'esito della ricerca, Gestione API usa il criterio del flusso di controllo `choose`.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Se la variabile di contesto `userprofile` non esiste, Gestione API dovrà eseguire una richiesta HTTP per recuperarla.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

Per creare l'URL per la risorsa del profilo utente, Gestione API usa `enduserid`. Dopo aver ottenuto la risposta, Gestione API estrae il corpo della risposta dalla risposta e lo archivia nuovamente in una variabile di contesto.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Per evitare che Gestione API esegua nuovamente la richiesta HTTP quando lo stesso utente esegue un'altra richiesta, è possibile specificare che si intende archiviare il profilo utente nella cache.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Gestione API archivia il valore nella cache usando esattamente la stessa chiave con cui in origine ha cercato di recuperarlo. La durata scelta da Gestione API per l'archiviazione del valore deve essere basata sulla frequenza di modifica delle informazioni e sulla tolleranza degli utenti rispetto alle informazioni non aggiornate. 

È importante tenere presente che il recupero dalla cache è ancora una richiesta di rete out-of-process, che può aggiungere decine di millisecondi della richiesta. I vantaggi sono evidenti quando per determinare le informazioni relative al profilo utente è necessario più tempo dal momento che è necessario eseguire query di database o aggregare le informazioni da più sistemi back-end.

Il passaggio finale del processo consiste nell'aggiornare la risposta restituita con le informazioni sul profilo utente.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

È possibile scegliere di includere le virgolette come parte del token in modo che anche quando non si verifica la sostituzione, la risposta rimane un oggetto JSON valido.  

Quando si combinano insieme tutti questi passaggi, il risultato finale è un criterio simile a quello riportato di seguito.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Questo approccio alla memorizzazione nella cache viene usato principalmente in siti web in cui il codice HTML viene composto sul lato server in modo che sia possibile eseguirne il rendering come una singola pagina. Può risultare utile anche per le API in cui i client non possono eseguire la memorizzazione nella cache del codice HTTP sul lato client o non è consigliabile affidare tale compito al client.

Questo stesso tipo di memorizzazione di frammenti può essere eseguito anche nei server Web back-end usando un server di memorizzazione nella cache Redis. Tuttavia, usare il servizio Gestione API per eseguire questa operazione può risultare utile quando i frammenti memorizzati nella cache provengono da back-end diversi rispetto alle risposte primarie.

## <a name="transparent-versioning"></a>Controllo delle versioni trasparente
Solitamente sono supportate più versioni diverse dell'implementazione di un'API in qualsiasi momento. Questo allo scopo, ad esempio, di supportare ambienti diversi (sviluppo, test, produzione e così via), oppure di supportare versioni precedenti dell'API e consentire ai consumer dell'API di eseguire la migrazione a versioni più recenti. 

Invece di chiedere agli sviluppatori di client di modificare l'URL da `/v1/customers` in `/v2/customers`, un possibile approccio alla gestione di questa situazione consiste nell'archiviare tra i dati del profilo del consumer la versione dell'API da usare e chiamare l'URL del back-end appropriato. Per determinare l'URL del back-end corretto da chiamare per un determinato client, è necessario eseguire una query su alcuni dati di configurazione. Con la memorizzazione nella cache di questi dati di configurazione, Gestione API può ridurre al minimo i problemi di prestazioni correlati alla ricerca.

Il primo passaggio consiste nel determinare l'identificatore usato per configurare la versione scelta. In questo esempio, si è scelto di associare la versione alla chiave della sottoscrizione del prodotto. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Gestione API esegue quindi una ricerca nella cache per verificare se è già stata recuperata la versione del client scelta.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Verifica quindi se non è stata trovata nella cache.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Se la versione non viene trovata, Gestione API la recupera.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Estrarre il corpo della risposta dalla risposta.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Memorizzare nuovamente l'informazione nella cache per usarla in futuro.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

Infine, aggiornare l'URL del back-end per selezionare la versione del servizio scelta dal client.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Il criterio completo è il seguente:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Consentire ai consumer delle API il controllo trasparente della versione del back-end accessibile dai client senza dover aggiornare e ridistribuire i client è una soluzione elegante che risolve diversi problemi di controllo delle versioni delle API.

## <a name="tenant-isolation"></a>Isolamento dei tenant
Nelle distribuzioni multi-tenant di grandi dimensioni alcune aziende creano gruppi di tenant separati in distribuzioni distinte dell'hardware back-end. Questo approccio permette di ridurre al minimo il numero di clienti interessati da un problema hardware nel back-end. Permette anche la distribuzione in più fasi delle nuove versioni dei software. È preferibile che questa architettura di back-end sia trasparente per i consumer dell'API. Questo approccio può essere realizzato in modo analogo al controllo delle versioni trasparente perché si basa sulla stessa tecnica di manipolazione dell'URL del back-end usando lo stato di configurazione per chiave dell'API.  

Invece di restituire una versione preferita dell'API per ogni chiave della sottoscrizione, viene restituito un identificatore che mette in relazione un tenant con il gruppo di componenti hardware assegnato. L'identificatore può essere usato per creare l'URL del back-end appropriato.

## <a name="summary"></a>Riepilogo
La libertà di usare la cache di Gestione API di Azure per archiviare qualsiasi tipo di dati consente di accedere facilmente ai dati di configurazione che possono influenzare la modalità di elaborazione di una richiesta in ingresso. La cache può anche essere usata per memorizzare frammenti di dati che possono integrare le risposte restituite da un'API back-end.
