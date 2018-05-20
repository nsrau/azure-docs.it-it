---
title: File di inclusione
description: File di inclusione
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 04cb694f556d1b53344c0fd95947a258170c4f88
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
---
### <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

Se è stata disabilitata l'opzione "Richiede l'autorizzazione client" durante la creazione del servizio di inoltro, è possibile inviare le richieste all'URL di Connessioni ibride con qualsiasi browser. Per l'accesso a endpoint protetti, è necessario creare e passare un token nell'intestazione `ServiceBusAuthorization`, come illustrato di seguito.

Per iniziare, creare un nuovo file JavaScript denominato `sender.js`.

### <a name="add-the-relay-npm-package"></a>Aggiungere il pacchetto NPM di inoltro

Eseguire `npm install hyco-https` dal prompt dei comandi di Node nella cartella del progetto. Questo pacchetto importa anche il normale pacchetto `https`. Per il lato client, la differenza principale è che il pacchetto fornisce funzioni per costruire gli URI e i token di inoltro.

### <a name="write-some-code-to-send-messages"></a>Scrivere codice per inviare messaggi

1. Aggiungere il valore `constants` seguente alla parte iniziale del file `sender.js`.
   
    ```js
    const https = require('hyco-https');
    ```

2. Aggiungere le costanti seguenti al file `sender.js` per i dettagli della connessione ibrida. Sostituire i segnaposto tra parentesi con i valori ottenuti durante la creazione della connessione ibrida.
   
   1. `const ns`: spazio dei nomi dell'inoltro. Assicurarsi di usare il nome completo dello spazio dei nomi, ad esempio `{namespace}.servicebus.windows.net`.
   2. `const path`: nome della connessione ibrida.
   3. `const keyrule`: nome della chiave di firma di accesso condiviso.
   4. `const key`: valore della chiave di firma di accesso condiviso.

3. Aggiungere il codice seguente al file `sender.js`. Si noterà che il codice non presenta differenze significative rispetto al normale uso del client HTTPS Node.js. Viene solo aggiunta l'intestazione di autorizzazione.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    Il file sender.js sarà simile al seguente:
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

