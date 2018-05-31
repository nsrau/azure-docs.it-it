---
title: Guida al protocollo per le connessioni ibride di inoltro di Azure | Microsoft Docs
description: Guida al protocollo per le connessioni ibride di inoltro di Azure.
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: ''
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 306a21add76261dce99c954a2ba373e4b5047a75
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895411"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocollo per le connessioni ibride di inoltro di Azure

L'inoltro di Azure è una delle funzionalità chiave di base della piattaforma Bus di servizio di Azure. La nuova funzionalità _Connessioni ibride_ di inoltro è un'evoluzione sicura del protocollo aperto basata su HTTP e WebSocket. Sostituisce la funzionalità precedente, comunemente denominata _Servizi BizTalk_, che è basata su un protocollo di proprietà. L'integrazione di Connessioni ibride nei servizi app di Azure continuerà a funzionare così com'è.

Connessioni ibride permette di stabilire una comunicazione bidirezionale con flussi binari tra due applicazioni di rete, di cui una o entrambe le parti sono protette da NAT o firewall.

Questo articolo descrive le interazioni lato client con l'inoltro di Connessioni ibride per la connessione dei client nei ruoli listener e mittente e il modo in cui i listener accettano nuove connessioni e richieste.

## <a name="interaction-model"></a>Modello di interazione

L'inoltro di Connessioni ibride connette due parti tramite un punto di incontro nel cloud di Azure che le parti possono individuare e a cui possono connettersi dalla rispettiva rete. Tale punto di incontro è detto "connessione ibrida" in questo e in altri documenti, nelle API e nel portale di Azure. L'endpoint di servizio di Connessioni ibride viene chiamato "servizio" nella parte restante di questo articolo.

Il servizio consente di inoltrare connessioni WebSocket e richieste e risposte HTTP/HTTPS.

Il modello di interazione si basa sulla nomenclatura stabilita da diverse altre API di rete. È presente un listener che prima indica la conformità alla gestione delle connessioni in ingresso e successivamente le accetta quando arrivano. Sull'altro lato un client si connette al listener, aspettando che la connessione venga accettata per stabilire un percorso di comunicazione bidirezionale. "Connettersi", "essere in ascolto", "accettare" sono gli stessi termini usati nella maggior parte delle API socket.

In un modello di comunicazione di inoltro entrambe le parti creano connessioni in uscita verso un endpoint di servizio, rendendo il "listener" anche un "client" nel linguaggio comune e causando altre sovrapposizioni terminologiche. La terminologia esatta usata per Connessioni ibride è quindi la seguente:

I programmi su entrambi i lati di una connessione sono detti "client", perché sono i client del servizio. Il client che attende e accetta le connessioni è il "listener", che è anche nel "ruolo listener". Il client che avvia una nuova connessione verso un listener tramite il servizio è detto "mittente", che è anche nel "ruolo mittente".

### <a name="listener-interactions"></a>Interazioni del listener

Il listener ha cinque interazioni con il servizio. Tutti i dettagli sono descritti più avanti in questo articolo nella sezione di riferimento.

I messaggi di ascolto, accettazione e richiesta vengono ricevuti dal servizio. Le operazioni di rinnovo e ping vengono inviate dal listener.

#### <a name="listen-message"></a>Messaggio di ascolto

Un listener, per indicare al servizio che è pronto ad accettare le connessioni, crea una connessione WebSocket in uscita. L'handshake della connessione trasporta il nome di una connessione ibrida configurata nello spazio dei nomi dell'inoltro e un token di sicurezza che conferisce il diritto di "ascoltare" a tale nome.

Quando il WebSocket viene accettato dal servizio, la registrazione è completa e il WebSocket stabilito viene mantenuto attivo come "canale di controllo" per abilitare tutte le interazioni successive. Il servizio consente fino a 25 listener simultanei in una connessione ibrida. La quota per AppHooks deve essere stabilita.

Per Connessioni ibride, se sono presenti due o più listener attivi, le connessioni in ingresso sono bilanciate tra loro in ordine casuale e non viene garantita una distribuzione equa.

#### <a name="accept-message"></a>Messaggio di accettazione

Quando un mittente apre una nuova connessione nel servizio, il servizio sceglie uno dei listener attivi nella connessione ibrida e gli invia una notifica. La notifica viene inviata al listener tramite il canale di controllo aperto come messaggio JSON contenente l'URL dell'endpoint del WebSocket a cui il listener deve connettersi per accettare la connessione.

L'URL può e deve essere usato direttamente dal listener senza operazioni aggiuntive.
Le informazioni codificate sono valide solo per un breve periodo, essenzialmente finché il mittente è disposto ad attendere che venga stabilita una connessione end-to-end. Il tempo massimo previsto è pari a 30 secondi. L'URL può essere usato solo per tentativo di connessione riuscito. Non appena viene stabilita la connessione WebSocket con l'URL di incontro, tutte le altre attività in questo WebSocket vengono inoltrate da e verso il mittente, senza interventi o interpretazioni da parte del servizio.

### <a name="request-message"></a>Messaggio di richiesta

Oltre alle connessioni WebSocket, il listener può anche ricevere frame di richiesta HTTP da un mittente, se questa funzionalità è abilitata in modo esplicito per la connessione ibrida.

I listener associati a Connessioni ibride con supporto HTTP DEVONO gestire l'azione `request`. Un listener che non gestisce l'azione `request` e che pertanto causa errori di timeout ripetuti durante la connessione PUÒ essere disattivato dal servizio in futuro.

I metadati di intestazione del frame HTTP vengono convertiti in JSON per essere gestiti in modo più semplice dal framework del listener, anche perché le librerie di analisi dell'intestazione HTTP sono più rare rispetto ai parser JSON. I metadati HTTP rilevanti solo per la relazione tra il mittente e il gateway HTTP di inoltro, incluse le informazioni di autorizzazione, non vengono inoltrati. I corpi delle richieste HTTP vengono trasferiti in modo trasparente come frame WebSocket binari.

Il listener può rispondere a richieste HTTP effettuate con un'azione di risposta equivalente.

Il flusso di richiesta/risposta usa il canale di controllo per impostazione predefinita, ma può essere "aggiornato" a un WebSocket di incontro distinto, quando necessario. Connessioni WebSocket distinte migliorano la velocità effettiva per ogni conversazione client, ma aumentano il carico del listener con più connessioni da gestire, situazione non auspicabile per i client più semplici.

Sul canale di controllo le dimensioni dei corpi dei messaggi di richiesta e risposta sono limitate a un massimo di 64 KB. I metadati di intestazione HTTP sono limitati a un totale di 32 KB. Se la richiesta o la risposta supera tale soglia, il listener DEVE eseguire l'aggiornamento a un WebSocket di incontro con un'azione equivalente alla gestione del massaggio di [accettazione](#accept-message).

Il servizio decide se instradare le richieste tramite il canale di controllo. Ciò include, in via esemplificativa, il caso in cui una richiesta superi i 64 KB (intestazioni e corpo) complessivi o quello in cui la richiesta venga inviata con [codifica di trasferimento in blocchi](https://tools.ietf.org/html/rfc7230#section-4.1) e il servizio preveda che la richiesta superi i 64 KB o legga che la richiesta non è istantanea. Se il servizio sceglie di recapitare la richiesta sul punto di incontro, al listener passa solo l'indirizzo di quest'ultimo.
Il listener DEVE quindi stabilire il WebSocket di incontro e il servizio recapita tempestivamente la richiesta completa, inclusi i corpi, sul WebSocket di incontro. Anche la risposta DEVE usare il WebSocket di incontro.

Per le richieste che arrivano tramite il canale di controllo, il listener decide se rispondere sul canale di controllo o tramite punto di incontro. Il servizio DEVE includere un indirizzo del punto di incontro in tutte le richieste indirizzate tramite il canale di controllo. Tale indirizzo è valido solo per l'aggiornamento dalla richiesta corrente.

Se il listener sceglie di eseguire l'aggiornamento, si connette e recapita tempestivamente la risposta sul socket di incontro stabilito.

Dopo che il WebSocket di incontro è stato stabilito, il listener DEVE mantenerlo per gestire successivamente le richieste e le risposte dello stesso client. Il servizio mantiene il WebSocket fino a quando la connessione socket HTTPS con il mittente è presente e indirizza tutte le richieste successive provenienti da tale mittente sul WebSocket specifico. Se il listener sceglie di eliminare il WebSocket di incontro nel proprio lato, il servizio elimina anche la connessione al mittente, indipendentemente dal fatto che una richiesta successiva possa essere già in corso.

#### <a name="renew-operation"></a>Operazione di rinnovo

Il token di sicurezza che deve essere usato per registrare il listener e mantenere il canale di controllo può scadere mentre il listener è attivo. La scadenza del token non ha effetto sulle connessioni in corso, ma il canale di controllo viene rimosso dal servizio al momento della scadenza o poco dopo. L'operazione "renew" è un messaggio JSON che il listener può inviare per sostituire il token associato al canale di controllo che potrà quindi essere mantenuto per lunghi periodi.

#### <a name="ping-operation"></a>Operazione di ping

Se il canale di controllo rimane inattivo a lungo, gli intermediari lungo il percorso, ad esempio servizi di bilanciamento del carico o NAT, potrebbero rimuovere la connessione TCP. L'operazione "ping" evita questo problema inviando nel canale una piccola quantità di dati che ricorda a tutti gli elementi nella route di rete che la connessione deve restare attiva, oltre a fungere da test dello stato attivo per il listener. Se il ping non riesce, il canale di controllo deve essere considerato non utilizzabile e il listener deve riconnettersi.

### <a name="sender-interaction"></a>Interazione del mittente

Il mittente ha due interazioni con il servizio, ovvero si connette a un protocollo WebSocket o invia richieste tramite HTTPS. Le richieste non possono essere inviate tramite WebSocket dal ruolo mittente.

#### <a name="connect-operation"></a>Operazione di connessione

L'operazione "connect" apre un WebSocket nel servizio, fornendo il nome della connessione ibrida e un token di sicurezza (facoltativo, ma richiesto per impostazione predefinita) che conferisce l'autorizzazione "Send" nella stringa di query. Il servizio interagisce quindi con il listener nel modo descritto in precedenza e il listener crea una connessione di incontro che viene unita a questo WebSocket. Dopo che il WebSocket è stato accettato, tutte le altre interazioni nel WebSocket avvengono quindi con un listener connesso.

#### <a name="request-operation"></a>Operazione di richiesta

Per le istanze di Connessioni ibride per cui è stata la funzionalità è stata abilitata, il mittente può inviare al listener richieste HTTP in gran parte senza restrizioni.

Ad eccezione di un token di accesso di inoltro incorporato nella stringa di query o in un'intestazione HTTP della richiesta, il servizio di inoltro è completamente trasparente per tutte le operazioni HTTP sull'indirizzo di inoltro e per tutti i suffissi del percorso relativo, lasciando al listener il pieno controllo dell'autorizzazione end-to-end e anche le funzionalità di estensione HTTP, ad esempio [CORS](https://www.w3.org/TR/cors/).

L'autorizzazione mittente per l'endpoint di inoltro è attivata per impostazione predefinita, ma è FACOLTATIVA. Il proprietario della connessione ibrida può scegliere di consentire mittenti anonimi. Il servizio intercetta, controlla e rimuove le informazioni di autorizzazione come indicato di seguito:

1. Se la stringa di query contiene un'espressione `sb-hc-token`, l'espressione viene SEMPRE rimossa dalla stringa di query e viene valutato se l'autorizzazione di inoltro è attivata.
2. Se le intestazioni della richiesta contengono un elemento `ServiceBusAuthorization`, l'espressione dell'intestazione viene SEMPRE rimossa dalla raccolta di intestazioni
   e viene valutato se l'autorizzazione di inoltro è attivata.
3. Solo se l'autorizzazione di inoltro è attivata, se le intestazioni della richiesta contengono un elemento `Authorization` e se nessuna delle espressioni precedenti è presente, l'intestazione viene valutata e rimossa. In caso contrario, l'elemento `Authorization` viene sempre trasmesso così com'è.

Se non è presente alcun listener attivo, il servizio restituisce un codice di errore 502 "Gateway non valido". Se sembra che il servizio non gestisca la richiesta, viene restituito un codice di errore 504 "Timeout gateway" dopo 60 secondi.

### <a name="interaction-summary"></a>Riepilogo delle interazioni

Il risultato di questo modello di interazione è che il client mittente esce dall'handshake con un WebSocket "pulito" che è connesso a un listener e non richiede altri preamboli o preparazioni. Questo modello consente in pratica alle implementazioni di client WebSocket esistenti di usare subito il servizio Connessioni ibride specificando un URL correttamente costruito nel livello del client WebSocket.

Anche il WebSocket della connessione di incontro che il listener ottiene tramite l'interazione accept è pulito e può essere assegnato a qualsiasi implementazione di server WebSocket esistente con una minima astrazione aggiuntiva che distingue tra operazioni "accept" sui listener di rete locale del framework e operazioni "accept" remote di Connessioni ibride.

Il modello di richiesta/risposta HTTP consente al mittente di operare in un'area del protocollo HTTP in gran parte senza restrizioni con un livello di autorizzazione FACOLTATIVO. Il listener riceve una sezione di intestazione della richiesta HTTP pre-analizzata riportare a una richiesta HTTP downstream o gestire così com'è, con frame binari che trasporta corpi HTTP. Le risposte usano lo stesso formato. Le interazioni con meno di 64 KB nel corpo della richiesta e della risposta possono essere gestite su un singolo WebSocket condiviso tra tutti i mittenti. Richieste e risposte di dimensioni maggiori possono essere gestite usando il modello di incontro.

## <a name="protocol-reference"></a>Riferimento al protocollo

Questa sezione descrive i dettagli delle interazioni del protocollo descritte sopra.

Tutte le connessioni WebSocket vengono stabilite sulla porta 443 come aggiornamento da HTTPS 1.1, che è in genere un'astrazione di alcune API o framework del WebSocket. La presente descrizione è neutra dal punto di vista dell'implementazione e non suggerisce un framework specifico.

### <a name="listener-protocol"></a>Protocollo del listener

Il protocollo del listener è costituito da due comandi di connessione e da tre operazioni messaggio.

#### <a name="listener-control-channel-connection"></a>Connessione del canale di controllo del listener

Il canale di controllo viene aperto con la creazione di una connessione WebSocket a:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

`namespace-address` è il nome di dominio completo dello spazio dei nomi di inoltro di Azure che ospita la connessione ibrida, in genere nel formato `{myname}.servicebus.windows.net`.

Le opzioni dei parametri della stringa di query sono le seguenti.

| Parametro        | Obbligatoria | Descrizione
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Sì      | Per il ruolo listener, il parametro deve essere **sb-hc-action=listen**
| `{path}`         | Sì      | Percorso dello spazio dei nomi codificato con URL della connessione ibrida preconfigurata in cui registrare questo listener. Questa espressione viene aggiunta alla parte del percorso `$hc/` fissa.
| `sb-hc-token`    | Sì\*    | Il listener deve fornire un token di accesso condiviso del bus di servizio codificato con URL valido per lo spazio dei nomi o la connessione ibrida che conferisce il diritto **Listen**.
| `sb-hc-id`       | No        | Questo ID facoltativo fornito dal client consente la traccia diagnostica end-to-end.

Se la connessione WebSocket non riesce perché il percorso della connessione ibrida non è registrato oppure a causa di un token mancante o non valido o di un altro errore, il feedback dell'errore viene specificato usando il normale modello di feedback dello stato HTTP 1.1. La descrizione dello stato contiene un ID di traccia dell'errore che può essere comunicato al personale del supporto di Azure:

| Codice | Tipi di errore          | DESCRIZIONE
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Non trovato      | Il percorso della connessione ibrida non è valido o il formato dell'URL di base non è corretto.
| 401  | Non autorizzata   | Il token di sicurezza è mancante, non valido o non corretto.
| 403  | Accesso negato      | Il token di sicurezza non è valido per questo percorso per questa azione.
| 500  | Errore interno | Si è verificato un errore nel servizio.

Se la connessione WebSocket viene intenzionalmente arrestata dal servizio dopo la configurazione iniziale, il motivo viene comunicato usando un codice errore del protocollo WebSocket appropriato con un messaggio di errore descrittivo che include anche un ID di traccia. Il servizio non arresterà il canale di controllo senza riscontrare una condizione di errore. Le chiusure normali sono controllate dal client.

| Stato Web Socket | DESCRIZIONE
| --------- | -------------------------------------------------------------------------------
| 1001      | Il percorso della connessione ibrida è stato eliminato o disabilitato.
| 1008      | Il token di sicurezza è scaduto e i criteri di autorizzazione vengono quindi violati.
| 1011      | Si è verificato un errore nel servizio.

#### <a name="accept-handshake"></a>Handshake accept

La notifica "accept" viene inviata dal servizio al listener tramite il canale di controllo stabilito in precedenza come messaggio JSON in una cornice di testo del WebSocket. Non sono previste risposte a questo messaggio.

Il messaggio contiene un oggetto JSON denominato "accept", che definisce le proprietà attuali seguenti:

* **address**: stringa dell'URL da usare per stabilire il WebSocket al servizio per accettare una connessione in ingresso.
* **id**: identificatore univoco per questa connessione. Se l'ID è stato fornito dal client mittente, si tratta del valore fornito dal mittente. In caso contrario, è un valore generato dal sistema.
* **connectHeaders**: tutte le intestazioni HTTP fornite all'endpoint di inoltro dal mittente, che include anche le intestazioni Sec-WebSocket-Protocol e Sec-WebSocket-Extensions.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

L'URL dell'indirizzo specificato nel messaggio JSON viene usato dal listener per stabilire il WebSocket per accettare o rifiutare il socket del mittente.

##### <a name="accepting-the-socket"></a>Accettazione del socket

Per accettare, il listener stabilisce una connessione WebSocket all'indirizzo specificato.

Se il messaggio "accept" contiene un'intestazione `Sec-WebSocket-Protocol`, è previsto che il listener accetti il WebSocket solo se supporta tale protocollo. Imposta inoltre l'intestazione quando viene definito il WebSocket.

Lo stesso vale per l'intestazione `Sec-WebSocket-Extensions`. Se il framework supporta un'estensione, deve impostare l'intestazione sulla risposta lato server dell'handshake `Sec-WebSocket-Extensions` obbligatorio per l'estensione.

L'URL deve essere usato così com'è per stabilire il socket di accettazione, ma contiene i parametri seguenti:

| Parametro      | Obbligatoria | DESCRIZIONE
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sì      | Per accettare un socket, il parametro deve essere `sb-hc-action=accept`
| `{path}`       | Sì      | (vedere il paragrafo seguente)
| `sb-hc-id`     | No        | Vedere la descrizione precedente di **id**.

`{path}` è il percorso dello spazio dei nomi codificato con URL della connessione ibrida preconfigurata in cui registrare questo listener. Questa espressione viene aggiunta alla parte del percorso `$hc/` fissa.

L'espressione `path` può essere estesa aggiungendo al nome registrato una barra, un suffisso e un'espressione di stringa di query.
Il client mittente può in questo modo trasmettere gli argomenti di invio al listener di accettazione quando non è possibile includere le intestazioni HTTP. Il framework del listener dovrebbe analizzare la parte fissa del percorso e il nome registrato dal percorso, quindi rendere disponibile la parte restante (possibilmente senza argomenti di stringa di query con prefisso `sb-`) all'applicazione perché decida se accettare la connessione.

Per altre informazioni, vedere la sezione "Protocollo per il mittente" che segue.

In caso di errore il servizio può rispondere come segue:

| Codice | Tipi di errore          | DESCRIZIONE
| ---- | -------------- | -----------------------------------
| 403  | Accesso negato      | URL non valido.
| 500  | Errore interno | Si è verificato un errore nel servizio

 Dopo che la connessione è stata stabilita, il server arresta il WebSocket quando il WebSocket mittente si arresta o ha lo stato seguente:

| Stato Web Socket | DESCRIZIONE                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Il client mittente arresta la connessione.                                    |
| 1001      | Il percorso della connessione ibrida è stato eliminato o disabilitato.                        |
| 1008      | Il token di sicurezza è scaduto e i criteri di autorizzazione vengono quindi violati. |
| 1011      | Si è verificato un errore nel servizio.                                            |

##### <a name="rejecting-the-socket"></a>Rifiuto del socket

 Per rifiutare il socket dopo avere esaminato il messaggio `accept`, è necessario un handshake simile in modo che il codice e la descrizione dello stato che comunicano il motivo del rifiuto possano tornare al mittente.

 La scelta di progettazione del protocollo in questo caso prevede l'uso di un handshake WebSocket (progettato per restituire uno stato di errore definito) in modo che le implementazioni del client listener possano continuare a basarsi su un client WebSocket e non sia necessario impiegare un altro client HTTP di base.

 Per rifiutare il socket, il client accetta l'URI dell'indirizzo dal messaggio `accept` e vi aggiunge due parametri di stringa di query, come indicato di seguito:

| Param                   | Obbligatoria | DESCRIZIONE                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Sì      | Codice di stato HTTP numerico.                |
| sb-hc-statusDescription | Sì      | Motivo leggibile del rifiuto. |

L'URI risultante viene quindi usato per stabilire una connessione WebSocket.

Se completato correttamente, questo handshake non riuscirà di proposito con il codice di errore HTTP 410, perché non è stato stabilito alcun WebSocket. Se si verificano problemi, i codici seguenti descrivono l'errore:

| Codice | Tipi di errore          | DESCRIZIONE                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Accesso negato      | URL non valido.                |
| 500  | Errore interno | Si è verificato un errore nel servizio. |

#### <a name="request-message"></a>Messaggio di richiesta

Il messaggio `request` viene inviato dal servizio al listener tramite il canale di controllo. Dopo la definizione della connessione, lo stesso messaggio viene inviato anche sul WebSocket di incontro.

L'elemento `request` è costituito da due parti: un frame dell'intestazione e uno del corpo binario.
Se non è presente alcun corpo, i frame del corpo vengono omessi. L'indicatore per verificare se è un corpo è presente è la proprietà booleana `body` nel messaggio di richiesta.

Per una richiesta con un corpo, la richiesta è simile alla presente:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

Il listener DEVE gestire la ricezione di corpo della richiesta suddiviso tra più frame binari (vedere [Frammenti WebSocket](https://tools.ietf.org/html/rfc6455#section-5.4)).
La richiesta termina quando viene ricevuto un frame binario con il set di flag FIN.

Per una richiesta senza corpo, è disponibile solo un frame di testo.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

Il contenuto JSON per `request` è indicato di seguito:

* **address** - stringa URI. Questo è l'indirizzo di incontro da usare per la richiesta. Se la richiesta in ingresso è superiore a 64 KB, la parte restante di questo messaggio viene lasciata vuota e il client DEVE avviare un handshake di incontro equivalente all'operazione `accept` descritta di seguito. Il servizio inserisce quindi l'elemento `request` completo sul WebSocket stabilito. Se si prevede che la risposta superi i 64 KB, il listener DEVE anche avviare un handshake di incontro e quindi trasferire la risposta tramite il WebSocket stabilito.
* **id** - stringa. Identificatore univoco per questa richiesta.
* **requestHeaders** - questo oggetto contiene tutte le intestazioni HTTP inviate dal mittente all'endpoint, ad eccezione delle informazioni di autorizzazione come indicato [in precedenza](#request-operation), e le intestazioni strettamente correlate alla connessione con il gateway. In particolare, TUTTE le intestazioni definite o riservate in [RFC7230](https://tools.ietf.org/html/rfc7230), ad eccezione di `Via`, vengono rimosse e non inoltrate:

  * `Connection` (RFC7230, Sezione 6.1)
  * `Content-Length` (RFC7230, Sezione 3.3.2)
  * `Host` (RFC7230, Sezione 5.4)
  * `TE` (RFC7230, Sezione 4.3)
  * `Trailer` (RFC7230, Sezione 4.4)
  * `Transfer-Encoding` (RFC7230, Sezione 3.3.1)
  * `Upgrade` (RFC7230, Sezione 6.7)
  * `Close` (RFC7230, Sezione 8.1)

* **requestTarget** - stringa. Questa proprietà contiene la ["destinazione richiesta" (RFC7230, Sezione 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) della richiesta. Ciò include la parte della stringa di query, rimossa da TUTTI i parametri `sb-hc-` con prefisso.
* **method** - stringa. Metodo della richiesta, per ogni elemento [RFC7231, Sezione 4](https://tools.ietf.org/html/rfc7231#section-4). Il metodo `CONNECT` NON DEVE essere usato.
* **body** - booleano. Indica se è presente più di un frame di corpo binario.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Risposta alle richieste

Il ricevente DEVE rispondere. Se ripetuta, la mancata risposta alle richieste durante la connessione può comportare la disattivazione del listener.

Le risposte possono essere inviate in qualsiasi ordine, ma ogni richiesta deve ottenere una risposta entro 60 secondi o il recapito verrà segnalato come non riuscito. La scadenza di 60 secondi viene conteggiata fino a quando il frame `response` non è stato ricevuto dal servizio. Una risposta in corso con più frame binari non può restare inattiva per più di 60 secondi o viene terminata.

Se la richiesta viene ricevuta tramite il canale di controllo, la risposta DEVE essere inviata sul canale di controllo da cui la richiesta è stata ricevuta o DEVE essere inviata tramite un canale di incontro.

La risposta è un oggetto JSON denominato "response". Le regole per la gestione del contenuto del corpo sono esattamente quelle usate con il messaggio `request` e si basano sulla proprietà `body`.

* **requestId** - stringa. OBBLIGATORIO. Valore della proprietà `id` del messaggio `request` a cui rispondere.
* **statusCode** - numero. OBBLIGATORIO. Codice di stato HTTP numerico che indica il risultato della notifica. Tutti i codici di stato di [RFC7231, Sezione 6](https://tools.ietf.org/html/rfc7231#section-6) sono consentiti, ad eccezione di [502 "Gateway non valido"](https://tools.ietf.org/html/rfc7231#section-6.6.3) e [504 "Timeout gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** - stringa. FACOLTATIVO. Frase del motivo del codice di stato HTTP per [RFC7230, Sezione 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** - intestazione HTTP da impostare in una risposta HTTP esterna.
  Come con `request`, le intestazioni RFC7230 definite NON DEVONO essere usate.
* **body** - booleano. Indica se sono presenti frame di corpo binari.

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Risposta tramite incontri

Per le risposte che superano 64 KB, la risposta DEVE essere recapitata tramite un socket di incontro. Se la richiesta supera i 64 KB e l'elemento `request` contiene solo il campo di indirizzo, è necessario anche stabilire un socket di incontro per ottenere l'elemento `request`. Dopo che un socket di incontro è stato stabilito, le risposte ai rispettivi client e alle successive richieste di tali client DEVONO essere recapitate tramite il socket di incontro mentre è attivo.

L'URL `address` nell'elemento `request` deve essere usato così com'è per stabilire il socket di incontro, ma contiene i parametri seguenti:

| Parametro      | Obbligatoria | DESCRIZIONE
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Sì      | Per accettare un socket, il parametro deve essere `sb-hc-action=request`

In caso di errore il servizio può rispondere come segue:

| Codice | Tipi di errore           | DESCRIZIONE
| ---- | --------------- | -----------------------------------
| 400  | Richiesta non valida | Azione non riconosciuta o URL non valido.
| 403  | Accesso negato       | L'URL è scaduto.
| 500  | Errore interno  | Si è verificato un errore nel servizio

 Dopo che la connessione è stata stabilita, il server arresta il WebSocket quando il socket HTTP del client si arresta o ha lo stato seguente:

| Stato Web Socket | DESCRIZIONE                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Il client mittente arresta la connessione.                                    |
| 1001      | Il percorso della connessione ibrida è stato eliminato o disabilitato.                        |
| 1008      | Il token di sicurezza è scaduto e i criteri di autorizzazione vengono quindi violati. |
| 1011      | Si è verificato un errore nel servizio.                                            |


#### <a name="listener-token-renewal"></a>Rinnovo del token del listener

Quando il token del listener sta per scadere, può essere sostituito inviando un messaggio in una cornice di testo al servizio tramite il canale di controllo stabilito. Il messaggio contiene un oggetto JSON denominato `renewToken`, che definisce la proprietà attuale seguente:

* **token**: token di accesso condiviso del bus di servizio codificato con URL valido per lo spazio dei nomi o la connessione ibrida che conferisce il diritto **Listen**.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Se la convalida del token non riesce, l'accesso viene negato e il servizio cloud chiude il WebSocket del canale di controllo con un errore. In caso contrario non vi è alcuna risposta.

| Stato Web Socket | DESCRIZIONE                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Il token di sicurezza è scaduto e i criteri di autorizzazione vengono quindi violati. |

### <a name="web-socket-connect-protocol"></a>Protocollo di connessione al WebSocket

Il protocollo per il mittente è di fatto identico a come viene stabilito un listener.
L'obiettivo è la massima trasparenza per il WebSocket end-to-end. L'indirizzo a cui connettersi è lo stesso del listener, ma l'"azione" è diversa e il token richiede un'autorizzazione diversa:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

_namespace-address_ è il nome di dominio completo dello spazio dei nomi di inoltro di Azure che ospita la connessione ibrida, in genere nel formato `{myname}.servicebus.windows.net`.

La richiesta può contenere intestazioni HTTP aggiuntive arbitrarie, incluse quelle definite dall'applicazione. Tutte le intestazioni specificate vengono trasmesse al listener e sono disponibili nell'oggetto `connectHeader` del messaggio di controllo **accept**.

Le opzioni dei parametri della stringa di query sono le seguenti:

| Param          | Obbligatorio? | DESCRIZIONE
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Sì       | Per il ruolo mittente, il parametro deve essere `sb-hc-action=connect`.
| `{path}`       | Sì       | (vedere il paragrafo seguente)
| `sb-hc-token`  | Sì\*     | Il listener deve fornire un token di accesso condiviso del bus di servizio codificato con URL valido per lo spazio dei nomi o la connessione ibrida che conferisce il diritto **Send**.
| `sb-hc-id`     | No         | ID facoltativo che consente la traccia diagnostica end-to-end ed è disponibile per il listener durante l'handshake accept.

 `{path}` è il percorso dello spazio dei nomi codificato con URL della connessione ibrida preconfigurata in cui registrare questo listener. L'espressione `path` può essere estesa con un suffisso e un'espressione di stringa di query per comunicare altre informazioni. Se la connessione ibrida è registrata nel percorso `hyco`, l'espressione `path` può essere `hyco/suffix?param=value&...` seguita dai parametri di stringa di query definiti qui. Un'espressione completa potrebbe quindi essere:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

L'espressione `path` viene trasmessa al listener nell'URI dell'indirizzo contenuto nel messaggio di controllo "accept".

Se la connessione WebSocket non riesce perché il percorso della connessione ibrida non è registrato oppure a causa di un token mancante o non valido o di un altro errore, il feedback dell'errore viene specificato usando il normale modello di feedback dello stato HTTP 1.1. La descrizione dello stato contiene un ID di traccia dell'errore che può essere comunicato al personale del supporto di Azure:

| Codice | Tipi di errore          | DESCRIZIONE
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Non trovato      | Il percorso della connessione ibrida non è valido o il formato dell'URL di base non è corretto.
| 401  | Non autorizzata   | Il token di sicurezza è mancante, non valido o non corretto.
| 403  | Accesso negato      | Il token di sicurezza non è valido per questo percorso e per questa azione.
| 500  | Errore interno | Si è verificato un errore nel servizio.

Se la connessione WebSocket viene intenzionalmente arrestata dal servizio dopo la configurazione iniziale, il motivo viene comunicato usando un codice di errore del protocollo WebSocket appropriato con un messaggio di errore descrittivo che include anche un ID di traccia.

| Stato Web Socket | DESCRIZIONE
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Il listener ha arrestato il socket.
| 1001      | Il percorso della connessione ibrida è stato eliminato o disabilitato.
| 1008      | Il token di sicurezza è scaduto e i criteri di autorizzazione vengono quindi violati.
| 1011      | Si è verificato un errore nel servizio.

### <a name="http-request-protocol"></a>Protocollo di richiesta HTTP

Il protocollo di richiesta HTTP consente le richieste HTTP arbitrarie, ad eccezione degli aggiornamenti di protocollo.
Alle richieste HTTP si fa riferimento all'indirizzo di runtime regolare dell'entità senza l'infisso $hc usato per i client WebSocket delle connessioni ibride.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

_namespace-address_ è il nome di dominio completo dello spazio dei nomi di inoltro di Azure che ospita la connessione ibrida, in genere nel formato `{myname}.servicebus.windows.net`.

La richiesta può contenere intestazioni HTTP aggiuntive arbitrarie, incluse quelle definite dall'applicazione. Tutte le intestazioni specificate, ad eccezione di quelle definite direttamente in RFC7230 (vedere il [messaggio di richiesta](#Request message)) vengono inviate al listener e possono essere trovate nell'oggetto `requestHeader` del messaggio **request**.

Le opzioni dei parametri della stringa di query sono le seguenti:

| Param          | Obbligatorio? | DESCRIZIONE
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Sì\*     | Il listener deve fornire un token di accesso condiviso del bus di servizio codificato con URL valido per lo spazio dei nomi o la connessione ibrida che conferisce il diritto **Send**.

Il token può essere spostato anche nell'intestazione HTTP `ServiceBusAuthorization` o `Authorization`. Il token può essere omesso se la connessione ibrida è configurata per consentire le richieste anonime.

Il servizio funziona effettivamente come un proxy, anche se non come un proxy HTTP reale, e di conseguenza aggiunge un'intestazione `Via` o prende nota dell'intestazione `Via` esistente conforme a [RFC7230, Sezione 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Il servizio aggiunge il nome host dello spazio dei nomi di inoltro all'elemento `Via`.

| Codice | Message  | DESCRIZIONE                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | La richiesta è stata gestita da almeno un listener.  |
| 202  | Accepted | La richiesta è stata accettata da almeno un listener. |

In caso di errore il servizio può rispondere come indicato di seguito. Per determinare se la risposta viene generata dal servizio o da quella del listener, verificare la presenza dell'intestazione `Via`. Se l'intestazione è presente, la risposta proviene dal listener.

| Codice | Tipi di errore           | DESCRIZIONE
| ---- | --------------- |--------- |
| 404  | Non trovato       | Il percorso della connessione ibrida non è valido o il formato dell'URL di base non è corretto.
| 401  | Non autorizzata    | Il token di sicurezza è mancante, non valido o non corretto.
| 403  | Accesso negato       | Il token di sicurezza non è valido per questo percorso e per questa azione.
| 500  | Errore interno  | Si è verificato un errore nel servizio.
| 503  | Gateway non valido     | La richiesta potrebbe non essere indirizzata ad alcun listener.
| 504  | Timeout gateway | La richiesta è stata indirizzata a un listener, ma il listener non ha confermato il ricevimento nel periodo di tempo necessario.

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti sull'inoltro](relay-faq.md)
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introduzione a Node](relay-hybrid-connections-node-get-started.md)
