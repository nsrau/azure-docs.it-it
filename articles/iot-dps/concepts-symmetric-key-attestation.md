---
title: Servizio Device Provisioning in hub IoT di Azure - Attestazione chiave simmetrica
description: In questo articolo viene fornita una panoramica concettuale dell'attestazione con chiave simmetrica con il servizio Device Provisioning IoT.
author: wesmc7777
ms.author: wesmc
ms.date: 08/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 2f6e1e1a27e32e567cf0eaa8ff7a99046ed81bbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746138"
---
# <a name="symmetric-key-attestation"></a>Attestazione con chiave simmetrica

Questo articolo descrive il processo di attestazione di identità quando si usano le chiavi simmetriche con il servizio Device Provisioning. 

L'attestazione con chiave simmetrica costituisce un approccio semplice per autenticare un dispositivo con un'istanza del servizio Device Provisioning. Questo metodo di attestazione rappresenta un'esperienza di "Hello World" per gli sviluppatori che non hanno familiarità con il provisioning dei dispositivi o che non possiedono requisiti di sicurezza restrittivi. L'attestazione relativa al dispositivo che si serve di un certificato [TPM](concepts-tpm-attestation.md) o [ X.509](concepts-security.md#x509-certificates) è più sicura e deve essere usata per requisiti di sicurezza più rigorosi.

Le registrazioni con chiave simmetrica offrono anche un modo eccezionale di avvio automatico nel cloud tramite IoT di Azure ai dispositivi legacy con funzionalità di sicurezza limitata. Per altre informazioni sull'attestazione con chiave simmetrica con i dispositivi legacy, vedere [Come usare le chiavi simmetriche con i dispositivi legacy](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Creazione di chiave simmetrica

Per impostazione predefinita, il servizio Device Provisioning consente di creare nuove chiavi simmetriche con una lunghezza predefinita di 32 byte quando le nuove registrazioni vengono salvate con l'opzione **Genera automaticamente chiavi** abilitata.

![Chiavi simmetriche generate automaticamente](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

È anche possibile fornire le proprie chiavi simmetriche per le registrazioni, disabilitando questa opzione. Quando si specificano le proprie chiavi simmetriche, queste devono avere una lunghezza di chiave compresa tra 16 e 64 byte. Inoltre, è necessario specificare le chiavi simmetriche in formato Base64 valido.



## <a name="detailed-attestation-process"></a>Processo di attestazione dettagliato

L'attestazione con chiave simmetrica con il servizio Device Provisioning viene eseguita usando gli stessi [token di sicurezza](../iot-hub/iot-hub-devguide-security.md#security-token-structure) supportati dall'hub IoT per identificare i dispositivi. Questi token di sicurezza sono [token di firma di accesso condiviso (SAS)](../service-bus-messaging/service-bus-sas.md). 

I token di firma di accesso condiviso possiedono una *firma* in codice hash creata usando la chiave simmetrica. La firma viene ricreata dal servizio Device Provisioning per verificare se un token di sicurezza presentato durante l'attestazione è autentico o meno.

I token di firma di accesso condiviso hanno il formato seguente:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Di seguito sono riportati i componenti di ciascun token:

| Value | DESCRIZIONE |
| --- | --- |
| {signature} |Stringa della firma HMAC-SHA256. Per le registrazioni individuali, questa firma viene generata utilizzando la chiave simmetrica (primaria o secondaria) per eseguire il codice hash. Per i gruppi di registrazioni, viene usata una chiave derivata dalla chiave del gruppo di registrazioni per eseguire il codice hash. Il codice hash viene eseguito su un messaggio nel formato: `URL-encoded-resourceURI + "\n" + expiry`. **Importante**: La chiave deve essere decodificata da base64 prima di essere usata per eseguire il calcolo di HMAC-SHA256. Inoltre, il risultato di firma deve essere codificato con URL. |
| {resourceURI} |URI dell'endpoint di registrazione a cui è possibile accedere tramite questo token, a partire da ID ambito per l'istanza del servizio Device Provisioning. Ad esempio: `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |Stringhe UTF8 per il numero di secondi trascorsi dalle 00:00:00 UTC dell'1 gennaio 1970. |
| {URL-encoded-resourceURI} |Codifica URL con lettere minuscole dell'URI della risorsa con lettere minuscole |
| {policyName} |Nome del criterio di accesso condiviso a cui fa riferimento il token. Il nome del criterio usato durante il provisioning con l'attestazione con chiave simmetrica è **registrazione**. |

Quando un dispositivo esegue l'attestazione con una registrazione singola, viene usata la chiave simmetrica definita nella voce di registrazione singola per creare la firma con codice hash per il token di firma di accesso condiviso.

Per esempi di codice che creano un token di firma di accesso condiviso, vedere [Token di sicurezza](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

La creazione di token di sicurezza per l'attestazione con chiave simmetrica è supportata dall'IoT di Azure C SDK. Per un esempio di utilizzo dell'IoT Azure C SDK per attestare con una registrazione singola, vedere [Effettuare il provisioning di un dispositivo simulato con chiavi simmetriche](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Registrazioni di gruppo

Le chiavi simmetriche per le registrazioni di gruppo non vengono usate direttamente dai dispositivi durante il provisioning. In alternativa, i dispositivi che appartengono a un gruppo di registrazioni effettuano il provisioning tramite una chiave di dispositivo derivata. 

In primo luogo,viene definito un ID di registrazione univoco per ogni dispositivo che effettua l'attestazione con un gruppo di registrazioni. I caratteri validi per l'ID di registrazione sono caratteri alfanumerici in minuscolo e trattini ("-"). Questo ID di registrazione deve essere un nome univoco che identifichi il dispositivo. Ad esempio, un dispositivo legacy potrebbe non supportare molte delle funzionalità di sicurezza. Il dispositivo legacy può avere solo un indirizzo MAC o un numero di serie disponibile per identificare in modo univoco il dispositivo. In tal caso, un ID di registrazione può essere composto da un indirizzo MAC e da un numero di serie simile al seguente:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Questo preciso esempio viene usato nell'articolo [Come effettuare il provisioning di dispositivi legacy usando chiavi simmetriche](how-to-legacy-device-symm-key.md).

Dopo aver definito un ID di registrazione del dispositivo, la chiave simmetrica per il gruppo di registrazione viene usata per calcolare un codice hash [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID di registrazione per produrre una chiave di dispositivo derivata. Il codice hash dell'ID di registrazione può essere eseguito con il codice C# riportato di seguito:

```C#
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```C#
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

La chiave del dispositivo risultante viene quindi usata per generare un token di firma di accesso condiviso da utilizzare per l'attestazione. Ogni dispositivo in un gruppo di registrazione deve eseguire l'attestazione usando un token di sicurezza generato da una chiave derivata univoca. La chiave simmetrica del gruppo di registrazioni non può essere utilizzata usata direttamente per l'attestazione.

#### <a name="installation-of-the-derived-device-key"></a>Installazione della chiave di dispositivo derivata

In teoria le chiavi di dispositivo vengono derivate e installate in ambiente di fabbrica. Questo metodo garantisce che la chiave di gruppo non venga mai inclusa in nessun software distribuito al dispositivo. Quando al dispositivo viene assegnato un indirizzo MAC o un numero di serie, la chiave può essere derivata e inserita nel dispositivo in qualsiasi modo il produttore scelga di archiviarla.

Si consideri il seguente diagramma che mostra una tabella di chiavi del dispositivo generate in ambiente di fabbrica eseguendo il codice hash di ogni ID di registrazione dispositivo con la chiave di registrazione di gruppo (**K**). 

![Chiavi dispositivo assegnate dall'ambiente di fabbrica](./media/concepts-symmetric-key-attestation/key-diversification.png)

L'identità di ogni dispositivo è rappresentata dall'ID di registrazione e dalla chiave di dispositivo derivata che viene installata in fase di produzione. La chiave del dispositivo non viene mai copiata in un'altra posizione e la chiave di gruppo non viene mai archiviata in un dispositivo.

Se le chiavi di dispositivo non vengono installate in ambiente di fabbrica, deve essere usato un [modulo di protezione hardware HSM](concepts-security.md#hardware-security-module) per archiviare in modo sicuro l'identità del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si hanno le informazioni sull'attestazione con chiave simmetrica, vedere gli articoli seguenti per altre informazioni:

* [Guida introduttiva: Effettuare il provisioning di un dispositivo simulato con chiavi simmetriche](quick-create-simulated-device-symm-key.md)
* [Informazioni sui concetti di provisioning automatico](./concepts-auto-provisioning.md)
* [Iniziare a usare il provisioning automatico](./quick-setup-auto-provision.md) 
