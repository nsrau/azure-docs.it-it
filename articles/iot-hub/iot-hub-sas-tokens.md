<properties
 pageTitle="Come generare i token di sicurezza hub IoT | Microsoft Azure"
 description="Descrizione dei diversi tipi di token di sicurezza (come i token di firma di accesso condiviso e X.509) usati dall'hub IoT e come crearli."
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
 ms.date="06/07/2016"
 ms.author="elioda"/>

# Usare i token di sicurezza hub IoT e i certificati X.509

Hub IoT usa i token di sicurezza per autenticare i dispositivi e i servizi ed evitare l'invio in rete delle chiavi. Inoltre, i token di sicurezza hanno una validità limitata in termini di tempo e portata. Gli [SDK di hub IoT Azure][lnk-apis-sdks] generano automaticamente i token senza richiedere alcuna configurazione speciale. In alcuni scenari, tuttavia, è necessario che l'utente generi e usi direttamente i token di sicurezza. Tra questi citiamo l'uso diretto delle superfici MQTT, AMQP o HTTP o l'implementazione del modello di servizio token, come descritto nell'articolo delle [indicazioni sull'hub IoT][lnk-guidance-security].

Hub IoT consente inoltre ai dispositivi di autenticarsi con hub IoT usando certificati X.509. Hub IoT supporta l'autenticazione basata su X.509 per i dispositivi su AMQP, AMQP sui protocolli WebSockets e HTTP.

L'articolo illustra:

* Il formato dei token di sicurezza e come crearli.
* I casi principali di utilizzo per i token di sicurezza per autenticare i dispositivi e i servizi back-end.
* Certificati X.509 supportati per l'autenticazione dei dispositivi.
* Processo di registrazione di un certificato client X.509 associato a un dispositivo specifico.
* Flusso di runtime tra dispositivo e hub IoT mediante un certificato client X.509 per l'autenticazione.


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
 
 Ai fini del confronto, il codice Python equivalente è:
 
    from base64 import b64encode, b64decode
    from hashlib import sha256
    from hmac import HMAC
    from urllib import urlencode
    
    def generate_sas_token(uri, key, policy_name='device', expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % (uri, int(ttl))
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())
     
        return 'SharedAccessSignature ' + urlencode({
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl)),
            'skn': policy_name
        })

> [AZURE.NOTE] Poiché la validità temporale del token viene verificata sui computer hub IoT, è importante che lo sfasamento dell'orologio del computer che genera il token sia minimo.

## Usare token di firma di accesso condiviso come dispositivo

Esistono due modi per ottenere le autorizzazioni **DeviceConnect** su hub IoT con i token di sicurezza: con una chiave di identità dispositivo o una chiave criteri di accesso condiviso.

È anche importante notare che tutte le funzionalità accessibili dai dispositivi vengono esposte, per impostazione predefinita, sugli endpoint con prefisso `/devices/{deviceId}`.

> [AZURE.IMPORTANT] L'unico modo di cui dispone l'hub IoT per autenticare un dispositivo specifico è tramite la chiave simmetrica identità dispositivo. Nei casi in cui si acceda alle funzionalità del dispositivo tramite criteri di accesso condiviso, la soluzione deve considerare il componente che emette il token di sicurezza come sottocomponente attendibile.

Gli endpoint per il dispositivo sono, indipendentemente dal protocollo:

| Endpoint | Funzionalità |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events`   | Invio di messaggi da dispositivo a cloud. |
| `{iot hub host name}/devices/{deviceId}/devicebound`   | Ricezione di messaggi da cloud a dispositivo. |

### Usare una chiave simmetrica nel registro identità

Quando si usa una chiave simmetrica dell'identità dispositivo per generare un token, l'elemento policyName (`skn`) del token viene omesso.

Ad esempio, un token creato per accedere a tutte le funzionalità del dispositivo deve avere i seguenti parametri:

* URI della risorsa: `{IoT hub name}.azure-devices.net/devices/{device id}`,
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

### Usare criteri di accesso condiviso

Quando si crea un token da criteri di accesso condiviso, il campo del nome del criterio `skn` deve essere impostato sul nome del criterio usato. È anche necessario che i criteri concedano l'autorizzazione **DeviceConnect**.

I due scenari principali per l'uso di criteri di accesso condiviso per accedere alla funzionalità dei dispositivi sono:

* [gateway del protocollo cloud][lnk-azure-protocol-gateway],
* [servizi token][lnk-devguide-security] tramite i quali implementare schemi di autenticazione personalizzati.

Poiché i criteri di accesso condiviso possono potenzialmente autorizzare la connessione a qualsiasi dispositivo, in fase di creazione dei token di sicurezza è importante usare l'URI risorsa corretto. Ciò è particolarmente importante per i servizi token, che devono definire l'ambito del token a un dispositivo specifico usando l'URI risorsa. Questo punto è meno importante per i gateway di protocollo, in quanto già filtrano il traffico per tutti i dispositivi.

Ad esempio, un servizio token che usa il criterio di accesso condiviso già esistente denominato **device** creerebbe un token con i parametri seguenti:

* URI della risorsa: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chiave di firma: una delle chiavi del criterio `device`,
* nome criterio: `device`,
* Qualsiasi ora di scadenza.

Un esempio di utilizzo della funzione Node precedente sarebbe:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Il risultato, che concede l'accesso a tutte le funzionalità per device1, sarà:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Un gateway di protocollo potrebbe usare lo stesso token per tutti i dispositivi semplicemente impostando l'URI della risorsa su `myhub.azure-devices.net/devices`.

## Usare token di sicurezza da componenti del servizio

I componenti del servizio possono generare token di sicurezza solo tramite criteri di accesso condiviso che concedono le autorizzazioni appropriate, come illustrato nella [sezione Sicurezza della Guida per gli sviluppatori dell'hub IoT][lnk-devguide-security].

Queste sono le funzioni del servizio esposte sugli endpoint:

| Endpoint | Funzionalità |
| ----- | ----------- |
| `{iot hub host name}/devices` | Creazione, aggiornamento, recupero ed eliminazione delle identità dispositivo. |
| `{iot hub host name}/messages/events`   | Ricezione di messaggi da dispositivo a cloud. |
| `{iot hub host name}/servicebound/feedback`   | Ricezione di feedback per messaggi da cloud a dispositivo. |
| `{iot hub host name}/devicebound`   | Invio di messaggi da cloud a dispositivo. |

Ad esempio, un servizio che usa il criterio di accesso condiviso già esistente denominato **registryRead** creerebbe un token con i parametri seguenti:

* URI della risorsa: `{IoT hub name}.azure-devices.net/devices`,
* chiave di firma: una delle chiavi del criterio `registryRead`,
* nome criterio: `registryRead`,
* Qualsiasi ora di scadenza.

    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Il risultato, che concede l'accesso in lettura a tutte le identità dispositivo, sarà:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## Certificati X.509 supportati

È possibile usare qualsiasi certificato X.509 per autenticare un dispositivo con hub IoT. Sono inclusi:

-   **Un certificato X.509 esistente**. Un dispositivo potrebbe già avere un certificato X.509 associato. Il dispositivo può usare questo certificato per autenticarsi con hub IoT.

-   **Un certificato X-509 auto-generato e auto-firmato**. Un produttore di dispositivi o un distributore interno può generare questi certificati e archiviare la chiave privata corrispondente (e il certificato) nel dispositivo. È possibile usare strumenti come [OpenSSL] e l'utilità [Windows SelfSignedCertificate] per questo scopo.

-   **Certificato X.509 firmato da un'autorità di certificazione**. È possibile usare un certificato X.509 generato e firmato da un'autorità di certificazione (CA) anche per identificare un dispositivo e autenticare un dispositivo con hub IoT.

Un dispositivo può usare un certificato X.509 o un token di sicurezza per l'autenticazione, ma non per entrambi.

## Registrare un certificato client X.509 per un dispositivo

L'[SDK Azure IoT Service per C#][lnk-service-sdk] (versione 1.0.8+) supporta la registrazione di un dispositivo che usa un certificato client X.509 per l'autenticazione. Anche altre API come quelle per l'importazione e l'esportazione dei dispositivi supportano i certificati client X.509.

### Supporto di C#

La classe **RegistryManager** offre un modo di registrare un dispositivo a livello di codice. In particolare, i metodi **AddDeviceAsync** e **UpdateDeviceAsync** consentono a un utente di registrare e aggiornare un dispositivo nel registro delle identità del dispositivo hub Iot. Questi due metodi accettano un'istanza **Device** come input. La classe **Device** include una proprietà **Authentication** che consente all'utente di specificare identificazioni personali principale e secondaria del certificato X.509. L'identificazione personale rappresenta un hash SHA-1 del certificato X.509 archiviato usando la codifica DER binaria. Gli utenti hanno la possibilità di specificare un'identificazione personale primaria o un'identificazione personale secondaria o entrambe. Le identificazioni personali primarie e secondarie sono supportate per poter gestire scenari di rollover dei certificati.

> [AZURE.NOTE] Hub IoT non richiede o archivia l'intero certificato client X.509 ma solo l'identificazione personale.

Ecco un frammento di codice C# di esempio per registrare un dispositivo usando un certificato client X.509:

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

## Usare un certificato client X.509 durante le operazioni di runtime

[Azure IoT SDK per dispositivi per .NET][lnk-client-sdk] (versione 1.0.11+) supporta l'uso dei certificati client X.509.

### Supporto di C#

La classe **DeviceAuthenticationWithX509Certificate** supporta la creazione di istanze **DeviceClient** tramite un certificato client X.509.

Di seguito è riportato un frammento di codice di esempio:

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[OpenSSL]: https://www.openssl.org/
[Windows SelfSignedCertificate]: https://technet.microsoft.com/library/hh848633
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device

<!---HONumber=AcomDC_0907_2016-->