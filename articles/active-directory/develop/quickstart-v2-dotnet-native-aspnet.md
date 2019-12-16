---
title: Chiamare un'API Web ASP.NET protetta di Azure AD - Microsoft Identity Platform
description: Questo avvio rapido illustra come chiamare un'API Web ASP.NET protetta da Azure Active Directory da un'applicazione desktop di Windows (WPF). Il client WPF autentica un utente, richiede un token di accesso e chiama l'API Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe3301c3c91343277997be1ee554ced76884274a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963308"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-azure-ad"></a>Guida introduttiva: Chiamare un'API Web ASP.NET protetta da Azure AD

In questo avvio rapido viene esposta e protetta un'API Web in modo che solo gli utenti autenticati possano accedervi. Questo esempio mostra come esporre un'API Web ASP.NET in modo che accetti accessi sia di account personali (ad esempio, outlook.com, live.com e altri) sia di account aziendali o di istituti di istruzione di proprietà di aziende oppure di organizzazioni con Azure Active Directory integrato.

L'esempio include anche un client dell'applicazione desktop di Windows (WPF) che illustra come richiedere un token di accesso per accedere a un'API Web.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo esempio, sono necessari:

* Visual Studio 2017 o 2019.  Scaricare [Visual Studio gratuitamente](https://www.visualstudio.com/downloads/).

* [Account Microsoft](https://www.outlook.com) o adesione al programma [Office 365 Developer Program](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Scaricare o clonare questo esempio

È possibile clonare questo esempio dalla shell o dalla riga di comando:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

In alternativa, è possibile [scaricare l'esempio come file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Registrare l'API Web nel portale di registrazione delle applicazioni

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Scegliere il tenant di Azure AD in cui si vuole creare le applicazioni

Se si vuole registrare manualmente le app, come primo passaggio è necessario:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account è presente in più tenant di Azure AD, selezionare il proprio profilo nell'angolo in alto a destra nel menu disponibile nella parte superiore della pagina e quindi **cambiare directory**.
   Impostare la sessione del portale sul tenant di Azure AD desiderato.

### <a name="register-the-service-app-todolistservice"></a>Registrare l'app del servizio (TodoListService)

1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:
   - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Impostare **Tipi di account supportati** su **Account in qualsiasi directory organizzativa**.
   - Selezionare **Registra** per creare l'applicazione.

1. Nella pagina **Panoramica**  dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota. Questo valore sarà necessario per configurare il file di configurazione di Visual Studio per questo progetto (`ClientId` in `TodoListService\Web.config`).
1. Selezionare la sezione **Esporre un'API** e:
   - Selezionare **Aggiungi un ambito**.
   - Accettare l'URI dell'ID applicazione proposto (api://{clientId}) selezionando **Salva e continua**.
   - Immettere i parametri seguenti:
     - Come valore di **Nome ambito** usare `access_as_user`
     - Verificare che sia selezionata l'opzione **Amministratori e utenti** per **Utenti che possono fornire il consenso**
     - In **Nome visualizzato per il consenso amministratore** digitare `Access TodoListService as a user`
     - In **Descrizione del consenso amministratore** digitare `Accesses the TodoListService Web API as a user`
     - In **Nome visualizzato per il consenso utente** digitare `Access TodoListService as a user`
     - In **Descrizione del consenso utente** digitare `Accesses the TodoListService Web API as a user`
     - Mantenere **Stato** impostato su **Abilitato**
     - Selezionare **Aggiungi ambito**

### <a name="configure-the-service-and-client-projects-to-match-the-registered-web-api"></a>Configurare i progetti del servizio e del client in modo che corrispondano all'API Web registrata 

1. Aprire la soluzione in Visual Studio e quindi aprire il file **Web.config** nella radice del progetto **TodoListService**.
1. Sostituire il valore del parametro `ida:ClientId` con il valore di **ID client (ID applicazione)** dell'applicazione appena registrata nel portale di registrazione delle applicazioni.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Aggiungere il nuovo ambito al file app config di *TodoListClient*

1. Aprire il file **app.config** che si trova nella cartella radice del progetto **TodoListClient** e quindi incollare il valore di **ID applicazione** dell'applicazione appena registrata per *TodoListService* nel parametro `TodoListServiceScope` sostituendo la stringa `{Enter the Application ID of your TodoListService from the app registration portal}`.

   > Note: Assicurarsi che usi il formato seguente:
   >
   > `api://{TodoListService-Application-ID}/access_as_user` 
   >
   >dove {TodoListService-Application-ID} è il GUID che rappresenta l'ID applicazione per TodoListService.

## <a name="register-the-client-app-todolistclient"></a>Registrare l'app client (TodoListClient)

In questo passaggio viene configurato il progetto *TodoListClient* registrando una nuova applicazione nel portale di registrazione delle applicazioni. Nei casi in cui il client e il server vengono considerati *la stessa applicazione* è anche possibile riutilizzare la stessa applicazione registrata nel passaggio 2. È necessario usare la stessa applicazione se si vuole che gli utenti accedano con gli account personali Microsoft.

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registrare l'applicazione *TodoListClient* nel *portale di registrazione delle applicazioni*

1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:
   - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `NativeClient-DotNet-TodoListClient`.
   - Impostare **Tipi di account supportati** su **Account in qualsiasi directory organizzativa**.
   - Selezionare **Registra** per creare l'applicazione.
1. Nella pagina Panoramica dell'app selezionare la sezione **Autenticazione**.
   - Nella sezione **URL di reindirizzamento** | **URL di reindirizzamento suggeriti per client pubblici (dispositivi mobili, desktop)** selezionare **urn:ietf:wg:oauth:2.0:oob**
   - Selezionare **Salva**.
1. Selezionare la sezione **Autorizzazioni API**
   - Fare clic sul pulsante **Aggiungi un'autorizzazione** e quindi
   - Selezionare la scheda **Le mie API**.
   - Nell'elenco di API selezionare l'API `AppModelv2-NativeClient-DotNet-TodoListService API` oppure il nome immesso per l'API Web.
   - Selezionare l'autorizzazione **access_as_user** se non è già selezionata. Se necessario, usare la casella di ricerca.
   - Selezionare il pulsante **Aggiungi autorizzazioni**

### <a name="configure-your-todolistclient-project"></a>Configurare il progetto *TodoListClient*

1. Nella pagina **Panoramica** del *portale di registrazione delle applicazioni* copiare il valore di **ID applicazione (client)** .
1. Aprire il file **app.config** che si trova nella cartella radice del progetto **TodoListClient** e quindi incollare il valore nel valore del parametro `ida:ClientId`.

## <a name="run-your-project"></a>Eseguire il progetto

1. Premere `<F5>` per eseguire il progetto. Il progetto *TodoListClient* dovrebbe aprirsi.
1. Selezionare **Accedi** in alto a destra e accedere con lo stesso utente usato per registrare l'applicazione oppure con un utente nella stessa directory.
1. A questo punto, se si accede per la prima volta, è possibile che venga richiesto di fornire il consenso per l'API Web di *TodoListService*.
1. L'accesso richiede anche il token di accesso all'ambito *access_as_user* per accedere all'API Web di *TodoListService* e modificare l'elenco *To-Do*.

## <a name="pre-authorize-your-client-application"></a>Pre-autorizzare l'applicazione client

Uno dei modi per consentire agli utenti di altre directory di accedere all'API Web consiste nel *pre-autorizzare* le applicazioni client ad accedere all'API Web aggiungendo gli ID applicazione delle applicazioni client all'elenco delle applicazioni *pre-autorizzate* per l'API Web. Se si aggiunge un client pre-autorizzato, non sarà necessario che l'utente acconsenta all'uso dell'API Web. Seguire questa procedura per pre-autorizzare l'applicazione Web:

1. Tornare al *portale di registrazione delle applicazioni* e aprire le proprietà di **TodoListService**.
1. Nella sezione **Esporre un'API** fare clic su **Aggiungi applicazione client** in *Applicazioni client autorizzate*.
1. Nel campo *ID client* incollare l'ID applicazione di `TodoListClient`.
1. Nella sezione *Ambiti autorizzati* selezionare l'ambito per l'API Web `api://<Application ID>/access_as_user`.
1. Fare clic sul pulsante **Aggiungi applicazione** nella parte inferiore della pagina.

## <a name="run-your-project"></a>Eseguire il progetto

1. Premere `<F5>` per eseguire il progetto. Il progetto *TodoListClient* dovrebbe aprirsi.
1. Selezionare **Accedi** in alto a destra (oppure Cancella cache/Accedi) e quindi accedere usando un account Microsoft personale (live.com o hotmail.com) oppure un account aziendale o dell'istituto di istruzione.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Facoltativo: Limitare l'accesso all'applicazione

Per impostazione predefinita, quando si carica questo esempio di codice e si configura l'applicazione per l'uso dell'endpoint Azure Active Directory v2 seguendo i passaggi precedenti, sia gli account personali, ad esempio outlook.com, live.com e altri, sia gli account aziendali o dell'istituto di istruzione di qualsiasi organizzazione integrata con Azure AD possono richiedere token e accedere all'API Web. 

Per limitare l'accesso all'applicazione, usare una delle opzioni seguenti:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Opzione 1: Limitare l'accesso a una singola organizzazione (singolo tenant)

È possibile limitare l'accesso all'applicazione ai soli account utente inclusi in un singolo tenant di Azure AD, inclusi gli *account Guest* di tale tenant. Questa opzione è uno scenario comune per le *applicazioni line-of-business*:

1. Aprire il file **App_Start\Startup.Auth** e modificare il valore dell'endpoint di metadati passato in `OpenIdConnectSecurityTokenProvider` a `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (è anche possibile usare il nome del tenant, ad esempio `contoso.onmicrosoft.com`).
2. Nello stesso file impostare la proprietà `ValidIssuer` in `TokenValidationParameters` su `"https://sts.windows.net/{Tenant ID}/"` e l'argomento `ValidateIssuer` su `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opzione 2: Usare un metodo personalizzato per convalidare le autorità di certificazione

È possibile implementare un metodo personalizzato per convalidare le autorità di certificazione usando il parametro **IssuerValidator**. Per altre informazioni su come usare questo parametro, vedere la [classe TokenValidationParameters class](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sullo scenario dell'API Web protetta supportato da Microsoft Identity Platform:
> [!div class="nextstepaction"]
> [Scenario: API Web protetta](scenario-protected-web-api-overview.md)
