---
title: "Avvio rapido: Chiamare un'API Web ASP.NET protetta da Microsoft Identity Platform | Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come chiamare un'API Web ASP.NET protetta da Microsoft Identity Platform da un'applicazione desktop di Windows (WPF).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 6874794dcf33d77d0b03f2a5713bdf42a40d6891
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560911"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Avvio rapido: Chiamare un'API Web ASP.NET protetta da Microsoft Identity Platform

In questa guida di avvio rapido si scarica e si esegue un esempio di codice che illustra come proteggere un'API Web ASP.NET limitando l'accesso alle risorse solo agli account autorizzati. L'esempio supporta l'autorizzazione di account Microsoft personali e di account presenti in qualsiasi organizzazione Azure Active Directory (Azure AD).

L'articolo usa anche un'app Windows Presentation Foundation (WPF) per dimostrare come richiedere un token di accesso per accedere a un'API Web.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Visual Studio 2017 o 2019. Scaricare [Visual Studio gratuitamente](https://www.visualstudio.com/downloads/).

## <a name="clone-or-download-the-sample"></a>Clonare o scaricare l'esempio

È possibile ottenere l'esempio in uno dei due modi seguenti:

* Clonarlo dalla shell o dalla riga di comando:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [Scaricarlo come file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api"></a>Registrare l'API Web

In questa sezione l'API Web viene registrata in **Registrazioni app** nel portale di Azure.

### <a name="choose-your-azure-ad-tenant"></a>Scegliere il tenant di Azure AD

Per registrare le app manualmente, scegliere il tenant di Azure Active Directory (Azure AD) in cui crearle.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account è presente in più tenant di Azure AD, selezionare il proprio profilo in alto a destra, quindi selezionare **Cambia directory**.
1. Impostare la sessione del portale sul tenant di Azure AD da usare.

### <a name="register-the-todolistservice-app"></a>Registrare l'app TodoListService

1. Passare al portale [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:

    1. Nella sezione **Nome** immettere un nome significativo che verrà visualizzato agli utenti dell'app. Immettere ad esempio **AppModelv2-NativeClient-DotNet-TodoListService**.
    1. Per **Tipi di account supportati** selezionare **Account in qualsiasi directory dell'organizzazione**.
    1. Selezionare **Registra** per creare l'applicazione.

1. Nella pagina **Panoramica**  dell'app cercare il valore del campo **ID applicazione (client)** e prenderne nota per un uso futuro. Questo valore sarà necessario per il file di configurazione di Visual Studio per questo progetto, ovvero `ClientId` nel file *TodoListService\Web.config*.

1. Nella sezione **Esporre un'API** selezionare **Aggiungi un ambito**, accettare l'URI dell'ID applicazione proposto (`api://{clientId}`) selezionando **Salva e continua**, quindi immettere le informazioni seguenti:

    1. Per **Nome ambito** immettere **access_as_user**.
    1. Per **Utenti che possono fornire il consenso** assicurarsi che sia selezionata l'opzione **Amministratori e utenti**.
    1. Nella casella **Nome visualizzato per il consenso amministratore** immettere **Accedi a TodoListService come utente**.
    1. Nella casella **Descrizione del consenso dell'amministratore** immettere **Accede all'API Web TodoListService Web API come utente**.
    1. Nella casella **Nome visualizzato per il consenso amministratore** immettere **Accedi a TodoListService come utente**.
    1. Nella casella **Descrizione del consenso dell'utente** immettere **Accede all'API Web TodoListService come utente**.
    1. Per **Stato** mantenere **Abilitato**.
    1. Selezionare **Aggiungi ambito**.

### <a name="configure-the-service-project"></a>Configurare il progetto di servizio

Configurare il progetto di servizio in modo che corrisponda all'API Web registrata, seguendo questa procedura:

1. Aprire la soluzione in Visual Studio e quindi aprire il file *Web.config* nella radice del progetto TodoListService.

1. Sostituire il valore del parametro `ida:ClientId` con il valore di ID client (ID applicazione) dell'applicazione registrata nel portale **Registrazioni app**.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Aggiungere il nuovo ambito al file app.config

Per aggiungere il nuovo ambito al file *app.config* di TodoListClient, procedere come segue:

1. Nella cartella radice del progetto TodoListClient aprire il file *app.config*.

1. Incollare l'ID dell'applicazione appena registrata per il progetto TodoListService nel parametro `TodoListServiceScope`, sostituendo la stringa `{Enter the Application ID of your TodoListService from the app registration portal}`.

  > [!NOTE]
  > Assicurarsi che l'ID applicazione sia nel formato `api://{TodoListService-Application-ID}/access_as_user`, dove`{TodoListService-Application-ID}` è il GUID che rappresenta l'ID applicazione per l'app TodoListService.

## <a name="register-the-todolistclient-client-app"></a>Registrare l'app client TodoListClient

In questa sezione si registra l'app TodoListClient in **Registrazioni app** nel portale di Azure e quindi si configura il codice nel progetto TodoListClient. Se il client e il server vengono considerati *la stessa applicazione* è anche possibile riutilizzare l'applicazione registrata nel passaggio 2. Usare la stessa applicazione se si vuole che gli utenti accedano con un account Microsoft personale.

### <a name="register-the-app"></a>Registrare l'app

Per registrare l'app TodoListClient, procedere come segue:

1. Passare al portale [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:

    1. Nella sezione **Nome** immettere un nome significativo che verrà visualizzato agli utenti dell'app, ad esempio **NativeClient-DotNet-TodoListClient**.
    1. Per **Tipi di account supportati** selezionare **Account in qualsiasi directory dell'organizzazione**.
    1. Selezionare **Registra** per creare l'applicazione.

   > [!NOTE]
   > Nel file *app.config* del progetto TodoListClient il valore predefinito di `ida:Tenant` è impostato su `common`. I valori possibili sono:
   > - `common`: è possibile accedere usando un account aziendale o dell'istituto di istruzione oppure un account Microsoft personale, perché nel passaggio 3b è stata selezionata l'opzione **Account in qualsiasi directory dell'organizzazione**.
   > - `organizations`: è possibile accedere usando un account aziendale o dell'istituto di istruzione.
   > - `consumers`: è possibile accedere solo usando un account Microsoft personale.

1. Nella pagina **Panoramica** selezionare **Autenticazione** e quindi procedere come segue:

    1. In **Configurazioni della piattaforma** selezionare il pulsante **Aggiungi una piattaforma**.
    1. Per **Applicazioni per dispositivi mobili e desktop** selezionare **Applicazioni per dispositivi mobili e desktop**.
    1. Per **URI di reindirizzamento** selezionare la casella di controllo **https://login.microsoftonline.com/common/oauth2/nativeclient** .
    1. Selezionare **Configura**.

1. Selezionare **Autorizzazioni API**, quindi procedere come segue:

    1. Selezionare il pulsante **Aggiungi un'autorizzazione**.
    1. Selezionare la scheda **Le mie API**.
    1. Nell'elenco di API selezionare l'**API AppModelv2-NativeClient-DotNet-TodoListService** oppure il nome immesso per l'API Web.
    1. Selezionare la casella di controllo dell'autorizzazione **access_as_user** se non è già selezionata. Se necessario, usare la casella di ricerca.
    1. Selezionare il pulsante **Aggiungi autorizzazioni**.

### <a name="configure-your-project"></a>Configurare il progetto

Per configurare il progetto TodoListClient, procedere come segue:

1. Nella pagina **Panoramica** del portale **Registrazioni app** copiare il valore di **ID applicazione (client)** .

1. Nella cartella radice del progetto TodoListClient aprire il file *app.config* e quindi incollare il valore di ID applicazione nel parametro `ida:ClientId`.

## <a name="run-your-todolistclient-project"></a>Eseguire il progetto TodoListClient

Per eseguire il progetto TodoListClient, procedere come segue:

1. Premere F5 per aprire il progetto TodoListClient. Verrà visualizzata la pagina del progetto.

1. In alto a destra selezionare **Accedi** e quindi accedere con le stesse credenziali usate per registrare l'applicazione oppure come utente della stessa directory.

   La prima volta che si accede è possibile che venga chiesto di fornire il consenso all'API Web TodoListService.

   Per facilitare l'accesso all'API Web TodoListService e manipolare l'elenco *To-Do*, viene anche richiesto un token di accesso per l'ambito *access_as_user*.

## <a name="pre-authorize-your-client-application"></a>Pre-autorizzare l'applicazione client

Un modo per consentire agli utenti di altre directory di accedere all'API Web consiste nel pre-autorizzare l'accesso all'applicazione client. A tale scopo, aggiungere l'ID applicazione dell'app client all'elenco di applicazioni pre-autorizzate per l'API Web. Aggiungendo un client pre-autorizzato, si autorizza l'accesso degli utenti all'API Web senza la necessità di fornire il consenso. Per pre-autorizzare l'app client, procedere come segue:

1. Nel portale **Registrazioni app** aprire le proprietà dell'app TodoListService.
1. Nella sezione **Esporre un'API** selezionare **Aggiungi applicazione client** in **Applicazioni client autorizzate**.
1. Nella casella **ID client** incollare l'ID applicazione dell'app TodoListClient.
1. Nella sezione **Ambiti autorizzati** selezionare l'ambito per l'API Web `api://<Application ID>/access_as_user`.
1. Selezionare **Aggiungi applicazione**.

### <a name="run-your-project"></a>Eseguire il progetto

1. Premere F5 per eseguire il progetto. Si aprirà l'app TodoListClient.
1. In alto a destra selezionare **Accedi** e quindi accedere usando un account Microsoft personale, come live.com o hotmail.com, oppure un account aziendale o dell'istituto di istruzione.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Facoltativo: Limitare l'accesso a specifici utenti

Per impostazione predefinita, se si segue la procedura precedente, qualsiasi account personale, come outlook.com o live.com, oppure qualsiasi account aziendale o dell'istituto di istruzione di un'organizzazione che abbia eseguito l'integrazione con Azure AD può richiedere i token e accedere all'API Web.

Per specificare chi può accedere all'applicazione, usare una delle opzioni seguenti:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Opzione 1: Limitare l'accesso a una singola organizzazione (singolo tenant)

È possibile limitare l'accesso all'applicazione ai soli account utente inclusi in un singolo tenant di Azure AD, inclusi gli *account guest* di tale tenant. Questo scenario è comune per le *applicazioni line-of-business*.

1. Aprire il file *App_Start\Startup.Auth* e quindi cambiare il valore dell'endpoint dei metadati passato a `OpenIdConnectSecurityTokenProvider` sostituendolo con `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"`. È anche possibile usare il nome del tenant, ad esempio `contoso.onmicrosoft.com`.
2. Nello stesso file impostare la proprietà `ValidIssuer` in `TokenValidationParameters` su `"https://sts.windows.net/{Tenant ID}/"` e l'argomento `ValidateIssuer` su `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opzione 2: Usare un metodo personalizzato per convalidare le autorità di certificazione

È possibile implementare un metodo personalizzato per convalidare le autorità di certificazione usando il parametro `IssuerValidator`. Per altre informazioni su come usare questo parametro, vedere la [classe TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet&preserve-view=true).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sullo scenario dell'API Web protetta supportato da Microsoft Identity Platform:
> [!div class="nextstepaction"]
> [Scenario: API Web protetta](scenario-protected-web-api-overview.md)
