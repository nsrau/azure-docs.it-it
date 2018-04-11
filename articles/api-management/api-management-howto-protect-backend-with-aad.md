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
ms.openlocfilehash: 2c05407d761a8848f9e032aa219960cd7ea6fa93
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Come proteggere un'API usando OAuth 2.0 con Azure Active Directory e Gestione API

Questa guida illustra come configurare la propria istanza di Gestione API (APIM) per proteggere un'API usando il protocollo OAuth 2.0 con Azure Active Directory (AAD). 

## <a name="prerequisite"></a>Prerequisito
Per eseguire i passaggi in questo articolo è necessario avere quanto segue:
* Un'istanza di Gestione API
* Un'API in corso di pubblicazione che usa l'istanza di Gestione API
* Un tenant di Azure AD

## <a name="overview"></a>Panoramica

Questa guida illustra come proteggere un'API con OAuth 2.0 in Gestione API. In questo articolo viene usato Azure AD come server di autorizzazione (server OAuth). Di seguito è riportata una rapida panoramica dei passaggi da eseguire:

1. Registrare un'applicazione (app back-end) in Azure AD per rappresentare l'API
2. Registrare un'altra applicazione (app client) in Azure AD per rappresentare un'applicazione client che deve chiamare l'API
3. In Azure AD concedere le autorizzazioni per consentire all'app client di chiamare l'app back-end
4. Configurare la console per sviluppatori per l'uso dell'autorizzazione utente OAuth 2.0
5. Aggiungere criteri di convalida JWT per convalidare il token OAuth per ogni richiesta in ingresso

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrare un'applicazione in Azure AD per rappresentare l'API

Per proteggere un'API con Azure AD, il primo passaggio consiste nel registrare un'applicazione in Azure AD che rappresenta l'API. 

Passare al tenant di Azure AD e quindi a **Registrazioni per l'app**.

Selezionare **Registrazione nuova applicazione**. 

Specificare un nome per l'applicazione. In questo esempio viene usato `backend-app`.  

Scegliere **App Web/API** come **Tipo di applicazione**. 

Per **URL accesso** è possibile usare `https://localhost` come segnaposto.

Fare clic su **Crea**.

Dopo aver creato l'applicazione, prendere nota dell'**ID applicazione** che dovrà essere specificato in un passaggio successivo. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrare un'altra applicazione in Azure AD per rappresentare un'applicazione client

Ogni applicazione client che deve chiamare l'API deve anche essere registrata come applicazione in Azure AD. In questa guida si userà la console per sviluppatori nel portale per sviluppatori di Gestione API come applicazione client di esempio. 

È quindi necessario registrare in Azure AD un'altra applicazione per rappresentare la console per sviluppatori.

Fare nuovamente clic su **Registrazione nuova applicazione**. 

Specificare un nome per l'applicazione e scegliere **App Web/API** come **Tipo di applicazione**. In questo esempio viene usato `client-app`.  

Per **URL accesso** è possibile usare `https://localhost` come segnaposto oppure l'URL di accesso dell'istanza di Gestione API. In questo esempio viene usato `https://contoso5.portal.azure-api.net/signin`.

Fare clic su **Crea**.

Dopo aver creato l'applicazione, prendere nota dell'**ID applicazione** che dovrà essere specificato in un passaggio successivo. 

È ora necessario creare un segreto client per l'applicazione che verrà usato in un passaggio successivo.

Fare nuovamente clic su **Impostazioni** e passare a **Chiavi**.

In **Password** specificare un valore in **Descrizione chiave**, indicare la scadenza della chiave e quindi fare clic su **Salva**.

Prendere nota del valore della chiave. 

## <a name="grant-permissions-in-aad"></a>Concedere le autorizzazioni in AAD

Ora che sono state registrate due applicazioni per rappresentare l'API (ovvero l'app back-end) e la console per sviluppatori (ovvero l'app client), è necessario concedere le autorizzazioni per consentire all'app client di chiamare l'app back-end.  

Passare nuovamente a **Registrazioni per l'app**. 

Fare clic su `client-app` e passare a **Impostazioni**.

Fare clic su **Autorizzazioni necessarie** e quindi su **Aggiungi**.

Fare clic su **Selezionare un'API** e cercare `backend-app`.

Selezionare `Access backend-app` in **Autorizzazioni delegate**. 

Fare clic su **Seleziona** e quindi su **Fine**. 

> [!NOTE]
> Se **Microsoft** **Azure Active Directory** non è elencata in Autorizzazioni per altre applicazioni, fare clic su **Aggiungi** e aggiungere la voce all'elenco.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Abilitare l'autorizzazione utente OAuth 2.0 nella console per sviluppatori

A questo punto sono state create le applicazioni in Azure AD e sono state concesse le autorizzazioni appropriate per consentire all'app client di chiamare l'app back-end. 

In questa guida si userà la console per sviluppatori come app client. I passaggi seguenti illustrano come abilitare l'autorizzazione utente OAuth 2.0 nella console per sviluppatori. 

Passare all'istanza di Gestione API.

Fare clic su **OAuth 2.0** e quindi su **Aggiungi**.

Specificare i valori per i campi **Nome visualizzato** e **Descrizione**.

Per URL della pagina di registrazione del client** immettere un valore segnaposto, ad esempio `http://localhost`.  **Client registration page URL** fa riferimento alla pagina che gli utenti possono usare per creare e configurare i propri account per i provider OAuth 2.0 che supportano la gestione degli account da parte degli utenti. In questo esempio gli utenti non creano e configurano i propri account, di conseguenza si usa un segnaposto.

Selezionare **Codice di autorizzazione** in **Tipi di concessione di autorizzazione**.

Successivamente, specificare un valore per **Authorization endpoint URL** e **Token endpoint URL**.

Questi valori possono essere recuperati dalla pagina **Endpoint** nel tenant di Azure AD. Per accedere agli endpoint, passare nuovamente alla pagina **Registrazioni per l'app** e fare clic su **Endpoint**.

Copiare il valore di **Endpoint di autorizzazione OAuth 2.0** e incollarlo nella casella di testo **URL dell'endpoint autorizzazione**.

Copiare il valore di **Endpoint token OAuth 2.0** e incollarlo nella casella di testo **URL dell'endpoint token**.

Oltre a incollare l'endpoint del token, aggiungere un altro parametro del corpo denominato **resource** e come valore usare l'**ID applicazione** dell'app back-end.

Specificare quindi le credenziali del client. Queste sono le credenziali per l'app client.

Per **ID client** usare l'**ID applicazione** dell'app client.

Per **Segreto client** usare la chiave creata in precedenza per l'app client. 

Immediatamente dopo il segreto client è riportato il valore di **redirect_url** per il tipo di concessione con codice di autorizzazione.

Prendere nota dell'URL.

Fare clic su **Crea**.

Tornare alla pagina **Impostazioni** dell'app client.

Fare clic su **URL di risposta** e incollare il valore di **redirect_url** nella prima riga. In questo esempio è stato sostituito `https://localhost` con l'URL nella prima riga.  

Ora che è stato configurato un server di autorizzazione OAuth 2.0, la console per sviluppatori dovrebbe essere in grado di ottenere i token di accesso da Azure AD. 

Il passaggio successivo consiste nell'abilitare l'autorizzazione utente OAuth 2.0 per l'API. In questo modo, la console per sviluppatori sa che deve ottenere un token di accesso per conto dell'utente prima di eseguire chiamate all'API.

Passare all'istanza di Gestione API e quindi ad **API**.

Fare clic sull'API da proteggere. In questo esempio si userà `Echo API`.

Passare a **Impostazioni**.

In **Sicurezza** scegliere **OAuth 2.0** e selezionare il server OAuth 2.0 configurato in precedenza. 

Fare clic su **Save**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Eseguire la chiamata all'API dal portale per sviluppatori

Ora che l'autorizzazione utente OAuth 2.0 è stata abilitata su `Echo API`, la console per sviluppatori otterrà un token di accesso per conto dell'utente prima di chiamare l'API.

Selezionare un'operazione qualsiasi in `Echo API` nel portale per sviluppatori e fare clic su **Prova** in modo da passare alla console per sviluppatori.

Nella sezione **Autorizzazione** è presente un nuovo elemento che corrisponde al server di autorizzazione appena aggiunto.

Selezionare **Codice di autorizzazione** dall'elenco a discesa delle autorizzazioni. Verrà chiesto di accedere al tenant di Azure AD. Se si è già eseguito l'accesso con l'account, è possibile che la richiesta non venga visualizzata.

Dopo l'accesso, alla richiesta verrà aggiunta un'intestazione `Authorization` con un token di accesso di Azure AD. 

Di seguito è riportato un token di esempio con codifica Base64.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

Fare clic su **Invia**. Al termine della procedura dovrebbe essere possibile eseguire una chiamata all'API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurare criteri di convalida JWT per preautorizzare le richieste

A questo punto, quando un utente prova a eseguire una chiamata dalla console per sviluppatori, viene chiesto di eseguire l'accesso e la console per sviluppatori ottiene un token di accesso per conto dell'utente. Tutto funziona come previsto, ma che cosa accade se un utente chiama l'API senza un token o con un token non valido? Se ad esempio si prova a eliminare l'intestazione `Authorization`, si noterà che è ancora possibile chiamare l'API. Questo avviene perché Gestione API non convalida il token di accesso a questo punto, ma passa semplicemente l'intestazione `Auhtorization` all'API back-end.

È possibile usare i criteri di [convalida JWT](api-management-access-restriction-policies.md#ValidateJWT) per preautorizzare le richieste in Gestione API convalidando i token di accesso di ogni richiesta in ingresso. Se una richiesta non ha un token valido, viene bloccata da Gestione API e non viene passata al back-end. È ad esempio possibile aggiungere i criteri seguenti alla sezione dei criteri `<inbound>` di `Echo API`. In questo modo, viene verificata l'attestazione dei destinatari in un token di accesso e viene restituito un messaggio di errore se il token non è valido. Per informazioni su come configurare i criteri, vedere [Impostare o modificare criteri](set-edit-policies.md).

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

In questa guida è stata usata la Console per sviluppatori di Gestione API come applicazione client di esempio per chiamare `Echo API` protetta da OAuth 2.0. Per altre informazioni su come creare un'applicazione e implementare il flusso di OAuth 2.0, vedere [Esempi di codice di Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni su [Azure Active Directory e OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md)
* Altre informazioni sui [video](https://azure.microsoft.com/documentation/videos/index/?services=api-management) relativi a Gestione API.
* Per altri metodi di protezione del servizio back-end, vedere [Come proteggere i servizi back-end usando l'autenticazione reciproca dei certificati](api-management-howto-mutual-certificates.md).

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
