---
title: API REST di Azure Active Directory-test con Fiddler
description: Usare Fiddler per testare l'API REST di configurazione di app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3766567fe58e8d2eb86556d3defa7a85efd9b2fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424150"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Testare l'API REST di configurazione di app Azure usando Fiddler

Per testare l'API REST con [Fiddler](https://www.telerik.com/fiddler), è necessario includere le intestazioni HTTP necessarie per [l'autenticazione](./rest-api-authentication-hmac.md) nelle richieste. Di seguito viene illustrato come configurare Fiddler per il test dell'API REST, generando automaticamente le intestazioni di autenticazione:

1. Assicurarsi che TLS 1,2 sia un protocollo consentito:
    1. Passare a **strumenti**  >  **Opzioni**  >  **https** ).
    1. Verificare che il **traffico HTTPS di decrittografia** sia selezionato.
    1. Nell'elenco dei protocolli aggiungere **TLS 1.2** se non è presente.
1. Aprire l' **editor di script Fiddler** oppure premere **CTRL + R** in Fiddler
1. Aggiungere il codice seguente all'interno della `Handlers` classe prima della `OnBeforeRequest` funzione

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Aggiungere il codice seguente alla fine della `OnBeforeRequest` funzione. Aggiornare la chiave di accesso come indicato dal commento TODO.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. Usare il [compositore di Fiddler](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) per generare e inviare una richiesta
