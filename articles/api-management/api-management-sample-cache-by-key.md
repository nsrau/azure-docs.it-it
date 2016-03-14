<properties
	pageTitle="Memorizzazione nella cache personalizzata in Gestione API di Azure"
	description="Informazioni su come memorizzare elementi nella cache in base alla chiave in Gestione API di Azure"
	services="api-management"
	documentationCenter=""
	authors="darrelmiller"
	manager=""
	editor=""/>

<tags
	ms.service="api-management"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/19/2016"
	ms.author="v-darmi"/>

# Memorizzazione nella cache personalizzata in Gestione API di Azure
Il servizio Gestione API di Azure prevede il supporto incorporato per la [memorizzazione nella cache delle risposte HTTP](api-management-howto-cache.md) usando l'URL della risorsa come chiave. La chiave può essere modificata dalle intestazioni della richiesta usando le proprietà `vary-by`. Questo risulta utile per la memorizzazione nella cache di intere risposte HTTP (note anche come rappresentazioni), ma talvolta è utile memorizzare nella cache anche solo una parte di una rappresentazione. I nuovi criteri [cache-lookup-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) e [cache-store-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) consentono di archiviare e recuperare singoli dati arbitrari all'interno di definizioni dei criteri. Questa possibilità migliora anche il criterio [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) introdotto in precedenza, dal momento che ora è possibile memorizzare nella cache le risposte provenienti da servizi esterni.

## Architettura  
Il servizio Gestione API usa una cache di dati condivisa per tenant. In questo modo, man mano che aumenta il numero di unità, è sempre possibile accedere agli stessi dati memorizzati nella cache. Quando si adotta una distribuzione in più aree, tuttavia, sono presenti cache indipendenti all'interno di ogni area. Per questo motivo, è importante non considerare la cache come un archivio dati e l'unica fonte di alcune informazioni. Così facendo, se successivamente si decide di usare la distribuzione in più aree, i clienti con utenti che viaggiano posso perdere l'accesso ai dati memorizzati nella cache.

## Memorizzazione di frammenti
Esistono casi in cui le risposte restituite contengono una parte di dati costosa da determinare ma che rimane aggiornata per un periodo di tempo ragionevole. Si consideri, ad esempio, un servizio creato da una compagnia aerea che fornisce informazioni sulla prenotazione dei voli, lo stato dei voli e così via. Se l'utente è membro del programma a punti della compagnia aerea, dovrà anche avere informazioni sul proprio stato e sulle miglia accumulate. Le informazioni relative agli utenti possono essere archiviate in un sistema diverso, ma potrebbe essere utile includerle nelle risposte restituite con le informazioni sulle prenotazioni e lo stato dei voli. Questa operazione può essere eseguita attraverso un processo denominato memorizzazione di frammenti. La rappresentazione primaria può essere restituita dal server di origine usando un token per indicare la posizione per l'inserimento delle informazioni relative agli utenti.

Si consideri la seguente risposta JSON da un'API back-end.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

E la risorsa secondaria `/userprofile/{userid}` che presenta un aspetto simile al seguente.

     { "username" : "Bob Smith", "Status" : "Gold" }

Per determinare quali informazioni dell'utente includere, è necessario identificare l'utente finale. Questo meccanismo dipende dall'implementazione. Nell'esempio viene usata l'attestazione `Subject` di un token `JWT`.

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Il valore `enduserid` viene memorizzato in una variabile di contesto per usarlo in seguito. Il passaggio successivo consiste nel determinare se una richiesta precedente ha già recuperato le informazioni utente e le ha archiviate nella cache. A tale scopo viene usato il criterio `cache-lookup-value`.

	  <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

Se non sono presenti voci nella cache che corrispondono al valore della chiave, non viene creata alcuna variabile di contesto `userprofile`. Per verificare l'esito della ricerca viene usato il criterio del flusso di controllo `choose`.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Se la variabile di contesto `userprofile` non esiste, sarà necessario eseguire una richiesta HTTP per recuperarla.

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

Per creare l'URL per la risorsa del profilo utente si usa `enduserid`. Dopo aver ottenuto la risposta, è possibile estrarre il corpo della risposta dalla risposta e archiviarlo nuovamente in una variabile di contesto.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Per evitare di dover eseguire nuovamente la richiesta HTTP quando lo stesso utente esegue un'altra richiesta, è possibile archiviare il profilo utente nella cache.

	<cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Il valore viene archiviato nella cache usando esattamente la stessa chiave con cui in origine si è cercato di recuperarlo. La durata scelta per l'archiviazione del valore deve essere basata sulla frequenza di modifica delle informazioni e sulla tolleranza degli utenti rispetto alle informazioni non aggiornate.

È importante tenere presente che il recupero dalla cache è ancora una richiesta di rete out-of-process, che può aggiungere decine di millisecondi della richiesta. I vantaggi sono evidenti quando per determinare le informazioni relative al profilo utente è necessario molto più tempo a causa della necessità di eseguire query di database o aggregare le informazioni da più sistemi back-end.

Il passaggio finale del processo consiste nell'aggiornare la risposta restituita con le informazioni sul profilo utente.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

Si è scelto di includere le virgolette come parte del token in modo che anche quando non si verifica la sostituzione, la risposta rimane un oggetto JSON valido. Lo scopo principale era semplificare il debug.

Quando si combinano insieme tutti questi passaggi, il risultato finale è un criterio simile a quello riportato di seguito.

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

    		<!-- If we don’t find it in the cache, make a request for it and store it -->
    		<choose>
    			<when condition="@(!context.Variables.ContainsKey("userprofile"))">
    				<!—Make HTTP request to get user profile -->
    				<send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
    					<set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
    					<set-method>GET</set-method>
    				</send-request>

    				<!—Store response body in context variable -->
    				<set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

    				<!—Store result in cache -->
    				<cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
    			</when>
    		</choose>
    		<base />
    	</inbound>
    	<outbound>
    		<!—Update response body with user profile-->
    		<find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
    		<base />
    	</outbound>
     </policies>

Questo approccio alla memorizzazione nella cache viene usato principalmente in siti web in cui il codice HTML viene composto sul lato server in modo che sia possibile eseguirne il rendering come una singola pagina. Tuttavia, può risultare utile anche per le API in cui i client non possono eseguire la memorizzazione nella cache del codice HTTP sul lato client o non è consigliabile affidare tale compito al client.

Questo stesso tipo di memorizzazione di frammenti può essere eseguito anche nei server Web back-end usando un server di memorizzazione nella cache Redis. Tuttavia, usare il servizio Gestione API per eseguire questa operazione può risultare utile quando i frammenti memorizzati nella cache provengono da back-end diversi rispetto alle risposte primarie.

## Controllo delle versioni trasparente
Solitamente sono supportate più versioni diverse dell'implementazione di un'API in qualsiasi momento. Ciò è dovuto probabilmente alla necessità di supportare ambienti diversi (ad esempio, ambienti di sviluppo, test, produzione e così via), oppure alla necessità di supportare versioni precedenti dell'API e consentire ai consumer dell'API di eseguire la migrazione a versioni più recenti.

Anziché richiedere agli sviluppatori di client di modificare l'URL da `/v1/customers` in `/v2/customers`, un possibile approccio alla gestione di questa situazione consiste nell'archiviare tra i dati del profilo del consumer la versione dell'API da usare e chiamare l'URL del back-end appropriato. Per determinare l'URL del back-end corretto da chiamare per un determinato client, è necessario eseguire una query su alcuni dati di configurazione. Con la memorizzazione nella cache di questi dati di configurazione, è possibile ridurre al minimo i problemi di prestazioni correlati alla ricerca.

Il primo passaggio consiste nel determinare l'identificatore usato per configurare la versione scelta. In questo esempio, si è scelto di associare la versione alla chiave della sottoscrizione del prodotto.

		<set-variable name="clientid" value="@(context.Subscription.Key)" />

Viene quindi eseguita una ricerca nella cache per verificare se è già stata recuperata la versione del client scelta.

		<cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

Il codice seguente permette di verificare se è presente nella cache.

	<choose>
		<when condition="@(!context.Variables.ContainsKey("clientversion"))">

Se non è presente, è possibile recuperarla.

	<send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
        		<set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
        		<set-method>GET</set-method>
	</send-request>

Estrarre il corpo della risposta dalla risposta.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Memorizzare nuovamente l'informazione nella cache per usarla in futuro.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

Infine, aggiornare l'URL del back-end per selezionare la versione del servizio scelta dal client.

	<set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

Il criterio completo sarà il seguente.

	 <inbound>
		<base />
		<set-variable name="clientid" value="@(context.Subscription.Key)" />
		<cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

		<!-- If we don’t find it in the cache, make a request for it and store it -->
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


Consentire ai consumer delle API il controllo trasparente della versione del back-end accessibile dai client senza dover aggiornare e ridistribuire i client è una soluzione elegante che risolve diversi problemi di controllo delle versioni delle API.

## Isolamento dei tenant

Nelle distribuzioni multi-tenant di grandi dimensioni alcune aziende creano gruppi di tenant separati in distribuzioni distinte dell'hardware back-end. Questo approccio permette di ridurre al minimo il numero di clienti interessati da un problema hardware nel back-end. Permette anche la distribuzione in più fasi delle nuove versioni dei software. È preferibile che questa architettura di back-end sia trasparente per i consumer dell'API. Questo approccio può essere realizzato in modo analogo al controllo delle versioni trasparente perché si basa sulla stessa tecnica di manipolazione dell'URL del back-end usando lo stato di configurazione per chiave dell'API.

Invece di restituire una versione preferita dell'API per ogni chiave della sottoscrizione, viene restituito un identificatore che mette in relazione un tenant con il gruppo di componenti hardware assegnato. L'identificatore può essere usato per creare l'URL del back-end appropriato.

## Riepilogo
La libertà di usare la cache di Gestione API di Azure per archiviare qualsiasi tipo di dati consente di accedere facilmente ai dati di configurazione che possono influenzare la modalità di elaborazione di una richiesta in ingresso. La cache può anche essere usata per memorizzare frammenti di dati che possono integrare le risposte restituite da un'API back-end.

## Passaggi successivi
Se questi criteri hanno permesso di abilitare altri scenari o se si vuole realizzare scenari non ancora disponibili, è possibile lasciare i propri commenti e suggerimenti nel thread Disqus di questo argomento.

<!---HONumber=AcomDC_0302_2016-->