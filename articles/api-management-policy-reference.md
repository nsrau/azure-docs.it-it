<properties 
	pageTitle="Riferimento ai criteri di Gestione API di Azure" 
	description="Informazioni sui criteri disponibili per configurare Gestione API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Riferimento ai criteri di Gestione API di Azure

Questo argomento include riferimenti relativi ai criteri seguenti per Gestione API (anteprima). Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API][Criteri di Gestione API].

-   [Criteri per le restrizioni sull'accesso][Criteri per le restrizioni sull'accesso]

    -   [Quota di utilizzo][Quota di utilizzo]: permette di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda.
    -   [Limite di frequenza][Limite di frequenza]: impedisce picchi d'utilizzo delle API tramite la limitazione delle chiamate e/o del tasso di consumo della larghezza di banda.
    -   [Restrizione per l'IP del chiamante][Restrizione per l'IP del chiamante]: filtra (permette/rifiuta) le chiamate da indirizzi IP e/o intervalli di indirizzi IP specifici.
-   [Criteri di trasformazione dei contenuti][Criteri di trasformazione dei contenuti]

    -   [Imposta intestazione HTTP][Imposta intestazione HTTP]: assegna un valore a una intestazione di risposta e/o di richiesta esistente oppure aggiunge una nuova intestazione di risposta e/o di richiesta.
    -   [Converti XML in JSON][Converti XML in JSON]: converte il corpo della richiesta o della risposta da XML al formato "JSON" o "fedele a XML" di JSON.
    -   [Sostituisci stringa nel corpo][Sostituisci stringa nel corpo]: trova una sottostringa di richiesta o risposta e la sostituisce con una sottostringa diversa.
    -   [Imposta parametro di stringa della query][Imposta parametro di stringa della query]: aggiunge, sostituisce il valore di o elimina il parametro di stringa della query di richiesta.
-   [Criteri di memorizzazione nella cache][Criteri di memorizzazione nella cache]

    -   [Archivia nella cache][Archivia nella cache]: memorizza nella cache la risposta in base alla configurazione specificata per la cache.
    -   [Recupera dalla cache][Recupera dalla cache]: esegue una ricerca nella cache e restituisce una risposta valida memorizzata nella cache, se disponibile.
-   [Altri criteri][Altri criteri]

    -   [Riscrivi URI][Riscrivi URI]: converte un URL di richiesta dal formato pubblico al formato previsto dal servizio Web.
    -   [Maschera URL nel contenuto][Maschera URL nel contenuto]: riscrive (maschera) i collegamenti nel corpo della risposta e nell'intestazione del percorso, in modo che facciano riferimento al collegamento equivalente tramite il proxy.
    -   [Permetti chiamate tra i domini][Permetti chiamate tra i domini]: rende accessibile l'API da client Adobe Flash e Microsoft Silverlight basati su browser.
    -   [JSONP][JSONP]: aggiunge il supporto per JSON con riempimento (JSONP) a un'operazione o a un'API per permettere le chiamate tra domini da client JavaScript basati su browser.
    -   [CORS][CORS]: aggiunge il supporto per CORS (Cross-Origin Resource Sharing) a un'operazione o a un'API per permettere le chiamate tra domini da client basati su browser.

## <a name="access-restriction-policies"> </a> Criteri per le restrizioni sull'accesso

-   [Quota di utilizzo][Quota di utilizzo]: permette di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda.
-   [Limite di frequenza][Limite di frequenza]: impedisce picchi d'utilizzo delle API tramite la limitazione delle chiamate e/o del tasso di consumo della larghezza di banda.
-   [Restrizione per l'IP del chiamante][Restrizione per l'IP del chiamante]: filtra (permette/rifiuta) le chiamate da indirizzi IP e/o intervalli di indirizzi IP specifici.

### <a name="usage-quota"> </a> Quota di utilizzo

**Descrizione:**
permette di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda.

**Istruzione del criterio:**

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
        <api name="name" calls="number" bandwidth="kilobytes">
            <operation name="name" calls="number" bandwidth="kilobytes" />
        </api>
    </quota>

**Esempio:**

    <policies>
        <inbound>
            <base />
            <quota calls="10000" bandwidth="40000" renewal-period="3600" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Dove può essere applicato:**
può essere usato nella sezione in entrata in corrispondenza dell'ambito Product.

**Quando può essere applicato:**
quando l'utilizzo delle operazioni e/o delle API deve essere limitato per un prodotto.

**Perché deve o non deve essere applicato:**
per definire i limiti di utilizzo per un prodotto in base all'orario e alla larghezza di banda. Quando un'API raggiunge il limite di quota definito, le chiamate successive saranno rifiutate con un messaggio di errore. Le quote sono in genere definite come numero di messaggi di richiesta permessi nel corso di un intervallo di tempo predefinito ed entro un limite specifico di larghezza di banda.

| Attributo                | Descrizione                                                                                                                              |
|--------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| quota calls="number"     | Numero massimo di chiamate di sottoscrizione permesse durante il periodo di rinnovo (ad esempio, 10000).                                 |
| bandwidth="kilobytes"    | Numero massimo di kilobyte che il prodotto, l'API o l'operazione può usare nel periodo di rinnovo specificato (ad esempio, 40000).       |
| renewal-period="seconds" | Periodo, in secondi, durante il quale la quota è applicabile (ad esempio, 3600).                                                         |
| api name="name"          | Nome della chiamata API per cui è applicabile la quota.                                                                                  |
| calls="number"           | Numero massimo di chiamate all'API o all'operazione che possono essere effettuate nel periodo di rinnovo specificato (ad esempio, 5000). |
| operation name="name"    | Nome dell'operazione per cui è applicabile la quota.                                                                                     |

### <a name="rate-limit"> </a> Limite di frequenza

**Descrizione:**
arresta i picchi di utilizzo dell'API limitando la frequenza delle richieste verso un'API e, facoltativamente, verso un'operazione di API specifica. Quando il criterio viene attivato, l'utente riceve un codice di stato `429 Too Many Requests` della risposta.

**Istruzione del criterio:**

    <rate-limit calls="number" renewal-period="seconds">
        <api name="name" calls="number" renewal-period="seconds">
            <operation name="name" calls="number" renewal-period="seconds" />
        </api>
    </rate-limit>

**Esempio:**

    <policies>
        <inbound>
            <base />
            <rate-limit calls="20" renewal-period="90" />
        </inbound>
        <outbound>
            <base />        
        </outbound>
    </policies>

**Dove può essere applicato:**
può essere usato nella sezione in entrata in corrispondenza dell'ambito Product.

| Elemento/Attributo       | Descrizione                                                                     |
|--------------------------|---------------------------------------------------------------------------------|
| calls="number"           | Numero di chiamate permesse per il periodo di rinnovo.                          |
| renewal-period="seconds" | Periodo dopo il quale la quota relativa al limite di frequenza verrà rinnovata. |
| api name="name"          | Nome dell'API a cui si applica il limite di frequenza.                          |
| operation name="name"    | Nome dell'operazione a cui si applica il limite di frequenza.                   |

### <a name="caller-ip-restriction"> </a> Restrizione per l'IP del chiamante

**Descrizione:**
filtra (permette/rifiuta) le chiamate da indirizzi IP e/o intervalli di indirizzi IP specifici.

**Istruzione del criterio:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Esempio:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Dove può essere applicato:**
può essere usato nella sezione in entrata in qualsiasi ambito.

**Quando deve essere applicato:**
usare questo criterio quando è necessario un controllo specifico sugli utenti autorizzati ad accedere al servizio.

**Perché deve o non deve essere applicato:**
questo criterio è necessario solo quando occorre un controllo rigoroso sull'accesso, ad esempio per i servizi con requisiti di sicurezza elevata, per un singolo host o per un intervallo di host.

| Attributo                                 | Descrizione                                                                                  |
|-------------------------------------------|----------------------------------------------------------------------------------------------|
| ip-filter action="allow | forbid"         | Specifica se le chiamate devono essere consentite o rifiutate per gli indirizzi specificati. |
| address="address"                         | Uno o più indirizzi IP per cui permettere o negare l'accesso.                                |
| address-range from="address" to="address" | Intervallo di indirizzi IP per cui permettere o negare l'accesso.                            |

## <a name="content-transformation-policies"> </a> Criteri di trasformazione dei contenuti

-   [Imposta intestazione HTTP][Imposta intestazione HTTP]: assegna un valore a una intestazione di risposta e/o di richiesta esistente oppure aggiunge una nuova intestazione di risposta e/o di richiesta.
-   [Converti XML in JSON][Converti XML in JSON]: converte il corpo della richiesta o della risposta da XML al formato "JSON" o "fedele a XML" di JSON.
-   [Sostituisci stringa nel corpo][Sostituisci stringa nel corpo]: trova una sottostringa di richiesta o risposta e la sostituisce con una sottostringa diversa.
-   [Imposta parametro di stringa della query][Imposta parametro di stringa della query]: aggiunge, sostituisce il valore di o elimina il parametro di stringa della query di richiesta.

### <a name="set-http-header"> </a> Imposta intestazione HTTP

**Descrizione:**
assegna un valore a una intestazione di risposta e/o di richiesta esistente oppure aggiunge una nuova intestazione di risposta e/o di richiesta.

Inserisce un elenco di intestazioni HTTP in un messaggio HTTP. Se inserito in una pipeline in entrata, questo criterio imposta le intestazioni HTTP per la richiesta passata al servizio di destinazione. Se inserito in una pipeline in uscita, questo criterio imposta le intestazioni HTTP per la risposta inviata al client del proxy.

**Istruzione del criterio:**

    <set-header name="header name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple headers with the same name add additional value elements-->
    </set-header>

**Esempio:**

    <set-header exists-action="override">
        <header name="some value to set" exists-action="override">
        <value>20</value> 
        </header>
    </set-header>

**Dove può essere applicato:**
può essere usato nelle sezioni in entrata e in uscita in corrispondenza di qualsiasi ambito, ad eccezione di *Publisher*.

**Quando deve essere applicato:**
usato per specificare i parametri operativi e/o i valori restituiti di una transazione HTTP.

**Perché deve o non deve essere applicato:**
la maggior parte delle richieste e molte risposte HTTP richiedono intestazioni per specificare i parametri di input/risposta. È quindi probabile che questo criterio sia applicabile a quasi tutti i servizi.

| Attributo                                | Descrizione                                                                                                                                                                                                                                                                                                                        |
|------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| reconcile-action="override|skip|append"  | Specifica l'azione da effettuare quando un'intestazione è già specificata. Nota: se è impostato su "override", l'integrazione di più voci con lo stesso nome avrà come risultato l'impostazione dell'intestazione in base a tutte le voci, che saranno elencate più volte. Nel risultato saranno impostati solo i valori elencati. |
| header name="header name"                | Specifica il nome dell'intestazione da impostare. Obbligatorio.                                                                                                                                                                                                                                                                    |
| exists-action="override | skip | append" | Specifica l'azione da effettuare quando un'intestazione è già specificata.                                                                                                                                                                                                                                                         |
| value="value"                            | Specifica il valore dell'intestazione da impostare.                                                                                                                                                                                                                                                                                |

### <a name="convert-xml-to-json"> </a> Converti XML in JSON

**Descrizione:**
converte il corpo della richiesta o della risposta da XML a JSON.

**Istruzione del criterio:**

    <xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>

**Esempio:**

    <policies>
        <inbound>
            <base />
        </inbound>
        <outbound>
            <base />
            <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
        </outbound>
    </policies>

**Dove può essere applicato:**
può essere usato nelle sezioni in entrata e in uscita in corrispondenza degli ambiti relativi ad API e operazioni.

**Perché deve o non deve essere applicato:**
per modernizzare le API basate su servizi Web back-end solo di tipo XML.

| Attributo                             | Descrizione                                                                                                                                              |
|---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| kind="javascript-friendly | direct    | Il formato di JSON convertito è familiare per gli sviluppatori JavaScript | Il formato JSON convertito riflette la struttura del documento XML originale |
| apply= always | content-type-xml      | Permette di eseguire sempre la conversione | Permette di eseguire la conversione solo se l'intestazione `Content-Type` indica la presenza di XML         |
| consider-accept-header="true | false" | Permette di applicare la conversione in base al valore dell'intestazione `Accept` | Permette di eseguire sempre la conversione                           |

### <a name="replace-string-in-body"> </a> Sostituisci stringa nel corpo

**Descrizione:**
trova una sottostringa nella richiesta o nella risposta e la sostituisce con una stringa diversa.

**Istruzione del criterio:**

    <find-and-replace from="what to replace" to="replacement" />

**Esempio:**

    <find-and-replace from="notebook" to="laptop" />

**Dove può essere applicato:**
può essere usato nelle sezioni in entrata e in uscita in corrispondenza di qualsiasi ambito.

| Attributo              | Descrizione                                                  |
|------------------------|--------------------------------------------------------------|
| from="what to replace" | Stringa da cercare.                                          |
| to="replacement"       | Stringa con cui sostituire quella individuata in precedenza. |

### <a name="set-query-string-parameter"> </a> Imposta parametro di stringa della query

**Descrizione:**
aggiunge, sostituisce il valore di o elimina il parametro di stringa della query di richiesta.

**Istruzione del criterio:**

    <set-query-parameter name="param name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
    </set-query-parameter>

**Esempio:**

    <policies>
        <inbound>
            <base />
            <set-query-parameter>
                <parameter name="api-key" exists-action="skip">
                    <value>12345678901</value>
                </parameter>
                <!-- for multiple parameters with the same name add additional value elements -->
            </set-query-parameter>
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Dove può essere applicato:**
può essere usato nella sezione in entrata in corrispondenza di qualsiasi ambito.

**Perché deve o non deve essere applicato:**
può essere usato per passare i parametri di query previsti dal servizio back-end che sono facoltativi o non sono mai presenti nella richiesta.

| Elemento/Attributo       | Descrizione                                                              |
|--------------------------|--------------------------------------------------------------------------|
| name="name"              | Stringa che specifica il nome del parametro.                             |
| exists-action="override" | Sostituisce il valore del parametro, se presente nella richiesta.        |
| exists-action="skip"     | Non esegue alcuna operazione se il parametro è presente nella richiesta. |
| exists-action="append"   | Aggiunge un valore alla fine del parametro della richiesta.              |
| exists-action="delete"   | Rimuove il parametro dalla richiesta.\*                                  |
| value="value"            | Imposta il valore del parametro nell'elemento che lo racchiude.          |

## <a name="caching-policies"> </a> Criteri di memorizzazione nella cache

-   [Archivia nella cache][Archivia nella cache]: memorizza nella cache la risposta in base alla configurazione specificata per la cache.
-   [Recupera dalla cache][Recupera dalla cache]: esegue una ricerca nella cache e restituisce una risposta valida memorizzata nella cache, se disponibile.

### <a name="store-to-cache"> </a> Archivia nella cache

**Descrizione:**
memorizza nella cache le risposte in base alle impostazioni specificate per la cache. Deve essere associato a un criterio [Recupera dalla cache][Recupera dalla cache] corrispondente.

**Istruzione del criterio:**

    cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />

**Esempio:**

    <policies>
        <inbound>
            <base />
              <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
                    <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
                    <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
            </cache-lookup>
        </inbound>
        <outbound>
            <base />
                <cache-store duration="3600" caching-mode="cache-on" />
        </outbound>
    </policies>

**Dove può essere applicato:**
può essere presente nella sezione in uscita in corrispondenza degli ambiti dell'API e dell'operazione.

**Quando deve essere applicato:**
deve essere applicato nei casi in cui il contenuto della risposta rimane statico in un periodo di tempo.

**Perché deve o non deve essere applicato:**
la memorizzazione delle risposte nella cache riduce la larghezza di banda e i requisiti di elaborazione imposti sul server Web back-end e riduce la latenza percepita dagli utenti delle API.

| Elemento/Attributo      | Descrizione                                                                                                                                                                                                    |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| secondi                 | Durata (TTL, Time-To-Live) delle voci memorizzate nella cache, in secondi. Valore predefinito: 3600.                                                                                                           |
| cache-on | do-not-cache | Le risposte sono memorizzate nella cache | Le risposte non vengono memorizzate nella cache e le intestazioni correlate alla cache sono impostate in modo da impedire la memorizzazione nella cache downstream. |

### <a name="get-from-cache"> </a> Recupera dalla cache

**Descrizione:**
esegue una ricerca nella cache e restituisce una risposta valida memorizzata nella cache, se disponibile. Risponde in modo appropriato alle richieste di convalida della cache dagli utenti dell'API. Deve essere associato a un criterio [Archivia nella cache][Archivia nella cache] corrispondente.

**Istruzione del criterio:**

    <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
        <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
        <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
    </cache-lookup>

**Esempio:**

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none">
                <vary-by-query-parameter>version</vary-by-query-parameter>
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>         
        </inbound>
        <outbound>
            <cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />
            <base />        
        </outbound>
    </policies>

**Dove può essere applicato:**
può essere presente nella sezione in entrata in corrispondenza degli ambiti dell'API e dell'operazione.

**Quando deve essere applicato:**
deve essere applicato nei casi in cui il contenuto della risposta rimane statico in un periodo di tempo.

**Perché deve o non deve essere applicato:**
la memorizzazione delle risposte nella cache riduce la larghezza di banda e i requisiti di elaborazione imposti sul server Web back-end e riduce la latenza percepita dagli utenti delle API.

| Elemento/Attributo                                | Descrizione                                                                                                                                                                                                                                                                |
|---------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| vary-by-developer="true | false"                  | Impostato su \*true\* per avviare le risposte di memorizzazione nella cache per ogni chiave di sviluppatore. È \*false\* per impostazione predefinita.                                                                                                                     |
| vary-by-developer-groups="true | false"           | Impostato su \*true\* per avviare le risposte di memorizzazione nella cache per ogni ruolo utente. È \*false\* per impostazione predefinita.                                                                                                                               |
| downstream-caching-type="none | private | public" | \*none\*: la memorizzazione nella cache downstream non è permessa. Impostazione predefinita. | \*private\*: la memorizzazione nella cache downstream privata è permessa. | \*public\*: la memorizzazione nella cache downstream privata e condivisa è permessa.            |
| vary-by-header: "Accept"                          | Avvia risposte di memorizzazione nella cache per ogni valore dell'intestazione `Accept`.                                                                                                                                                                                   |
| vary-by-header: Accept-Charset"                   | Avvia risposte di memorizzazione nella cache per ogni valore dell'intestazione `Accept-Charset`.                                                                                                                                                                           |
| vary-by-header: "header name"                     | Avvia risposte di memorizzazione nella cache per ogni valore dell'intestazione specificata, ad esempio `Accept | Accept-Charset | Accept-Encoding | Accept-Language | Authorization | Expect | From | Host | If-Match`.                                                    |
| vary-by-query-parameter: "parameter name"         | Avvia risposte di memorizzazione nella cache per ogni valore dei parametri di query specificati. Immettere un singolo parametro o più parametri. Usare un punto e virgola (;) come separatore. Se non è specificato alcun nome, verranno usati tutti i parametri di query. |

## <a name="other-policies"> </a> Altri criteri

-   [Riscrivi URI][Riscrivi URI]: converte un URL di richiesta dal formato pubblico al formato previsto dal servizio Web.
-   [Maschera URL nel contenuto][Maschera URL nel contenuto]: riscrive (maschera) i collegamenti nel corpo della risposta e nell'intestazione del percorso, in modo che facciano riferimento al collegamento equivalente tramite il proxy.
-   [Permetti chiamate tra i domini][Permetti chiamate tra i domini]: rende accessibile l'API da client Adobe Flash e Microsoft Silverlight basati su browser.
-   [JSONP][JSONP]: aggiunge il supporto per JSON con riempimento (JSONP) a un'operazione o a un'API per permettere le chiamate tra domini da client JavaScript basati su browser.
-   [CORS][CORS]: aggiunge il supporto per CORS (Cross-Origin Resource Sharing) a un'operazione o a un'API per permettere le chiamate tra domini da client basati su browser.

### <a name="rewrite-uri"> </a> Riscrivi URI

**Descrizione:**
converte un URL di richiesta dal formato pubblico al formato previsto dal servizio Web.

**URL pubblico:** <http://api.example.com/storenumber/ordernumber>

**URL richiesta:** <http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State>.

Non aggiungere un parametro di percorso di modello aggiuntivo nell'URL riscritto. È possibile aggiungere solo parametri delle stringhe di query.

**Istruzione del criterio:**

    <rewrite-uri template="uri template" />

**Esempio:**

    <policies>
        <inbound>
            <base />
            <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Dove può essere applicato:**
nella sezione in entrata in corrispondenza del solo ambito Operation.

**Quando deve essere applicato:**
può essere usato quando un URL ideale per utenti e/o per il browser deve essere trasformato nel formato di URL previsto dal servizio Web.

**Perché deve o non deve essere applicato:**
deve essere applicato solo quando si espone un formato di URL alternativo. Gli URL semplici, gli URL RESTful, gli URL ideali per gli utenti o gli URL ideali per SEO sono URL puramente strutturali, che non includono una stringa di query ma contengono solo il percorso della risorsa, dopo lo schema e l'autorità. Sono spesso usati a fini estetici, di usabilità o di ottimizzazione dei motori di ricerca (SEO, Search Engine Optimization).

| Attributo               | Descrizione                                                                     |
|-------------------------|---------------------------------------------------------------------------------|
| template="uri template" | URL effettivo del servizio Web con eventuali parametri delle stringhe di query. |

### <a name="mask-urls-in-content"> </a> Maschera URL nel contenuto

**Descrizione:**
riscrive (maschera) i collegamenti nel corpo della risposta e nell'intestazione del percorso, in modo che facciano riferimento al collegamento equivalente tramite il proxy.

**Istruzione del criterio:**

    <redirect-body-urls />

**Esempio:**

    <redirect-body-urls />

**Dove può essere applicato:**
può essere applicato negli ambiti *API* e *Operation*. Può essere applicato nella sezione in entrata o in uscita.

### <a name="allow-cross-domain-calls"> </a> Permetti chiamate tra i domini

**Descrizione:**
rende accessibile l'API da client Adobe Flash e Microsoft Silverlight basati su browser.

**Istruzione del criterio:**

    <cross-domain />

**Esempio:**

    <cross-domain />

**Dove può essere applicato:**
può essere usato nella sezione in entrata in corrispondenza dell'ambito *Publishers*.

### <a name="jsonp"> </a> JSONP

**Descrizione:**
aggiunge il supporto per JSON con riempimento (JSONP) a un'operazione o a un'API per permettere le chiamate tra domini da client JavaScript basati su browser. JSONP è un metodo usato in programmi JavaScript per richiedere dati da un server in un dominio diverso. JSONP supera le limitazioni applicate dalla maggior parte dei Web browser, in cui l'accesso alle pagine Web deve essere effettuato nello stesso dominio.

**Istruzione del criterio:**

    <jsonp callback-parameter-name="callback function name" />

**Esempio:**

    <jsonp callback-parameter-name="cb" />

Se si chiama il metodo senza il parametro di callback `?cb=XXX` restituirà JSON semplice, senza wrapper di chiamata della funzione.

Se si aggiunge il parametro di callback, `?cb=XXX` restituirà un risultato JSONP, eseguendo il wrapping dei risultati JSON originali intorno alla funzione di callback, ad esempio `XXX('<json result goes here>')`;

**Dove può essere applicato:**
può essere usato solo nella sezione in uscita.

**Quando deve essere applicato:**
per richiedere dati da un server in un dominio diverso.

| Attributo               | Descrizione                                                                                                   |
|-------------------------|---------------------------------------------------------------------------------------------------------------|
| callback-parameter-name | Funzione JavaScript tra domini che ha come prefisso il nome completo del dominio in cui si trova la funzione. |

### <a name="cors"> </a> CORS

**Descrizione:**
aggiunge il supporto per CORS (Cross-Origin Resource Sharing) a un'operazione o a un'API per permettere le chiamate tra domini da client basati su browser.

CORS permette a un browser e a un server di interagire e di determinare se permettere o meno richieste specifiche con origini diverse, ad esempio chiamate XMLHttpRequests effettuate da JavaScript in una pagina Web in altri domini. Ciò offre una maggiore flessibilità rispetto a permettere solo richieste con la stessa origine e una maggiore sicurezza rispetto a permettere tutte le richieste con origini diverse.

**Istruzione del criterio:**

     <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>URI</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
     </cors>

**Esempio:**

    <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>http://contoso.com:81</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
    </cors>

**Dove può essere applicato:**
può essere usato nella sezione in entrata e solo negli ambiti *API* e *Operation*.

| Attributo            | Descrizione                                                                                                                                   |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| <origin>\*</origin>  | Permette tutti gli URI OPPURE un elenco di uno o più URI specifici.                                                                           |
| <origin>URI</origin> | L'URI deve includere uno schema, un host e una porta. Se la porta viene omessa, verranno usate la porta 80 per http e la porta 443 per https. |

  [Criteri di Gestione API]: ../api-management-howto-policies
  [Criteri per le restrizioni sull'accesso]: #access-restriction-policies
  [Quota di utilizzo]: #usage-quota
  [Limite di frequenza]: #rate-limit
  [Restrizione per l'IP del chiamante]: #caller-ip-restriction
  [Criteri di trasformazione dei contenuti]: #content-transformation-policies
  [Imposta intestazione HTTP]: #set-http-header
  [Converti XML in JSON]: #convert-xml-to-json
  [Sostituisci stringa nel corpo]: #replace-string-in-body
  [Imposta parametro di stringa della query]: #set-query-string-parameter
  [Criteri di memorizzazione nella cache]: #caching-policies
  [Archivia nella cache]: #store-to-cache
  [Recupera dalla cache]: #get-from-cache
  [Altri criteri]: #other-policies
  [Riscrivi URI]: #rewrite-uri
  [Maschera URL nel contenuto]: #mask-urls-in-content
  [Permetti chiamate tra i domini]: #allow-cross-domain-calls
  [JSONP]: #jsonp
  [CORS]: #cors

<!--HONumber=46--> 

<!--HONumber=46--> 
