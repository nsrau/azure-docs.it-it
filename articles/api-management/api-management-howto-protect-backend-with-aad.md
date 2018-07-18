---
title: Proteggere un'API usando OAuth 2.0 con Azure Active Directory e Gestione API | Microsoft Docs
description: Informazioni su come proteggere il back-end di un'API Web con Azure Active Directory e Gestione API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: f5662a4082487137dfd642cc3264a90f8ab19054
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30928037"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Proteggere un'API usando OAuth 2.0 con Azure Active Directory e Gestione API

Questa guida illustra come configurare l'istanza di Gestione API di Azure per proteggere un'API usando il protocollo OAuth 2.0 con Azure Active Directory (Azure AD). 

## <a name="prerequisites"></a>prerequisiti
Per eseguire i passaggi in questo articolo è necessario avere quanto segue:
* Un'istanza di Gestione API
* Un'API in corso di pubblicazione che usa l'istanza di Gestione API
* Un tenant di Azure AD

## <a name="overview"></a>Panoramica

Ecco una rapida panoramica dei passaggi:

1. Registrare un'applicazione (app back-end) in Azure AD per rappresentare l'API.
2. Registrare un'altra applicazione (app client) in Azure AD per rappresentare un'applicazione client che deve chiamare l'API.
3. In Azure AD concedere le autorizzazioni per consentire all'app client di chiamare l'app back-end.
4. Configurare la console per sviluppatori per l'uso dell'autorizzazione utente OAuth 2.0.
5. Aggiungere i criteri **validate-jwt** per convalidare il token OAuth per ogni richiesta in ingresso.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrare un'applicazione in Azure AD per rappresentare l'API

Per proteggere un'API con Azure AD, il primo passaggio consiste nel registrare un'applicazione in Azure AD che rappresenta l'API. 

1. Passare al tenant di Azure AD e quindi a **Registrazioni per l'app**.

2. Selezionare **Registrazione nuova applicazione**. 

3. Specificare un nome per l'applicazione. In questo esempio il nome è `backend-app`.  

4. Scegliere **App Web/API** come **Tipo di applicazione**. 

5. Per **URL accesso** è possibile usare `https://localhost` come segnaposto.

6. Selezionare **Create**.

Dopo aver creato l'applicazione, prendere nota dell'**ID applicazione**, che verrà usato in un passaggio successivo. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrare un'altra applicazione in Azure AD per rappresentare un'applicazione client

Ogni applicazione client che chiama l'API deve anche essere registrata come applicazione in Azure AD. In questo caso, l'applicazione client di esempio è la console per sviluppatori nel portale per sviluppatori di Gestione API. Ecco come registrare un'altra applicazione in Azure AD per rappresentare la console per sviluppatori.

1. Selezionare **Registrazione nuova applicazione**. 

2. Specificare un nome per l'applicazione. In questo esempio il nome è `client-app`.

3. Scegliere **App Web/API** come **Tipo di applicazione**.  

4. Per **URL accesso** è possibile usare `https://localhost` come segnaposto oppure l'URL di accesso dell'istanza di Gestione API. In questo esempio l'URL è `https://contoso5.portal.azure-api.net/signin`.

5. Selezionare **Create**.

Dopo aver creato l'applicazione, prendere nota dell'**ID applicazione**, che verrà usato in un passaggio successivo. 

Creare ora un segreto client per l'applicazione, che verrà usato in un passaggio successivo.

1. Selezionare di nuovo **Impostazioni** e passare a **Chiavi**.

2. In **Password** specificare una descrizione in **Descrizione chiave**. Scegliere quando la chiave deve scadere e selezionare **Salva**.

Prendere nota del valore della chiave. 

## <a name="grant-permissions-in-azure-ad"></a>Concedere le autorizzazioni in Azure AD

Ora che sono state registrate due applicazioni per rappresentare l'API e la console per sviluppatori, è necessario concedere le autorizzazioni per consentire all'app client di chiamare l'app back-end.  

1. Passare a **Registrazioni per l'app**. 

2. Selezionare `client-app` e passare a **Impostazioni**.

3. Selezionare **Autorizzazioni necessarie** > **Aggiungi**.

4. Fare clic su **Selezionare un'API** e cercare `backend-app`.

5. In **Autorizzazioni delegate** selezionare `Access backend-app`. 

6. Fare clic su **Seleziona** e quindi su **Fine**. 

> [!NOTE]
> Se **Azure Active Directory** non è presente nell'elenco di autorizzazioni per altre applicazioni, selezionare **Aggiungi** per aggiungere la voce all'elenco.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Abilitare l'autorizzazione utente OAuth 2.0 nella console per sviluppatori

A questo punto sono state create le applicazioni in Azure AD e sono state concesse le autorizzazioni appropriate per consentire all'app client di chiamare l'app back-end. 

In questo esempio la console per sviluppatori è l'app client. La procedura seguente descrive come abilitare l'autorizzazione utente OAuth 2.0 nella console per sviluppatori. 

1. Passare all'istanza di Gestione API.

2. Selezionare **OAuth 2.0** > **Aggiungi**.

3. Specificare i valori per i campi **Nome visualizzato** e **Descrizione**.

4. Per **URL della pagina di registrazione del client** immettere un valore segnaposto, ad esempio `http://localhost`. L'**URL della pagina di registrazione del client** punta alla pagina che gli utenti possono usare per creare e configurare i propri account per i provider OAuth 2.0 che supportano questa operazione. In questo esempio gli utenti non creano e configurano i propri account, quindi si usa un segnaposto.

5. Per **Tipi di concessione di autorizzazione** selezionare **Codice di autorizzazione**.

6. Specificare **URL dell'endpoint autorizzazione** e **URL dell'endpoint token**. Recuperare questi valori nella pagina **Endpoint** nel tenant di Azure AD. Passare di nuovo alla pagina **Registrazioni per l'app** e selezionare **Endpoint**.

7. Copiare il valore di **Endpoint di autorizzazione OAuth 2.0** e incollarlo nella casella di testo **URL dell'endpoint autorizzazione**.

8. Copiare il valore di **Endpoint token OAuth 2.0** e incollarlo nella casella di testo **URL dell'endpoint token**. Oltre a incollare il valore nell'endpoint del token, aggiungere un parametro del corpo denominato **risorsa**. Come valore di questo parametro usare l'**ID applicazione** per l'app back-end.

9. Specificare quindi le credenziali del client. Queste sono le credenziali per l'app client.

10. Per **ID client** usare l'**ID applicazione** per l'app client.

11. Per **Segreto client** usare la chiave creata in precedenza per l'app client. 

12. Immediatamente dopo il segreto client è riportato il valore di **redirect_url** per il tipo di concessione con codice di autorizzazione. Prendere nota dell'URL.

13. Selezionare **Create**.

14. Tornare alla pagina **Impostazioni** dell'app client.

15. Selezionare **URL di risposta** e incollare il valore di **redirect_url** nella prima riga. In questo esempio è stato sostituito `https://localhost` con l'URL nella prima riga.  

Ora che è stato configurato un server di autorizzazione OAuth 2.0, la console per sviluppatori può ottenere i token di accesso da Azure AD. 

Il passaggio successivo consiste nell'abilitare l'autorizzazione utente OAuth 2.0 per l'API. In questo modo, la console per sviluppatori sa che deve ottenere un token di accesso per conto dell'utente prima di eseguire le chiamate all'API.

1. Passare all'istanza di Gestione API e quindi ad **API**.

2. Selezionare l'API da proteggere. In questo esempio si usa `Echo API`.

3. Passare a **Impostazioni**.

4. In **Sicurezza** scegliere **OAuth 2.0** e selezionare il server OAuth 2.0 configurato in precedenza. 

5. Selezionare **Salva**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Eseguire la chiamata all'API dal portale per sviluppatori

Ora che l'autorizzazione utente OAuth 2.0 è stata abilitata su `Echo API`, la console per sviluppatori ottiene un token di accesso per conto dell'utente prima di chiamare l'API.

1. Passare a qualsiasi operazione in `Echo API` nel portale per sviluppatori e selezionare **Prova**. Verrà visualizzata la console per sviluppatori.

2. Nella sezione **Autorizzazione** è presente un nuovo elemento che corrisponde al server di autorizzazione appena aggiunto.

3. Selezionare **Codice di autorizzazione** nell'elenco a discesa delle autorizzazioni. Verrà chiesto di accedere al tenant di Azure AD. Se si è già eseguito l'accesso con l'account, è possibile che la richiesta non venga visualizzata.

4. Dopo l'accesso, alla richiesta viene aggiunta un'intestazione `Authorization` con un token di accesso di Azure AD. Di seguito è riportato un token di esempio (con codifica Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Selezionare **Invia** e quindi sarà possibile chiamare l'API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurare criteri di convalida JWT per preautorizzare le richieste

A questo punto, quando un utente prova a eseguire una chiamata dalla console per sviluppatori, viene chiesto di eseguire l'accesso. La console per sviluppatori ottiene un token di accesso per conto dell'utente.

Ma che cosa accade se un utente chiama l'API senza un token o con un token non valido? È ad esempio possibile chiamare comunque l'API anche se si elimina l'intestazione `Authorization`. Questo avviene perché Gestione API non convalida il token di accesso a questo punto. Passa invece semplicemente l'intestazione `Authorization` all'API back-end.

È possibile usare i criteri di [convalida JWT](api-management-access-restriction-policies.md#ValidateJWT) per pre-autorizzare le richieste in Gestione API convalidando i token di accesso di ogni richiesta in ingresso. Se una richiesta non ha un token valido, Gestione API la blocca. È ad esempio possibile aggiungere i criteri seguenti alla sezione `<inbound>` di `Echo API`. In questo modo, viene verificata l'attestazione dei destinatari in un token di accesso e viene restituito un messaggio di errore se il token non è valido. Per informazioni su come configurare i criteri, vedere [Impostare o modificare criteri](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>Creare un'applicazione per chiamare l'API

In questa guida è stata usata la console per sviluppatori di Gestione API come applicazione client di esempio per chiamare `Echo API` a cui è applicata la protezione tramite OAuth 2.0. Per altre informazioni su come creare un'applicazione e implementare OAuth 2.0, vedere [Esempi di codice di Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni su [Azure Active Directory e OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md).
* Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.
* Per altri metodi di protezione del servizio back-end, vedere [Autenticazione reciproca dei certificati](api-management-howto-mutual-certificates.md).

* [Creare una nuova istanza del servizio Gestione API di Azure](get-started-create-service-instance.md).

* [Gestire la prima API](import-and-publish.md).
