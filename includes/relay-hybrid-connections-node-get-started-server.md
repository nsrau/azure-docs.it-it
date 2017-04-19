### <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js
* Creare un nuovo file JavaScript denominato `listener.js`.

### <a name="add-the-relay-npm-package"></a>Aggiungere il pacchetto NPM di inoltro
* Eseguire `npm install hyco-ws` dal prompt dei comandi di Node nella cartella del progetto.

### <a name="write-some-code-to-receive-messages"></a>Scrivere codice per ricevere messaggi
1. Aggiungere il valore `constant` seguente alla parte iniziale del file `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Aggiungere il valore `constants` di inoltro seguente al file `listener.js` per i dettagli della connessione ibrida. Sostituire i segnaposto tra parentesi con i valori specifici ottenuti durante la creazione della connessione ibrida.
   
   1. `const ns`: spazio dei nomi di inoltro (usare FQDN, ad esempio `{namespace}.servicebus.windows.net`)
   2. `const path`: nome della connessione ibrida
   3. `const keyrule`: nome della chiave di firma di accesso condiviso
   4. `const key`: valore della chiave di firma di accesso condiviso
3. Aggiungere il codice seguente al file `listener.js`:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Ecco l'aspetto previsto per il file listener.js:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

