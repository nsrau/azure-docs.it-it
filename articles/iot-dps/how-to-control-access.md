---
title: Endpoint di sicurezza nel servizio Device Provisioning in hub IoT | Microsoft Docs
description: Concetti - Come controllare l'accesso al servizio Device Provisioning in hub IoT per app back-end. Include informazioni sui token di sicurezza.
services: iot-dps
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-dps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: dkshir,rajeevmv
ms.openlocfilehash: 7e98df582baeb4a15b772351802c63fd90303c77
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Controllo dell'accesso al servizio Device Provisioning in hub IoT di Azure

Questo articolo illustra le opzioni per la protezione del servizio Device Provisioning in hub IoT. Il servizio di provisioning usa le *autorizzazioni* per concedere l'accesso a ogni endpoint. Le autorizzazioni limitano l'accesso all'istanza del servizio in base alla funzionalità.

L'articolo illustra:

* Le diverse autorizzazioni che è possibile concedere a un'app back-end per accedere al servizio di provisioning.
* Il processo di autenticazione e i token usati per verificare le autorizzazioni.

### <a name="when-to-use"></a>Quando usare le autorizzazioni

È necessario avere le autorizzazioni appropriate per accedere agli endpoint del servizio di provisioning. Un'app back-end, ad esempio, deve includere un token contenente le credenziali di sicurezza con ogni messaggio inviato al servizio.

## <a name="access-control-and-permissions"></a>Controllo dell'accesso e autorizzazioni

Per concedere le [autorizzazioni](#device-provisioning-service-permissions) è possibile procedere nei modi seguenti:

* **Criteri di autorizzazione dell'accesso condiviso**. I criteri di accesso condiviso possono concedere qualsiasi combinazione di [autorizzazioni](#device-provisioning-service-permissions). È possibile definire i criteri nel [portale di Azure][lnk-management-portal] o a livello di codice usando le [API REST del servizio Device Provisioning][lnk-resource-provider-apis]. Un servizio di provisioning appena creato ha i criteri predefiniti seguenti:

  * **provisioningserviceowner**: criteri con tutte le autorizzazioni.

> [!NOTE]
> Per informazioni dettagliate, vedere [Autorizzazioni](#device-provisioning-service-permissions).

## <a name="authentication"></a>Autenticazione

Servizio Device Provisioning in hub IoT di Azure concede l'accesso agli endpoint tramite la verifica di un token rispetto ai criteri di accesso condiviso. Le credenziali di sicurezza, ad esempio le chiavi asimmetriche, non vengono mai trasmesse in rete.

> [!NOTE]
> Il servizio Device Provisioning viene protetto tramite la sottoscrizione di Azure, analogamente a tutti i provider in [Azure Resource Manager][lnk-azure-resource-manager].

Per altre informazioni sulla creazione e sull'uso di token di sicurezza, vedere la sezione seguente.

Il protocollo HTTP è l'unico protocollo supportato e implementa l'autenticazione includendo un token valido nell'intestazione della richiesta **Authorization**.

#### <a name="example"></a>Esempio
`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`

> [!NOTE]
> Gli [SDK del servizio Device Provisioning in hub IoT][lnk-sdks] generano automaticamente i token durante la connessione al servizio.

## <a name="security-tokens"></a>Token di sicurezza
Il servizio Device Provisioning usa i token di sicurezza per autenticare i servizi ed evitare l'invio in rete delle chiavi. Inoltre, i token di sicurezza hanno una validità limitata in termini di tempo e portata. Gli [SDK del servizio Device Provisioning in hub IoT][lnk-sdks] generano automaticamente i token senza richiedere una configurazione speciale. In alcuni scenari è necessario generare e usare direttamente i token di sicurezza. Questi scenari includono l'uso diretto di superfici HTTP.

### <a name="security-token-structure"></a>Formato del token di sicurezza

I token di sicurezza consentono di concedere a servizi l'accesso con limite temporale a funzionalità specifiche del servizio Device Provisioning in hub IoT. Per ottenere l'autorizzazione per connettersi al servizio di provisioning, i dispositivi e i servizi devono inviare i token di sicurezza firmati con una chiave di accesso condiviso o una chiave simmetrica.

Un token firmato con una chiave di accesso condiviso concede l'accesso a tutte le funzionalità associate alle autorizzazioni dei criteri di accesso condiviso. 

Il token di sicurezza ha il formato seguente:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

I valori previsti sono i seguenti:

| Valore | Descrizione |
| --- | --- |
| {signature} |Stringa della firma HMAC-SHA256 nel formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: la chiave viene decodificata dalla codifica Base64 e usata come chiave per eseguire il calcolo di HMAC-SHA256.|
| {expiry} |Stringhe UTF8 per il numero di secondi trascorsi dalle 00:00:00 UTC dell'1 gennaio 1970. |
| {URL-encoded-resourceURI} | Codifica URL con lettere minuscole dell'URI della risorsa con lettere minuscole Prefisso URI (per segmento) degli endpoint a cui è possibile accedere tramite questo token e che inizia con il nome host del servizio Device Provisioning in hub IoT senza il protocollo. ad esempio `mydps.azure-devices-provisioning.net`. |
| {policyName} |Nome del criterio di accesso condiviso a cui fa riferimento il token. |

**Nota sul prefisso**: il prefisso dell'URI viene calcolato in base al segmento e non in base al carattere. Ad esempio `/a/b` è un prefisso per `/a/b/c` ma non per `/a/bc`.

Il frammento seguente di Node.js mostra una funzione denominata **generateSasToken** che calcola il token dagli input `resourceUri, signingKey, policyName, expiresInMins`. Nelle sezioni successive viene illustrato nel dettaglio come inizializzare gli input a seconda del caso d'uso.

```nodejs
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Per fare un confronto, l'equivalente in termini di codice Python per generare un token di sicurezza è:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Poiché la validità temporale del token viene verificata sui computer del servizio Device Provisioning in hub IoT, lo sfasamento dell'orologio del computer che genera il token deve essere minimo.


### <a name="use-security-tokens-from-service-components"></a>Usare token di sicurezza da componenti del servizio

I componenti del servizio possono generare token di sicurezza solo usando criteri di accesso condiviso che concedono le autorizzazioni appropriate, come illustrato prima.

Di seguito vengono indicate le funzioni del servizio esposte sugli endpoint:

| Endpoint | Funzionalità |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Fornisce operazioni di registrazione del dispositivo con il servizio Device Provisioning. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Fornisce operazioni per la gestione dei gruppi di registrazione del dispositivo. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Fornisce operazioni per il recupero e la gestione dello stato delle registrazioni dei dispositivi. |


Ad esempio, un servizio generato con il criterio di accesso condiviso già esistente denominato **enrollmentread** creerebbe un token con i parametri seguenti:

* URI della risorsa: `{mydps}.azure-devices-provisioning.net`,
* chiave di firma: una delle chiavi del criterio `enrollmentread` ,
* nome criterio: `enrollmentread`,
* Qualsiasi ora di scadenza.

![Creare i criteri di accesso condiviso per l'istanza del servizio Device Provisioning nel portale][img-add-shared-access-policy]

```nodejs
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Il risultato, che concede l'accesso in lettura a tutti i record di registrazione, sarà:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Argomenti di riferimento:

Gli argomenti di riferimento seguenti offrono altre informazioni sul controllo dell'accesso al servizio Device Provisioning in hub IoT.

## <a name="device-provisioning-service-permissions"></a>Autorizzazioni per il servizio Device Provisioning

La tabella seguente elenca le autorizzazioni che è possibile usare per controllare l'accesso al servizio Device Provisioning in hub IoT.

| Autorizzazione | Note |
| --- | --- |
| **ServiceConfig** |Concede l'accesso per la modifica delle configurazioni del servizio. <br/>Questa autorizzazione viene usata dai servizi cloud back-end. |
| **EnrollmentRead** |Concede l'accesso in lettura alle registrazioni dei dispositivi e ai gruppi di registrazione. <br/>Questa autorizzazione viene usata dai servizi cloud back-end. |
| **EnrollmentWrite** |Concede l'accesso in scrittura alle registrazioni dei dispositivi e ai gruppi di registrazione. <br/>Questa autorizzazione viene usata dai servizi cloud back-end. |
| **RegistrationStatusRead** |Concede l'accesso in lettura allo stato della registrazione dei dispositivi. <br/>Questa autorizzazione viene usata dai servizi cloud back-end. |
| **RegistrationStatusWrite**  |Concede l'accesso con autorizzazione di eliminazione allo stato della registrazione dei dispositivi. <br/>Questa autorizzazione viene usata dai servizi cloud back-end. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
