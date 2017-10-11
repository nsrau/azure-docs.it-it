---
title: Panoramica delle API Node di inoltro di Azure | Microsoft Docs
description: Panoramica dell'API Node di inoltro
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 28526c05c7f364f0fcaaa362fc97857f850040ee
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---

# <a name="relay-hybrid-connections-node-api-overview"></a>Panoramica dell'API del pacchetto Node per Connessioni ibride di inoltro

## <a name="overview"></a>Panoramica

Il pacchetto Node [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) per le connessioni ibride di inoltro di Azure si basa sul pacchetto NPM ['ws'](https://www.npmjs.com/package/ws) e lo estende. Questo pacchetto permette di esportare nuovamente tutte le esportazioni del pacchetto di base e aggiunge nuove esportazioni che consentono l'integrazione con la funzionalità connessioni ibride del servizio di inoltro di Azure. 

Le applicazioni esistenti che usano `require('ws')` possono usare questo pacchetto con `require('hyco-ws')`, che consente scenari ibridi in cui un'applicazione può contemporaneamente restare in attesa di connessioni WebSocket in locale dall'interno del firewall e tramite connessioni ibride.
  
## <a name="documentation"></a>Documentazione

La documentazione delle API si trova [nel pacchetto 'ws' principale](https://github.com/websockets/ws/blob/master/doc/ws.md). Questo articolo descrive le differenze tra questo pacchetto e il pacchetto di base. 

La differenza principale tra il pacchetto di base e questo pacchetto 'hyco-ws' sta nell'aggiunta di una nuova classe server, esportata tramite `require('hyco-ws').RelayedServer`, e alcuni metodi helper.

### <a name="package-helper-methods"></a>Metodi helper del pacchetto

Nell'esportazione del pacchetto sono disponibili diversi metodi di utilità a cui è possibile fare riferimento come indicato di seguito:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

I metodi helper sono progettati per questo pacchetto, ma possono essere usati anche da un server Node per abilitare client di dispositivo o Web per la creazione di listener o mittenti. Il server ne fa uso passando a questi metodi URI che incorporano token di breve durata. È possibile usare questi URI anche con stack WebSocket comuni che non supportano l'impostazione di intestazioni HTTP per l'handshake WebSocket. L'incorporamento di token di autorizzazione nell'URI è supportato principalmente per scenari di utilizzo esterni alla libreria. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Crea un URI del listener della connessione ibrida di inoltro di Azure valido per il percorso e lo spazio dei nomi specificati. L'URI può quindi essere usato con la versione di inoltro della classe WebSocketServer.

- `namespaceName` (obbligatorio): nome di dominio completo dello spazio dei nomi di inoltro di Azure da usare.
- `path` (obbligatorio): nome di una connessione ibrida di inoltro di Azure esistente nello spazio dei nomi.
- `token` (facoltativo): token di accesso di inoltro rilasciato in precedenza incorporato nell'URI del listener. Vedere l'esempio seguente.
- `id` (facoltativo): identificatore di rilevamento che consente di tenere traccia della diagnostica end-to-end delle richieste.

Il valore `token` è facoltativo e deve essere usato solo quando non è possibile inviare intestazioni HTTP con l'handshake WebSocket, come nel caso dello stack WebSocket W3C.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Crea un URI di invio della connessione ibrida di inoltro di Azure valido per il percorso e lo spazio dei nomi specificati. L'URI può essere usato con qualsiasi client WebSocket.

- `namespaceName` (obbligatorio): nome di dominio completo dello spazio dei nomi di inoltro di Azure da usare.
- `path` (obbligatorio): nome di una connessione ibrida di inoltro di Azure esistente nello spazio dei nomi.
- `token` (facoltativo): token di accesso di inoltro rilasciato in precedenza incorporato nell'URI di invio. Vedere l'esempio seguente.
- `id` (facoltativo): identificatore di rilevamento che consente di tenere traccia della diagnostica end-to-end delle richieste.

Il valore `token` è facoltativo e deve essere usato solo quando non è possibile inviare intestazioni HTTP con l'handshake WebSocket, come nel caso dello stack WebSocket W3C.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Crea un token di firma di accesso condiviso di inoltro di Azure per l'URI di destinazione specificato, una regola di firma di accesso condiviso e la relativa chiave. Quest'ultima è valida per il numero di secondi specificato o per un'ora dall'istante corrente, se viene omesso l'argomento della scadenza.

- `uri` (obbligatorio): URI per il quale viene rilasciato il token. L'URI viene normalizzato affinché usi lo schema HTTP e le informazioni sulla stringa di query vengono rimosse.
- `ruleName` (obbligatorio): nome della regola di firma di accesso condiviso per l'entità rappresentata dall'URI specificato o per lo spazio dei nomi rappresentato dalla parte dell'URI relativa all'host.
- `key` (obbligatorio): chiave valida della regola di firma di accesso condiviso. 
- `expirationSeconds` (facoltativo): numero di secondi fino alla scadenza del token generato. Se non viene specificato, il valore predefinito è 1 ora (3600).

Il token rilasciato conferisce i diritti associati alla regola di firma di accesso condiviso specificata per la durata specificata.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

A livello funzionale, questo metodo equivale al metodo `createRelayToken` documentato in precedenza, ma restituisce il token aggiunto in modo corretto all'URI di input.

### <a name="class-wsrelayedserver"></a>Class ws.RelayedServer

La classe `hycows.RelayedServer` rappresenta un'alternativa alla classe `ws.Server`, che non rimane in ascolto sulla rete locale ma delega l'ascolto al servizio di inoltro di Azure.

Le due classi sono per lo più compatibili a livello di contratto. Ciò significa che un'applicazione esistente che usa la classe `ws.Server` può essere modificata facilmente per l'uso della versione inoltrata. Le differenze principali sono rappresentate dal costruttore e dalle opzioni disponibili.

#### <a name="constructor"></a>Costruttore  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

Il costruttore `RelayedServer` supporta un set di argomenti diverso rispetto a `Server`, perché non è un listener autonomo né può essere incorporato in un framework di listener HTTP esistente. Anche il numero di opzioni disponibili è ridotto, perché la gestione del WebSocket viene delegata in gran parte al servizio di inoltro.

Argomenti del costruttore:

- `server` (obbligatorio): URI completo del nome di una connessione ibrida su cui rimanere in ascolto, costruito in genere con il metodo helper WebSocket.createRelayListenUri().
- `token` (obbligatorio): questo argomento contiene una stringa di token rilasciata in precedenza o una funzione di callback che è possibile chiamare per ottenere la stringa di token. È preferibile usare l'opzione di callback, perché consente il rinnovo del token.

#### <a name="events"></a>Eventi

Le istanze di `RelayedServer` generano tre eventi che consentono di gestire le richieste in ingresso, stabilire le connessioni e rilevare le condizioni di errore. Per gestire i messaggi è necessario sottoscrivere l'evento `connect`. 

##### <a name="headers"></a>headers

```JavaScript 
function(headers)
```

L'evento `headers` viene generato immediatamente prima che venga accettata una connessione in ingresso, consentendo la modifica delle intestazioni da inviare al client. 

##### <a name="connection"></a>connessione

```JavaScript
function(socket)
```

Viene generato quando viene accettata una nuova connessione WebSocket. L'oggetto è di tipo `ws.WebSocket`, come nel pacchetto di base.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Se il server sottostante genera un errore, viene inoltrato qui.  

#### <a name="helpers"></a>Helper

Per semplificare l'avvio di un server inoltrato e sottoscrivere immediatamente le connessioni in ingresso, il pacchetto espone una semplice funzione helper che viene usata anche negli esempi, come indicato di seguito:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Questo metodo chiama il costruttore per creare una nuova istanza di RelayedServer e quindi sottoscrive il callback specificato nell'evento 'connection'.
 
##### <a name="relayedconnect"></a>relayedConnect

Con il semplice mirroring dell'helper `createRelayedServer` nella funzione, `relayedConnect` crea una connessione client e sottoscrive l'evento 'open' nel socket risultante.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul servizio di inoltro di Azure, vedere i collegamenti seguenti:
* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [API di inoltro disponibili](relay-api-overview.md)

