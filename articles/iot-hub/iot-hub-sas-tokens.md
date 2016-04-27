<properties
 pageTitle="Come generare i token di sicurezza hub IoT | Microsoft Azure"
 description="Descrizione dei diversi tipi di token di sicurezza usati dall'hub IoT e come crearli."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="03/02/2016"
 ms.author="elioda"/>

# Uso dei token di sicurezza hub IoT

Hub IoT usa i token di sicurezza per autenticare i dispositivi e i servizi ed evitare l'invio in rete delle chiavi. Inoltre, i token di sicurezza hanno una validità limitata in termini di tempo e portata. Gli [SDK di hub IoT Azure][lnk-apis-sdks] generano automaticamente i token senza richiedere alcuna configurazione speciale. In alcuni scenari, tuttavia, è necessario che l'utente generi e usi direttamente i token di sicurezza. Tra questi citiamo l'uso diretto delle superfici AMQP, MQTT e HTTP o l'implementazione del modelli di servizio token, come descritto nell'articolo relativo allo [hub IoT][lnk-guidance-security].

L'articolo illustra:

* Il formato dei token di sicurezza e come crearli.
* I casi principali di utilizzo per i token di sicurezza per autenticare i dispositivi e i servizi back-end.

## Formato del token di sicurezza
I token di sicurezza consentono di concedere a dispositivi e servizi l'accesso con limite temporale a funzionalità specifiche dell'hub IoT. Per garantire che possano connettersi esclusivamente servizi e dispositivi autorizzati , i token di sicurezza devono essere firmati con una chiave criteri di accesso condiviso o con una chiave simmetrica memorizzata con un'identità dispositivo nel registro identità.

Un token firmato con una chiave criteri di accesso condiviso concede l'accesso a tutte le funzionalità associate alle autorizzazioni dei criteri di accesso condiviso. Fare riferimento alla [sezione Sicurezza della Guida per gli sviluppatori dell'hub IoT][lnk-devguide-security]. D'altro canto, un token firmato con una chiave simmetrica dell'identità dispositivo concede solo l'autorizzazione **DeviceConnect** per l'identità del dispositivo associato.

Il token di sicurezza ha il formato seguente:

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Questi sono i valori previsti:

| Valore | Descrizione |
| ----- | ----------- |
| {signature} | Stringa della firma HMAC-SHA256 nel formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: la chiave viene decodificata dalla codifica Base64 e usata come chiave per eseguire il calcolo di HMAC-SHA256. |
| {resourceURI} | Prefisso URI (per segmento) degli endpoint a cui è possibile accedere tramite questo token e che inizia con il nome host dell'hub IoT senza il protocollo. Ad esempio, `myHub.azure-devices.net/devices/device1` |
| {expiry} | Stringhe UTF8 per il numero di secondi trascorsi dalle 00:00:00 UTC dell'1 gennaio 1970. |
| {URL-encoded-resourceURI} | Codifica URL con lettere minuscole dell'URI della risorsa con lettere minuscole |
| {policyName} | Nome del criterio di accesso condiviso a cui fa riferimento il token. Assente nel caso in cui i token facciano riferimento a credenziali del registro dei dispositivi. |

**Nota sul prefisso**: il prefisso dell'URI viene calcolato in base al segmento e non in base al carattere. Ad esempio `/a/b` è un prefisso per `/a/b/c` ma non per `/a/bc`.

Si tratta di una funzione Node che calcola il token dagli input `resourceUri, signingKey, policyName, expiresInMins`. Nelle sezioni successive verrà illustrato nel dettaglio come inizializzare gli input diversi per i casi di utilizzo di token diversi.

    var crypto = require('crypto');

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // using crypto
        var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
        const hmac = crypto.createHmac('sha256', decodedPassword);
        hmac.update(toSign);
        var base64signature = hmac.digest('base64');
        var base64UriEncoded = encodeURIComponent(base64signature);

        // construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };

> [AZURE.NOTE] Poiché la validità temporale del token viene verificata sui computer hub IoT, è importante che lo sfasamento dell'orologio del computer che genera il token sia minimo.

## Uso dei token di firma di accesso condiviso come dispositivo

Esistono due modi per ottenere le autorizzazioni **DeviceConnect** su hub IoT con i token di sicurezza: con una chiave di identità dispositivo o una chiave criteri di accesso condiviso.

Inoltre, è importante notare che tutte le funzionalità accessibili dai dispositivi vengono esposte, per impostazione predefinita, sugli endpoint con prefisso `/devices/{deviceId}`.

> [AZURE.IMPORTANT] L'unico modo di cui dispone l'hub IoT per autenticare un dispositivo specifico è tramite la chiave simmetrica identità dispositivo. Nei casi in cui si acceda alle funzionalità del dispositivo tramite criteri di accesso condiviso, la soluzione deve considerare il componente che emette il token di sicurezza come sottocomponente attendibile.

Gli endpoint per il dispositivo sono, indipendentemente dal protocollo:

| Endpoint | Funzionalità |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Invio di messaggi da dispositivo a cloud. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Ricezione di messaggi da cloud a dispositivo. |

### Uso di una chiave simmetrica nel registro identità

Quando si usa una chiave simmetrica dell'identità dispositivo per generare un token, l'elemento policyName (`skn`) del token viene omesso.

Ad esempio, un token creato per accedere a tutte le funzionalità del dispositivo deve avere i seguenti parametri:

* URI risorsa: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* Chiave di firma: qualsiasi chiave simmetrica per l'identità `{device id}`,
* Nessun nome di criterio,
* Qualsiasi ora di scadenza.

Un esempio di utilizzo della funzione Node precedente sarebbe:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

Il risultato, che concede l'accesso a tutte le funzionalità per device1, sarà:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] È possibile generare un token di sicurezza tramite lo strumento .NET [Device Explorer][lnk-device-explorer].

### Uso di criteri di accesso condiviso

Quando si crea un token da criteri di accesso condiviso, il campo del nome del criterio `skn` deve essere impostato sul nome del criterio usato. È inoltre necessario che i criteri concedano l'autorizzazione **DeviceConnect**.

I due scenari principali per l'uso di criteri di accesso condiviso per accedere alla funzionalità dei dispositivi sono:

* [gateway del protocollo cloud][lnk-azure-protocol-gateway],
* [servizi token][lnk-devguide-security] tramite i quali implementare schemi di autenticazione personalizzati.

Poiché i criteri di accesso condiviso possono potenzialmente autorizzare la connessione a qualsiasi dispositivo, in fase di creazione dei token di sicurezza è importante usare l'URI risorsa corretto. Ciò è particolarmente importante per i servizi token, che devono definire l'ambito del token a un dispositivo specifico usando l'URI risorsa. Questo punto è meno importante per i gateway di protocollo, in quanto già filtrano il traffico per tutti i dispositivi.

Ad esempio, un servizio token che usa i criteri di accesso condiviso già esistente denominato **device** creerebbe un token con i parametri seguenti:

* URI risorsa: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* Chiave di firma: una delle chiavi dell'elemento plocy `device`,
* Nome criterio: `device`,
* Qualsiasi ora di scadenza.

Un esempio di utilizzo della funzione Node precedente sarebbe:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Il risultato, che concede l'accesso a tutte le funzionalità per device1, sarà:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Si può usare lo stesso token per un gateway di protocollo per tutti i dispositivi semplicemente impostando l'URI risorsa su `myhub.azure-devices.net/devices`.

## Uso di token di sicurezza da componenti del servizio

I componenti del servizio possono generare i token di sicurezza solo tramite i criteri di accesso condiviso che concedono le autorizzazioni appropriate, come illustrato nella [sezione Sicurezza della Guida per gli sviluppatori dell'hub IoT][lnk-devguide-security].

Queste sono le funzioni del servizio esposte sugli endpoint:

| Endpoint | Funzionalità |
| ----- | ----------- |
| `{iot hub host name}/devices` | Creazione, aggiornamento, recupero ed eliminazione delle identità dispositivo. |
| `{iot hub host name}/messages/events` | Ricezione di messaggi da dispositivo a cloud. |
| `{iot hub host name}/servicebound/feedback` | Ricezione di feedback per messaggi da cloud a dispositivo. |
| `{iot hub host name}/devicebound` | Invio di messaggi da cloud a dispositivo. |

Ad esempio, un servizio che usa il criterio di accesso condiviso già esistente denominato **registryRead** creerebbe un token con i parametri seguenti:

* URI risorsa: `{IoT hub name}.azure-devices.net/devices`,
* Chiave di firma: una delle chiavi dell'elemento plocy `registryRead`,
* Nome criterio: `registryRead`,
* Qualsiasi ora di scadenza.

    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Il risultato, che concede l'accesso in lettura a tutte le identità dispositivo, sarà:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead


[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

<!---HONumber=AcomDC_0413_2016-->