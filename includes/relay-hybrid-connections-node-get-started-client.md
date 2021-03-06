---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 9d4f7faa18ee7fae158afb42b8c42287e61dd103
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67180734"
---
### <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

Creare un nuovo file JavaScript denominato `sender.js`.

### <a name="add-the-relay-npm-package"></a>Aggiungere il pacchetto NPM di inoltro

Eseguire `npm install hyco-ws` dal prompt dei comandi di Node nella cartella del progetto.

### <a name="write-some-code-to-send-messages"></a>Scrivere codice per inviare messaggi

1. Aggiungere il valore `constants` seguente alla parte iniziale del file `sender.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Aggiungere le costanti seguenti al file `sender.js` per i dettagli della connessione ibrida. Sostituire i segnaposto tra parentesi con i valori ottenuti durante la creazione della connessione ibrida.
   
   1. `const ns`: spazio dei nomi dell'inoltro. Assicurarsi di usare il nome completo dello spazio dei nomi, ad esempio `{namespace}.servicebus.windows.net`.
   2. `const path`: nome della connessione ibrida.
   3. `const keyrule`: nome della chiave di firma di accesso condiviso.
   4. `const key`: valore della chiave di firma di accesso condiviso.

3. Aggiungere il codice seguente al file `sender.js`:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    Il file sender.js sarà simile al seguente:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

