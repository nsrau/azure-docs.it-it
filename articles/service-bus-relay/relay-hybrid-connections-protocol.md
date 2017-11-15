---
title: Guida al protocollo per le connessioni ibride di inoltro di Azure | Microsoft Docs
description: Guida al protocollo per le connessioni ibride di inoltro di Azure.
services: service-bus-relay
documentationcenter: na
author: clemensv
manager: timlt
editor: 
ms.assetid: 149f980c-3702-4805-8069-5321275bc3e8
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 9d015678dbd99b8d978c2c8200b36bf51cac8893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protocollo per le connessioni ibride di inoltro di Azure
L'inoltro di Azure è una delle funzionalità chiave di base della piattaforma Bus di servizio di Azure. La nuova funzionalità *Connessioni ibride* di inoltro è un'evoluzione sicura del protocollo aperto basata su HTTP e WebSocket. Sostituisce la funzionalità precedente, comunemente denominata *Servizi BizTalk*, che è basata su un protocollo di proprietà. L'integrazione di Connessioni ibride nei servizi app di Azure continuerà a funzionare così com'è.

Connessioni ibride permette di stabilire una comunicazione bidirezionale con flussi binari tra due applicazioni di rete, di cui una o entrambe le parti risiedono dietro NAT o firewall. Questo articolo descrive le interazioni lato client con l'inoltro di Connessioni ibride per la connessione dei client nei ruoli listener e mittente e come i listener accettano nuove connessioni.

## <a name="interaction-model"></a>Modello di interazione
L'inoltro di Connessioni ibride connette due parti fornendo un punto di incontro nel cloud di Azure che entrambe le parti possono individuare e a cui possono connettersi dalla rispettiva rete. Tale punto di incontro è detto "connessione ibrida" in questo e negli altri documenti, nelle API e anche nel Portale di Azure. L'endpoint di servizio di Connessioni ibride viene chiamato "servizio" nella parte restante di questo articolo. Il modello di interazione si basa sulla nomenclatura stabilita da diverse altre API di rete.

È presente un listener che prima indica la conformità alla gestione delle connessioni in ingresso e successivamente le accetta quando arrivano. Sull'altro lato è presente un client di connessione che si connette al listener, aspettando che la connessione venga accettata per stabilire un percorso di comunicazione bidirezionale.
"Connettersi", "essere in ascolto", "accettare" sono gli stessi termini usati nella maggior parte delle API socket.

In un modello di comunicazione di inoltro entrambe le parti creano connessioni in uscita verso un endpoint di servizio, rendendo il "listener" anche un "client" nel linguaggio comune e causando altre sovrapposizioni terminologiche. La terminologia esatta usata per Connessioni ibride è quindi la seguente:

I programmi su entrambi i lati di una connessione sono detti "client", perché sono i client del servizio. Il client che attende e accetta le connessioni è il "listener", che è anche nel "ruolo listener". Il client che avvia una nuova connessione verso un listener tramite il servizio è detto "mittente", che è anche nel "ruolo mittente".

### <a name="listener-interactions"></a>Interazioni del listener
Il listener ha quattro interazioni con il servizio. Tutti i dettagli sono descritti più avanti in questo articolo nella sezione di riferimento.

#### <a name="listen"></a>Attesa
Un listener, per indicare al servizio che è pronto ad accettare le connessioni, crea una connessione WebSocket in uscita. L'handshake della connessione trasporta il nome di una connessione ibrida configurata nello spazio dei nomi dell'inoltro e un token di sicurezza che conferisce il diritto di "ascoltare" a tale nome.
Quando il WebSocket viene accettato dal servizio, la registrazione è completa e il WebSocket stabilito viene mantenuto attivo come "canale di controllo" per abilitare tutte le interazioni successive. Il servizio consente fino a 25 listener simultanei in una connessione ibrida. In presenza di due o più listener attivi, le connessioni in ingresso vengono bilanciate tra di essi in ordine casuale. Non è garantita una distribuzione equa.

#### <a name="accept"></a>Accept
Quando un mittente apre una nuova connessione nel servizio, il servizio sceglie uno dei listener attivi nella connessione ibrida e gli invia una notifica. La notifica viene inviata al listener tramite il canale di controllo aperto come messaggio JSON contenente l'URL dell'endpoint del WebSocket a cui il listener deve connettersi per accettare la connessione.

L'URL può e deve essere usato direttamente dal listener senza operazioni aggiuntive.
Le informazioni codificate sono valide solo per un breve periodo, essenzialmente finché il mittente è disposto ad attendere che venga stabilita una connessione end-to-end e comunque per un massimo di 30 secondi. L'URL può essere usato solo per tentativo di connessione riuscito. Non appena viene stabilita la connessione WebSocket con l'URL di incontro, tutte le altre attività in questo WebSocket vengono inoltrate da e verso il mittente, senza interventi o interpretazioni da parte del servizio.

#### <a name="renew"></a>Renew
Il token di sicurezza che deve essere usato per registrare il listener e mantenere il canale di controllo può scadere mentre il listener è attivo. La scadenza del token non ha effetto sulle connessioni in corso, ma il canale di controllo viene rimosso dal servizio al momento della scadenza o poco dopo. L'operazione "renew" è un messaggio JSON che il listener può inviare per sostituire il token associato al canale di controllo che potrà quindi essere mantenuto per lunghi periodi.

#### <a name="ping"></a>Ping
Se il canale di controllo rimane inattivo a lungo, gli intermediari lungo il percorso, ad esempio servizi di bilanciamento del carico o NAT, potrebbero rimuovere la connessione TCP. L'operazione "ping" evita questo problema inviando nel canale una piccola quantità di dati che ricorda a tutti gli elementi nella route di rete che la connessione deve restare attiva, oltre a fungere da test dello stato attivo per il listener. Se il ping non riesce, il canale di controllo deve essere considerato non utilizzabile e il listener deve riconnettersi.

### <a name="sender-interaction"></a>Interazione del mittente
Il mittente ha una sola interazione con il servizio, la connessione.

#### <a name="connect"></a>Connettere
L'operazione "connect" apre un WebSocket nel servizio, fornendo il nome della connessione ibrida e un token di sicurezza (facoltativo, ma richiesto per impostazione predefinita) che conferisce l'autorizzazione "Send" nella stringa di query. Il servizio interagisce quindi con il listener nel modo descritto in precedenza e il listener crea una connessione di incontro che viene unita a questo WebSocket. Dopo che il WebSocket è stato accettato, tutte le altre interazioni nel WebSocket avvengono quindi con un listener connesso.

### <a name="interaction-summary"></a>Riepilogo delle interazioni
Il risultato di questo modello di interazione è che il client mittente esce dall'handshake con un WebSocket "pulito" che è connesso a un listener e non richiede altri preamboli o preparazioni. Questo modello consente in pratica alle implementazioni di client WebSocket esistenti di usare subito il servizio Connessioni ibride specificando un URL correttamente costruito nel livello del client WebSocket.

Anche il WebSocket della connessione di incontro che il listener ottiene tramite l'interazione accept è pulito e può essere assegnato a qualsiasi implementazione di server WebSocket esistente con una minima astrazione aggiuntiva che distingue tra operazioni "accept" sui listener di rete locale del framework e operazioni "accept" remote di Connessioni ibride.

## <a name="protocol-reference"></a>Riferimento al protocollo

Questa sezione descrive i dettagli delle interazioni del protocollo descritte sopra.

Tutte le connessioni WebSocket vengono stabilite sulla porta 443 come aggiornamento da HTTPS 1.1, che è in genere un'astrazione di alcune API o framework del WebSocket. La presente descrizione è neutra dal punto di vista dell'implementazione e non suggerisce un framework specifico.

### <a name="listener-protocol"></a>Protocollo del listener
Il protocollo del listener è costituito da due comandi di connessione e da tre operazioni messaggio.

#### <a name="listener-control-channel-connection"></a>Connessione del canale di controllo del listener
Il canale di controllo viene aperto con la creazione di una connessione WebSocket a:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...
```

`namespace-address` è il nome di dominio completo dello spazio dei nomi di inoltro di Azure che ospita la connessione ibrida, in genere nel formato `{myname}.servicebus.windows.net`.

Le opzioni dei parametri della stringa di query sono le seguenti.

| Parametro | Obbligatorio | Descrizione |
| --- | --- | --- |
| `sb-hc-action` |Sì |Per il ruolo listener, il parametro deve essere **sb-hc-action=listen** |
| `{path}` |Sì |Il percorso dello spazio dei nomi codificato con URL della connessione ibrida preconfigurata in cui registrare questo listener. Questa espressione viene aggiunta alla parte del percorso `$hc/` fissa. |
| `sb-hc-token` |Sì\* |Il listener deve fornire un token di accesso condiviso del bus di servizio codificato con URL valido per lo spazio dei nomi o la connessione ibrida che conferisce il diritto **Listen**. |
| `sb-hc-id` |No |Questo ID facoltativo fornito dal client consente la traccia diagnostica end-to-end. |

Se la connessione WebSocket non riesce perché il percorso della connessione ibrida non è registrato oppure a causa di un token mancante o non valido o di un altro errore, il feedback dell'errore viene specificato usando il normale modello di feedback dello stato HTTP 1.1. La descrizione dello stato contiene un ID di traccia dell'errore che può essere comunicato al personale del supporto di Azure:

| Codice | Errore | Description |
| --- | --- | --- |
| 404 |Non trovato |Il percorso della connessione ibrida non è valido o il formato dell'URL di base non è corretto. |
| 401 |Non autorizzata |Il token di sicurezza è mancante, non valido o non corretto. |
| 403 |Accesso negato |Il token di sicurezza non è valido per questo percorso per questa azione. |
| 500 |Errore interno |Si è verificato un errore nel servizio. |

Se la connessione WebSocket viene intenzionalmente arrestata dal servizio dopo la configurazione iniziale, il motivo viene comunicato usando un codice errore del protocollo WebSocket appropriato con un messaggio di errore descrittivo che include anche un ID di traccia. Il servizio non arresterà il canale di controllo senza riscontrare una condizione di errore. Le chiusure normali sono controllate dal client.

| Stato Web Socket | Description |
| --- | --- |
| 1001 |Il percorso della connessione ibrida è stato eliminato o disabilitato. |
| 1008 |Il token di sicurezza è scaduto e i criteri di autorizzazione vengono quindi violati. |
| 1011 |Si è verificato un errore nel servizio. |

### <a name="accept-handshake"></a>Handshake accept
La notifica "accept" viene inviata dal servizio al listener tramite il canale di controllo stabilito in precedenza come messaggio JSON in una cornice di testo del WebSocket. Non sono previste risposte a questo messaggio.

Il messaggio contiene un oggetto JSON denominato "accept", che definisce le proprietà attuali seguenti:

* **address**: stringa dell'URL da usare per stabilire il WebSocket al servizio per accettare una connessione in ingresso.
* **id**: identificatore univoco per questa connessione. Se l'ID è stato fornito dal client mittente, si tratta del valore fornito dal mittente. In caso contrario, è un valore generato dal sistema.
* **connectHeaders**: tutte le intestazioni HTTP fornite all'endpoint di inoltro dal mittente, che include anche le intestazioni Sec-WebSocket-Protocol e Sec-WebSocket-Extensions.

#### <a name="accept-message"></a>Messaggio accept

```json
{                                                           
    "accept" : {
        "address" : "wss://168.61.148.205:443/$hc/{path}?..."    
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

#### <a name="accepting-the-socket"></a>Accettazione del socket
Per accettare, il listener stabilisce una connessione WebSocket all'indirizzo specificato.

Se il messaggio "accept" contiene un'intestazione `Sec-WebSocket-Protocol`, è previsto che il listener accetti il WebSocket solo se supporta tale protocollo. Imposta inoltre l'intestazione quando viene definito il WebSocket.

Lo stesso vale per l'intestazione `Sec-WebSocket-Extensions`. Se il framework supporta un'estensione, deve impostare l'intestazione sulla risposta lato server dell'handshake `Sec-WebSocket-Extensions` obbligatorio per l'estensione.

L'URL deve essere usato così com'è per stabilire il socket di accettazione, ma contiene i parametri seguenti:

| Parametro | Obbligatorio | Descrizione |
| --- | --- | --- |
| `sb-hc-action` |Sì |Per accettare un socket, il parametro deve essere `sb-hc-action=accept` |
| `{path}` |Sì |(vedere il paragrafo seguente) |
| `sb-hc-id` |No |Vedere la descrizione precedente di **id**. |

`{path}` è il percorso dello spazio dei nomi codificato con URL della connessione ibrida preconfigurata in cui registrare questo listener. Questa espressione viene aggiunta alla parte del percorso `$hc/` fissa. 

L'espressione `path` può essere estesa aggiungendo al nome registrato una barra, un suffisso e un'espressione di stringa di query. Il client mittente può in questo modo trasmettere gli argomenti di invio al listener di accettazione quando non è possibile includere le intestazioni HTTP. Il framework del listener dovrebbe analizzare la parte fissa del percorso e il nome registrato dal percorso, quindi rendere disponibile la parte restante (possibilmente senza argomenti di stringa di query con prefisso `sb-`) all'applicazione perché decida se accettare la connessione.

Per altre informazioni, vedere la sezione "Protocollo per il mittente" che segue.

In caso di errore il servizio può rispondere come segue:

| Codice | Errore | Description |
| --- | --- | --- |
| 403 |Accesso negato |URL non valido. |
| 500 |Errore interno |Si è verificato un errore nel servizio |

Dopo che la connessione è stata stabilita, il server arresta il WebSocket quando il WebSocket mittente si arresta o ha lo stato seguente:

| Stato Web Socket | Description |
| --- | --- |
| 1001 |Il client mittente arresta la connessione. |
| 1001 |Il percorso della connessione ibrida è stato eliminato o disabilitato. |
| 1008 |Il token di sicurezza è scaduto e i criteri di autorizzazione vengono quindi violati. |
| 1011 |Si è verificato un errore nel servizio. |

#### <a name="rejecting-the-socket"></a>Rifiuto del socket
Per rifiutare il socket dopo avere esaminato il messaggio "accept", è necessario un handshake simile in modo che il codice di stato e la descrizione dello stato che comunica il motivo del rifiuto possano tornare al mittente.

La scelta di progettazione del protocollo in questo caso prevede l'uso di un handshake WebSocket (progettato per restituire uno stato di errore definito) in modo che le implementazioni del client listener possano continuare a basarsi su un client WebSocket e non sia necessario impiegare un altro client HTTP di base.

Per rifiutare il socket, il client accetta l'URI dell'indirizzo dal messaggio "accept" e vi aggiunge due parametri di stringa di query, come indicato di seguito:

| Param | Obbligatorio | Descrizione |
| --- | --- | --- |
| statusCode |Sì |Codice di stato HTTP numerico. |
| statusDescription |Sì |Motivo leggibile del rifiuto. |

L'URI risultante viene quindi usato per stabilire una connessione WebSocket.

Se completato correttamente, questo handshake non riuscirà di proposito con il codice di errore HTTP 410, perché non è stato stabilito alcun WebSocket. Se si verificano problemi, i codici seguenti descrivono l'errore:

| Codice | Errore | Description |
| --- | --- | --- |
| 403 |Accesso negato |URL non valido. |
| 500 |Errore interno |Si è verificato un errore nel servizio. |

### <a name="listener-token-renewal"></a>Rinnovo del token del listener
Quando il token del listener sta per scadere, può essere sostituito inviando un messaggio in una cornice di testo al servizio tramite il canale di controllo stabilito. Il messaggio contiene un oggetto JSON denominato `renewToken`, che definisce la proprietà attuale seguente:

* **token**: token di accesso condiviso del bus di servizio codificato con URL valido per lo spazio dei nomi o la connessione ibrida che conferisce il diritto **Listen**.

#### <a name="renewtoken-message"></a>Messaggio renewToken

```json
{                                                                                                                                                                        
    "renewToken" : {                                                                                                                                                      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"  
    }                                                                                                                                                                     
}
```

Se la convalida del token non riesce, l'accesso viene negato e il servizio cloud chiude il WebSocket del canale di controllo con un errore. In caso contrario non vi è alcuna risposta.

| Stato Web Socket | Description |
| --- | --- |
| 1008 |Il token di sicurezza è scaduto e i criteri di autorizzazione vengono quindi violati. |

## <a name="sender-protocol"></a>Protocollo per il mittente
Il protocollo per il mittente è di fatto identico a come viene stabilito un listener.
L'obiettivo è la massima trasparenza per il WebSocket end-to-end. L'indirizzo a cui connettersi è lo stesso del listener, ma l'"azione" è diversa e il token richiede un'autorizzazione diversa:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

*namespace-address* è il nome di dominio completo dello spazio dei nomi di inoltro di Azure che ospita la connessione ibrida, in genere nel formato `{myname}.servicebus.windows.net`.

La richiesta può contenere intestazioni HTTP aggiuntive arbitrarie, incluse quelle definite dall'applicazione. Tutte le intestazioni specificate vengono trasmesse al listener e sono disponibili nell'oggetto `connectHeader` del messaggio di controllo **accept**.

Le opzioni dei parametri della stringa di query sono le seguenti:

| Param | Obbligatorio? | Descrizione |
| --- | --- | --- |
| `sb-hc-action` |Sì |Per il ruolo mittente, il parametro deve essere `action=connect`. |
| `{path}` |Sì |(vedere il paragrafo seguente) |
| `sb-hc-token` |Sì\* |Il listener deve fornire un token di accesso condiviso del bus di servizio codificato con URL valido per lo spazio dei nomi o la connessione ibrida che conferisce il diritto **Send**. |
| `sb-hc-id` |No |ID facoltativo che consente la traccia diagnostica end-to-end ed è disponibile per il listener durante l'handshake accept. |

`{path}` è il percorso dello spazio dei nomi codificato con URL della connessione ibrida preconfigurata in cui registrare questo listener. L'espressione `path` può essere estesa con un suffisso e un'espressione di stringa di query per comunicare altre informazioni. Se la connessione ibrida è registrata nel percorso `hyco`, l'espressione `path` può essere `hyco/suffix?param=value&...` seguita dai parametri di stringa di query definiti qui. Un'espressione completa potrebbe quindi essere:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

L'espressione `path` viene trasmessa al listener nell'URI dell'indirizzo contenuto nel messaggio di controllo "accept".

Se la connessione WebSocket non riesce perché il percorso della connessione ibrida non è registrato oppure a causa di un token mancante o non valido o di un altro errore, il feedback dell'errore viene specificato usando il normale modello di feedback dello stato HTTP 1.1. La descrizione dello stato contiene un ID di traccia dell'errore che può essere comunicato al personale del supporto di Azure:

| Codice | Errore | Description |
| --- | --- | --- |
| 404 |Non trovato |Il percorso della connessione ibrida non è valido o il formato dell'URL di base non è corretto. |
| 401 |Non autorizzata |Il token di sicurezza è mancante, non valido o non corretto. |
| 403 |Accesso negato |Il token di sicurezza non è valido per questo percorso e per questa azione. |
| 500 |Errore interno |Si è verificato un errore nel servizio. |

Se la connessione WebSocket viene intenzionalmente arrestata dal servizio dopo la configurazione iniziale, il motivo viene comunicato usando un codice di errore del protocollo WebSocket appropriato con un messaggio di errore descrittivo che include anche un ID di traccia.

| Stato Web Socket | Description |
| --- | --- |
| 1000 |Il listener ha arrestato il socket. |
| 1001 |Il percorso della connessione ibrida è stato eliminato o disabilitato. |
| 1008 |Il token di sicurezza è scaduto e i criteri di autorizzazione vengono quindi violati. |
| 1011 |Si è verificato un errore nel servizio. |

## <a name="next-steps"></a>Passaggi successivi
* [Domande frequenti sull'inoltro](relay-faq.md)
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introduzione a Node](relay-hybrid-connections-node-get-started.md)

