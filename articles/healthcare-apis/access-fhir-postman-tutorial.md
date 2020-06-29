---
title: Server FHIR Postman in Azure - API di Azure per FHIR
description: Questa esercitazione illustra i passaggi necessari per usare Postman per accedere a un server FHIR. Postman è utile per eseguire il debug delle applicazioni che accedono alle API.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: mihansen
author: hansenms
ms.date: 02/07/2019
ms.openlocfilehash: 684f85042fd09c14621801ec017fea0e632f2598
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "84870531"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Accedere all'API di Azure per FHIR con Postman

Un'applicazione client accede a un'API per FHIR tramite un'[API REST](https://www.hl7.org/fhir/http.html). Si può anche decidere di interagire direttamente con il server FHIR durante la compilazione delle applicazioni, ad esempio a scopo di debug. Questa esercitazione illustra i passaggi necessari per usare [Postman](https://www.getpostman.com/) per accedere a un server FHIR. Postman è uno strumento usato spesso per il debug durante la compilazione di applicazioni che accedono alle API.

## <a name="prerequisites"></a>Prerequisiti

- Un endpoint FHIR in Azure. È possibile configurarlo usando l'API gestita di Azure per FHIR o il server FHIR open source per Azure. Configurare l'API gestita di Azure per FHIR usando il [portale di Azure](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md) o l'[interfaccia della riga di comando di Azure](fhir-paas-cli-quickstart.md).
- Postman installato. È possibile ottenerlo all'indirizzo [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>Dettagli sull'autenticazione e sul server FHIR

Per usare Postman, sono necessari i dettagli seguenti:

- L'URL del server FHIR, ad esempio `https://MYACCOUNT.azurehealthcareapis.com`
- Il provider di identità `Authority` per il server FHIR, ad esempio `https://login.microsoftonline.com/{TENANT-ID}`
- `audience` configurato. Si tratta in genere dell'URL del server FHIR, ad esempio `https://MYACCOUNT.azurehealthcareapis.com` o semplicemente `https://azurehealthcareapis.com`.
- Il valore di `client_id` (o ID applicazione) dell'[applicazione client](register-confidential-azure-ad-client-app.md) che si userà per accedere al servizio FHIR.
- Il valore di `client_secret` (o segreto dell'applicazione) per l'applicazione client.

Infine, è necessario verificare che `https://www.getpostman.com/oauth2/callback` sia un URL di risposta registrato per l'applicazione client.

## <a name="connect-to-fhir-server"></a>Connettersi al server FHIR

Usando Postman, inviare una richiesta `GET` a `https://fhir-server-url/metadata`:

![Dichiarazione di funzionalità dei metadati Postman](media/tutorial-postman/postman-metadata.png)

L'URL dei metadati per l'API di Azure per FHIR è `https://MYACCOUNT.azurehealthcareapis.com/metadata`. In questo esempio l'URL del server FHIR è `https://fhirdocsmsft.azurewebsites.net` e la dichiarazione di funzionalità è disponibile all'indirizzo `https://fhirdocsmsft.azurewebsites.net/metadata`. L'endpoint dovrebbe essere accessibile senza autenticazione.

Se si prova ad accedere a risorse con restrizioni, si dovrebbe ottenere una risposta di autenticazione non riuscita:

![Autenticazione non riuscita](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Come ottenere un token di accesso

Per ottenere un token di accesso valido, selezionare "Authorization" (Autorizzazione) e quindi il tipo "OAuth 2.0":

![Impostare OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Premere "Get New Access Token" (Ottieni nuovo token di accesso) per aprire una finestra di dialogo:

![Richiedere il token di accesso](media/tutorial-postman/postman-request-token.png)

È necessario specificare alcuni dettagli:

| Campo                 | Valore di esempio                                                                                                   | Comment                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Token Name (Nome del token)            | MYTOKEN                                                                                                         | Un nome a scelta          |
| Grant Type (Tipo di concessione)            | Codice di autorizzazione                                                                                              |                            |
| Callback URL (URL callback)          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Auth URL (URL autorizzazione)              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` è `https://MYACCOUNT.azurehealthcareapis.com` per l'API di Azure per FHIR |
| Access Token URL (URL token di accesso)      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| ID client             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | ID applicazione             |
| Client Secret         | `XXXXXXXX`                                                                                                        | Chiave client segreta          |
| Scope | `<Leave Blank>` |
| State                 | `1234`                                                                                                            |                            |
| Client Authentication (Autenticazione client) | Invia le credenziali del client nel corpo                                                                                 |                 

Premere "Request Token" (Richiedi token) per seguire il flusso di autenticazione di Azure Active Directory e ricevere un token da Postman. Se si verificano problemi, aprire la console di Postman scegliendo View->Show Postman Console (Visualizza -> Mostra console di Postman).

Scorrere in basso nella schermata del token restituita e premere "Use Token" (Usa token):

![Usare il token](media/tutorial-postman/postman-use-token.png)

Il token dovrebbe ora essere inserito nel campo "Access Token" (Token di accesso) ed è possibile selezionarlo in "Available Tokens" (Token disponibili). Se si ripete l'invio per eseguire di nuovo la ricerca della risorsa `Patient`, si otterrà uno stato `200 OK`:

![200 - OK](media/tutorial-postman/postman-200-OK.png)

In questo caso, significa che il database non contiene pazienti e la ricerca è vuota.

Se si ispeziona il token di accesso con uno strumento come [https://jwt.ms](https://jwt.ms), verrà visualizzato un contenuto simile al seguente:

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Per la risoluzione dei problemi, la verifica che il destinatario sia corretto (attestazione `aud`) è un buon punto di partenza. Se il token proviene dall'autorità emittente corretta (attestazione`iss`) e il destinatario è corretto (attestazione `aud`), ma non è ancora possibile accedere all'API per FHIR, è probabile che l'utente o l'entità servizio (attestazione`oid`) non abbia accesso al piano dati di FHIR. È consigliabile [usare Controllo degli accessi in base al ruolo di Azure](configure-azure-rbac.md) per assegnare i ruoli del piano dati agli utenti. Se si usa un tenant di Azure Active Directory secondario ed esterno per il piano dati, sarà necessario [configurare assegnazioni locali di Controllo degli accessi in base al ruolo](configure-local-rbac.md).

È anche possibile [ottenere un token per l'API di Azure per FHIR usando l'interfaccia della riga di comando di Azure](get-healthcare-apis-access-token-cli.md). Se si usa un token ottenuto con l'interfaccia della riga di comando di Azure, è necessario usare il tipo di autorizzazione "token di connessione" e incollare il token direttamente.

## <a name="inserting-a-patient"></a>Inserimento di un paziente

Una volta ottenuto un token di accesso valido, è possibile inserire un nuovo paziente. Passare al metodo "POST" e aggiungere il documento JSON seguente nel corpo della richiesta:

[!code-json[](samples/sample-patient.json)]

Premere "Send" (Invia). Si noterà che il paziente è stato creato correttamente:

![Paziente creato](media/tutorial-postman/postman-patient-created.png)

Se si ripete la ricerca del paziente, si dovrebbe ora visualizzare il record corrispondente:

![Paziente creato](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato eseguito l'accesso a un'API per FHIR con Postman. Per informazioni sulle funzionalità dell'API supportate, vedere la sezione corrispondente.
 
>[!div class="nextstepaction"]
>[Funzionalità supportate](fhir-features-supported.md)
