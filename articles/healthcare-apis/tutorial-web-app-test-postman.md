---
title: Esercitazione su un'app Web - Testare la connessione all'API di Azure per FHIR
description: Questa esercitazione illustra un esempio di distribuzione di una semplice applicazione Web. Questa sezione dell'esercitazione descrive come testare la connessione al server FHIR con Postman
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852923"
---
# <a name="testing-the-fhir-api"></a>Test dell'API per FHIR
Nei due passaggi precedenti è stata distribuita l'API di Azure per FHIR ed è stata registrata l'applicazione client. Ora è possibile per verificare che l'API di Azure per FHIR sia configurata con l'applicazione client. 

## <a name="retrieve-capability-statement"></a>Recuperare la dichiarazione di funzionalità
Prima di tutto si otterrà la dichiarazione di funzionalità per l'API di Azure per FHIR. 
1. Aprire Postman
1. Recuperare la dichiarazione di funzionalità tramite GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/metadata. Nell'immagine seguente il nome del server FHIR è **fhirserver**.

![Dichiarazione di funzionalità](media/tutorial-web-app/postman-capability-statement.png)

Successivamente si proverà a recuperare un paziente. Per recuperare un paziente, immettere GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/Patient. Si riceverà un errore 401 di operazione non autorizzata. Questo errore si verifica perché non è stato dimostrato che l'acceso ai dati dei pazienti è consentito.

## <a name="get-patient-from-fhir-server"></a>Ottenere un paziente dal server FHIR
![Accesso al paziente non riuscito](media/tutorial-web-app/postman-patient-authorization-failed.png)

Per ottenere l'accesso, è necessario un token di accesso.
1. In Postman selezionare **Authorization** (Autorizzazione) e impostare il tipo su **OAuth2.0**
1. Selezionare **Get New Access Token** (Ottieni nuovo token di accesso)
1. Compilare i campi e selezionare **Request Token** (Richiedi token). Di seguito sono riportati i valori per ogni campo per questa esercitazione.

|Campo                |valore                                                               |
|---------------------|--------------------------------------------------------------------|
|Token Name (Nome del token)           |Un nome per il token                                               |
|Grant Type (Tipo di concessione)           |Codice di autorizzazione                                                  |
|Callback URL (URL callback)         |https://www.getpostman.com/oauth2/callback                          |
|Auth URL (URL autorizzazione)             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/?resource=https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com|
|Access Token URL (URL token di accesso)     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/token|
|ID client            |L'ID client copiato nei passaggi precedenti             |
|Client Secret        |\<BLANK>                                                            |
|Scope                |\<BLANK>                                                            |
|State                |1234                                                                |
|Client Authentication (Autenticazione client)|Invia le credenziali del client nel corpo                                     |

4. Accedere con le proprie credenziali e selezionare **Accept** (Accetta)
1. Scorrere verso il basso nel risultato e selezionare **Use Token** (Usa token)
1. Selezionare di nuovo **Send** (Invia) in alto e questa volta si dovrebbe ottenere il risultato ![Success Patient](media/tutorial-web-app/postman-patient-authorization-success.png) (Accesso al paziente riuscito)

## <a name="post-patient-into-fhir-server"></a>Pubblicare il paziente nel server FHIR
Una volta ottenuto l'accesso, è possibile creare un nuovo paziente. Ecco un esempio di un semplice paziente che è possibile aggiungere al server FHIR. Immettere il codice seguente nella sezione **Body** (Corpo) di Postman.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
Questa istruzione POST creerà un nuovo paziente nel server FHIR con il nome James Tiberious Kirk.
![Pubblicare il paziente](media/tutorial-web-app/postman-post-patient.png)

Se si esegue di nuovo GET come nel passaggio precedente per recuperare un paziente, si noterà che James Tiberious Kirk è presente nell'output.

## <a name="troubleshooting-access-issues"></a>Risoluzione dei problemi di accesso
Se si verificano problemi durante uno di questi passaggi, esaminare i documenti relativi ad Azure Active Directory e all'API di Azure per FHIR. 

* [Azure AD e API di Azure per FHIR](azure-ad-hcapi.md): questo documento illustra alcuni principi di base di Azure Active Directory e la relativa interazione con l'API di Azure per FHIR.
* [Convalida dei token di accesso](azure-ad-hcapi-token-validation.md): questa guida pratica fornisce dettagli più specifici sulla convalida dei token di accesso e sui passaggi da eseguire per risolvere i problemi di accesso.

## <a name="next-steps"></a>Passaggi successivi
Ora che è possibile connettersi all'applicazione client, si è pronti per scrivere l'applicazione Web.

>[!div class="nextstepaction"]
>[Scrivere un'applicazione Web](tutorial-web-app-write-web-app.md)



