---
title: User migration approaches in Azure Active Directory B2C
description: Discusses both core and advanced concepts on user migration using the Azure AD Graph API, and optionally using Azure AD B2C custom policies.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9c01e22cfa96321994c16df6b61a52ebd4137549
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322924"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>Migrate users to Azure Active Directory B2C

When you migrate your identity provider to Azure Active Directory B2C (Azure AD B2C), you might also need to migrate the user accounts. Questo articolo spiega come eseguire la migrazione degli account utente esistenti da qualsiasi provider di identità ad Azure AD B2C. The article is not meant to be prescriptive, but rather, it describes a few scenarios. Lo sviluppatore è responsabile dell'idoneità di ogni approccio.

## <a name="user-migration-flows"></a>Flussi di migrazione degli utenti

With Azure AD B2C, you can migrate users through the [Azure AD Graph API][B2C-GraphQuickStart]. Il processo di migrazione degli utenti rientra in due flussi:

- **Pre-migrazione**: questo flusso si applica quando si ha accesso alle credenziali di un utente (nome utente e password) o se le credenziali sono crittografate ma è possibile decrittografarle. Il processo di pre-migrazione include la lettura degli utenti dal vecchio provider di identità e la creazione di nuovi account nella directory di Azure AD B2C.

- **Pre-migrazione e reimpostazione della password**: questo flusso si applica quando la password di un utente non è accessibile, ad esempio:
  - La password è archiviata in formato HASH.
  - La password è archiviata in un provider di identità a cui non è possibile accedere. Il vecchio provider di identità convalida le credenziali dell'utente chiamando un servizio Web.

In entrambi i flussi, eseguire prima il processo di pre-migrazione, leggere gli utenti dal vecchio provider di identità e creare nuovi account nella directory di Azure AD B2C. If you don't have the password, you create the account by using a password that's generated randomly. Chiedere quindi all'utente di cambiare la password oppure al primo accesso Azure AD B2C chiederà all'utente di reimpostarla.

## <a name="password-policy"></a>Criteri password

I criteri password di Azure AD B2C (per gli account locali) si basano su quelli di Azure AD. The Azure AD B2C sign-up or sign-in and password reset policies use the "strong" password strength and don't expire any passwords. Per altre informazioni, vedere [Criteri password in Azure AD][AD-PasswordPolicies].

Se gli account di cui si vuole eseguire la migrazione hanno un livello di complessità della password inferiore al [livello avanzato applicato da Azure AD B2C][AD-PasswordPolicies], è possibile disabilitare il requisito per la password complessa. Per modificare i criteri password predefiniti, impostare la proprietà `passwordPolicies` su `DisableStrongPassword`. È ad esempio possibile modificare la richiesta di creazione utente come segue:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Passaggio 1: Usare l'API Graph di Azure AD per eseguire la migrazione degli utenti

Creare l'account utente di Azure AD B2C con l'API Graph (con la password personale o con una password casuale). Questa sezione descrive il processo di creazione di account utente nella directory di Azure AD B2C usando l'API Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Passaggio 1.1: Registrare l'applicazione nel tenant

Per comunicare con l'API Graph è innanzitutto necessario avere un account di servizio con privilegi amministrativi. In Azure AD, you register an application and enable write access to the directory. The application credentials are the **Application ID** and **Application Secret**. L'applicazione agisce autonomamente, non come utente, per chiamare l'API Graph.

First, register an application that you can use for management tasks like user migration.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Step 1.2: Grant administrative permission to your application

Next, grant the application the Azure AD Graph API permissions required for writing to the directory.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Step 1.3: Create the application secret

Create a client secret (key) for use by the user migration application that you configure in a later step.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Now you have an application with permissions to create, read, and update users in your Azure AD B2C tenant.

### <a name="step-14-optional-environment-cleanup"></a>Passaggio 1.4: (Facoltativo) Pulizia dell'ambiente

The *Read and write directory data* permission does *not* include the right to delete users. Per consentire all'applicazione di eliminare utenti, ovvero di pulire l'ambiente, è necessario un passaggio aggiuntivo che prevede l'esecuzione di PowerShell per impostare le autorizzazioni di amministratore account utente. In caso contrario, è possibile passare alla sezione successiva.

> [!IMPORTANT]
> È necessario usare un account di amministratore tenant B2C che sia *locale* in relazione al tenant B2C. The account name syntax is *admin\@contosob2c.onmicrosoft.com*.

In this PowerShell script, which requires the [Azure AD PowerShell V2 module][AD-Powershell], do the following:

1. Connettersi al servizio online. A tale scopo, eseguire il cmdlet `Connect-AzureAD` al prompt dei comandi di Windows PowerShell e immettere le credenziali.

1. Usare l'**ID applicazione** per assegnare all'applicazione il ruolo di amministratore di account utente. Questi ruoli hanno identificatori noti, pertanto è sufficiente immettere l'**ID applicazione** nello script.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Modificare il valore `$AppId` con l'**ID applicazione** di Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Passaggio 2: Esempio di applicazione di pre-migrazione

You can find the pre-migration code sample in the community-maintained `azure-ad-b2c/user-migration` GitHub repository:

[azure-ad-b2c/user-migration/pre-migration][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>Passaggio 2.1: Modificare il file dei dati di migrazione

L'app di esempio usa un file JSON che contiene dati utente fittizi. Dopo aver eseguito correttamente l'esempio è possibile modificare il codice per usare i dati del proprio database. In alternativa, è possibile esportare il profilo utente in un file JSON e impostare l'app in modo che usi questo file.

Per modificare il file JSON, aprire la soluzione Visual Studio `AADB2C.UserMigration.sln`. Nel progetto `AADB2C.UserMigration` aprire il file `UsersData.json`.

![Portion of UsersData.json file showing JSON blocks of two users](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Come si può notare, il file contiene un elenco di entità utente. Ogni entità utente ha le proprietà seguenti:

- email
- displayName
- firstName
- lastName
- password (il campo può essere vuoto)

> [!NOTE]
> In fase di compilazione, Visual Studio copia il file nella directory `bin`.

### <a name="step-22-configure-the-application-settings"></a>Passaggio 2.2: Configurare le impostazioni dell'applicazione

Nel progetto `AADB2C.UserMigration` aprire il file *App.config*. Sostituire le impostazioni dell'app seguenti con valori personalizzati:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - L'uso della stringa di connessione di una tabella di Azure è descritto nelle sezioni successive.
> - Il nome del tenant B2C è il dominio immesso durante la creazione del tenant ed è visualizzato nel portale di Azure. Il nome del tenant termina in genere con il suffisso *.onmicrosoft.com* (ad esempio, *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Passaggio 2.3: Eseguire il processo di pre-migrazione

Fare clic con il pulsante destro del mouse sulla soluzione `AADB2C.UserMigration` e ricompilare l'esempio. Se l'operazione riesce, sarà disponibile un file eseguibile `UserMigration.exe` in `AADB2C.UserMigration\bin\Debug\net461`. Per eseguire il processo di migrazione, usare un parametro della riga di comando tra i seguenti:

- Per **eseguire la migrazione degli utenti con password**, usare il comando `UserMigration.exe 1`.

- Per **eseguire la migrazione degli utenti con una password casuale**, usare il comando `UserMigration.exe 2`. Questa operazione crea anche un'entità tabella di Azure. Più avanti verranno configurati i criteri per chiamare il servizio API REST. Il servizio usa una tabella di Azure per monitorare e gestire il processo di migrazione.

![Command Prompt window showing output of UserMigration.exe command](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Passaggio 2.4: Controllare il processo di pre-migrazione

Per convalidare la migrazione, usare uno dei due metodi seguenti:

- Per cercare un utente in base al nome visualizzato, usare il portale di Azure:

   1. Open **Azure AD B2C**, and then select **Users**.
   1. Nella casella di ricerca digitare il nome visualizzato dell'utente e quindi visualizzarne il profilo.

- To retrieve a user by sign-in email address, use the sample application:

   1. Eseguire il comando seguente:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > È anche possibile recuperare un utente in base al nome visualizzato usando il comando seguente: `UserMigration.exe 4 "<Display name>"`.

   1. Aprire il file UserProfile.json in un editor JSON per visualizzare le informazioni dell'utente.

      ![UserProfile.json file open in the Visual Studio Code editor](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Passaggio 2.5: (Facoltativo) Pulizia dell'ambiente

Per eseguire la pulizia del tenant Azure AD e rimuovere utenti dalla directory di Azure AD, eseguire il comando `UserMigration.exe 5`.

> [!NOTE]
> * Per eseguire la pulizia del tenant, configurare le autorizzazioni di amministratore account utente per l'applicazione.
> * L'app di migrazione di esempio esegue la pulizia di tutti gli utenti elencati nel file JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Passaggio 2.6: Accedere con gli utenti di cui è stata eseguita la migrazione (con password)

Dopo aver eseguito il processo di pre-migrazione con le password degli utenti, gli account saranno pronti e gli utenti potranno accedere all'applicazione usando Azure AD B2C. Se l'accesso alle password degli utenti non è disponibile, passare alla sezione successiva.

## <a name="step-3-help-users-reset-their-password"></a>Passaggio 3: Aiutare gli utenti a reimpostare la password

Nel caso in cui si esegua la migrazione degli utenti con una password casuale, questi dovranno reimpostare la propria password. Per facilitare la reimpostazione della password, inviare un messaggio di benvenuto contenente un collegamento per reimpostare la password.

To get the link to your password reset policy, follow these steps. This procedure assumes you've previously created a password reset [custom policy](active-directory-b2c-get-started-custom.md).

1. Select the directory containing your Azure AD B2C tenant by using the **Directory + subscription** filter in the upper-right section of the [Azure portal](https://portal.azure.com).
1. Select **Azure AD B2C** in the left-hand menu (or from within **All services**).
1. Under **Policies**, select **Identity Experience Framework**.
1. Select your password reset policy. For example, *B2C_1A_PasswordReset*.
1. Select your application in the **Select application** drop-down.

    > [!NOTE]
    > **Run now** requires at least one application to be registered in your tenant. To learn how to register applications, see [Tutorial: Register an application in Azure Active Directory B2C][B2C-AppRegister].

1. Copy the URL shown in the **Run now endpoint** text box, and then send it to your users.

    ![Password reset policy page with Run now endpoint highlighted](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Passaggio 4: (Facoltativo) Modificare i criteri per controllare e impostare lo stato di migrazione degli utenti

> [!NOTE]
> Per controllare e modificare lo stato di migrazione degli utenti è necessario usare criteri personalizzati. The set-up instructions from [Get started with custom policies][B2C-GetStartedCustom] must be completed.

Quando gli utenti provano a eseguire l'accesso senza prima reimpostare la password, i criteri dovrebbero restituire un messaggio di errore descrittivo, ad esempio:

> *Your password has expired. To reset it, select the Reset Password link.*

Questo passaggio facoltativo richiede l'uso di criteri personalizzati in Azure AD B2C, come descritto nell'articolo [Introduzione ai criteri personalizzati][B2C-GetStartedCustom].

In questa sezione vengono modificati i criteri per controllare lo stato di migrazione dell'utente all'accesso. Se l'utente non ha cambiato la password, verrà restituito un messaggio di errore HTTP 409 in cui si chiede all'utente di selezionare il collegamento **Password dimenticata?** .

Per monitorare il cambio della password, usare una tabella di Azure. L'esecuzione del processo di pre-migrazione con il parametro `2` della riga di comando crea un'entità utente in una tabella di Azure. Il servizio esegue le operazioni seguenti:

- In fase di accesso, i criteri di Azure AD B2C richiamano il servizio RESTful di migrazione inviando un messaggio e-mail come attestazione di input. Il servizio cerca l'indirizzo e-mail nella tabella di Azure. Se l'indirizzo esiste, il servizio genera un messaggio di errore che richiede di *cambiare la password*.

- Dopo che l'utente ha cambiato la password, rimuovere l'entità dalla tabella di Azure.

> [!NOTE]
> L'uso di una tabella di Azure consente di semplificare l'esempio. È possibile archiviare lo stato di migrazione in qualsiasi database o come proprietà personalizzata nell'account Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: Aggiornare l'impostazione dell'applicazione

1. Per testare la demo dell'API RESTful, aprire `AADB2C.UserMigration.sln` in Visual Studio.
1. In the `AADB2C.UserMigration.API` project, open the *Web.config* file. Sostituire l'impostazione con quella configurata nel [passaggio 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Passaggio 4.2: Distribuire l'applicazione Web nel Servizio app di Azure

In Esplora soluzioni fare clic con il pulsante destro del mouse su `AADB2C.UserMigration.API` e scegliere "Pubblica". Seguire le istruzioni per la pubblicazione in Servizio app di Azure. Per altre informazioni, vedere [Distribuire l'app nel Servizio app di Azure][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Passaggio 4.3: Aggiungere un profilo tecnico e la relativa convalida ai criteri

1. In Esplora soluzioni espandere "Elementi di soluzione" e aprire il file dei criteri *TrustFrameworkExtensions.xml*.
1. Modificare i campi `TenantId`, `PublicPolicyUri` e `<TenantId>` da `yourtenant.onmicrosoft.com` al nome del tenant.
1. Under the `<TechnicalProfile Id="login-NonInteractive">` element, replace all instances of `ProxyIdentityExperienceFrameworkAppId` and `IdentityExperienceFrameworkAppId` with the Application IDs configured in [Getting started with custom policies][B2C-GetStartedCustom].
1. Nel nodo `<ClaimsProviders>` trovare il frammento di codice XML seguente. Modificare il valore di `ServiceUrl` in modo che punti all'URL di Servizio app di Azure.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Il profilo tecnico precedente definisce un'attestazione di input: `signInName` (invia come e-mail). All'accesso l'attestazione viene inviata all'endpoint RESTful.

After you define the technical profile for your RESTful API, configure the existing `SelfAsserted-LocalAccountSignin-Email` technical profile to additionally call your REST API technical profile by overriding it within your *TrustFrameworkExtensions.xml* file:

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

Then, change the `Id` of the `LocalAccountSignIn` technical profile to `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Passaggio 4.4: Caricare i criteri nel tenant

1. Nel [portale di Azure][Portal] passare al [contesto del tenant di Azure AD B2C][B2C-NavContext] e quindi selezionare **Azure AD B2C**.
1. Fare clic su **Framework dell'esperienza di gestione delle identità**.
1. Selezionare **Tutti i criteri**.
1. Selezionare **Carica criteri**.
1. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.
1. Caricare il file *TrustFrameworkExtensions.xml* e assicurarsi che superi la convalida.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Passaggio 4.5: Testare i criteri personalizzati tramite Esegui adesso

1. Select **Azure AD B2C**, and then select **Identity Experience Framework**.
1. Aprire *B2C_1A_signup_signin*, i criteri personalizzati della relying party caricati in precedenza, quindi selezionare **Esegui adesso**.
1. Enter the credentials of one of the migrated users, and then select **Sign In**. L'API REST genera il messaggio di errore seguente:

    ![Sign-in Sign-up page showing the change password error message](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Passaggio 4.6: (Facoltativo) Risoluzione dei problemi dell'API REST

È possibile visualizzare e monitorare le informazioni di registrazione quasi in tempo reale.

1. Nel menu delle impostazioni dell'applicazione RESTful, in **Monitoraggio** selezionare **Log di diagnostica**.
1. Impostare **Registrazione applicazioni (file system)** su **Attiva**.
1. Impostare il **Livello** su **Dettagliato**.
1. Selezionare **Salva**

    ![Diagnostics logs configuration page in Azure portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. Nel menu **Impostazioni** selezionare **Flusso di registrazione**.
1. Controllare l'output dell'API RESTful.

> [!IMPORTANT]
> Usare i log di diagnostica solo in fase di sviluppo e test. L'output dell'API RESTful può contenere informazioni riservate che non devono essere esposte in produzione.

## <a name="optional-download-the-complete-policy-files"></a>(Facoltativo) Scaricare i file dei criteri completi

After you complete the [Get started with custom policies][B2C-GetStartedCustom] walk-through, we recommend that you build your scenario by using your own custom policy files. For your reference, we have provided [sample policy files][UserMigrationSample-policy].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
