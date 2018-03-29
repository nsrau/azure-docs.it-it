---
title: 'Azure Active Directory B2C: approcci alla migrazione degli utenti'
description: Discutere concetti di base e avanzati sulla migrazione degli utenti usando l'API Graph e, facoltativamente, i criteri personalizzati di Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/04/2017
ms.author: davidmu
ms.openlocfilehash: be80ea534be6de4fad2b072cf531669f45eda527
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: migrazione degli utenti
Quando si esegue la migrazione del provider di identità ad Azure Active Directory B2C (Azure AD B2C) potrebbe essere necessario eseguire anche la migrazione dell'account utente. Questo articolo spiega come eseguire la migrazione degli account utente esistenti da qualsiasi provider di identità ad Azure AD B2C. Questo articolo non fornisce informazioni prescrittive, ma descrive piuttosto due dei diversi approcci possibili. Lo sviluppatore è responsabile dell'idoneità di ogni approccio.

## <a name="user-migration-flows"></a>Flussi di migrazione degli utenti
Azure AD B2C consente di eseguire la migrazione degli utenti usando l'[API Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Il processo di migrazione degli utenti rientra in due flussi:

* **Pre-migrazione**: questo flusso si applica quando si ha accesso alle credenziali di un utente (nome utente e password) o se le credenziali sono crittografate ma è possibile decrittografarle. Il processo di pre-migrazione include la lettura degli utenti dal vecchio provider di identità e la creazione di nuovi account nella directory di Azure AD B2C.

* **Pre-migrazione e reimpostazione della password**: questo flusso si applica quando la password di un utente non è accessibile, Ad esempio: 
    * La password è archiviata in formato HASH.
    * La password è archiviata in un provider di identità a cui non è possibile accedere. Il vecchio provider di identità convalida le credenziali dell'utente chiamando un servizio Web.

In entrambi i flussi, eseguire prima il processo di pre-migrazione, leggere gli utenti dal vecchio provider di identità e creare nuovi account nella directory di Azure AD B2C. Se la password non è disponibile, creare l'account usando una password generata in modo casuale. Chiedere quindi all'utente di cambiare la password oppure al primo accesso Azure AD B2C chiederà all'utente di reimpostarla.

## <a name="password-policy"></a>Criteri password
I criteri password di Azure AD B2C (per gli account locali) si basano su quelli di Azure AD. I criteri di iscrizione o accesso e reimpostazione della password di Azure AD B2C usano un livello di complessità "avanzato" e non prevedono la scadenza delle password. Per altre informazioni, vedere [Criteri password in Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx).

Se gli account di cui si vuole eseguire la migrazione hanno un livello di complessità della password inferiore al [livello avanzato applicato da Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), è possibile disabilitare il requisito per la password complessa. Per modificare i criteri password predefiniti, impostare la proprietà `passwordPolicies` su `DisableStrongPassword`. È ad esempio possibile modificare la richiesta di creazione utente come segue: 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-graph-api-to-migrate-users"></a>Passaggio 1: Usare l'API Graph per eseguire la migrazione degli utenti
Creare l'account utente di Azure AD B2C con l'API Graph (con la password personale o con una password casuale). Questa sezione descrive il processo di creazione di account utente nella directory di Azure AD B2C usando l'API Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Passaggio 1.1: Registrare l'applicazione nel tenant
Per comunicare con l'API Graph è innanzitutto necessario avere un account di servizio con privilegi amministrativi. In Azure AD registrare un'applicazione e l'autenticazione ad Azure AD. Le credenziali dell'applicazione sono l'**ID applicazione** e il **Segreto dell'applicazione**. L'applicazione agisce autonomamente, non come utente, per chiamare l'API Graph.

Per prima cosa registrare l'applicazione di migrazione in Azure AD. Creare quindi la chiave dell'applicazione (segreto) e impostare l'applicazione con privilegi di scrittura.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Scegliere il tenant Azure AD **B2C** selezionando il proprio account nella parte superiore destra della finestra.

3. Nel riquadro sinistro selezionare **Azure Active Directory** (non Azure AD B2C). Per individuarlo potrebbe essere necessario selezionare **Altri servizi**.

4. Selezionare **Registrazioni per l'app**.

5. Selezionare **Registrazione nuova applicazione**.

    ![Registrazione nuova applicazione](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

6. Creare una nuova applicazione seguendo questa procedura:
    * Per **Nome** usare **B2CUserMigration** o qualsiasi altro nome.
    * Per **Tipo di applicazione** usare **App Web/API**.
    * Per **URL di accesso** usare **https://localhost** (non pertinente per questa applicazione).
    * Selezionare **Create**.

7. Dopo aver creato l'applicazione, nell'elenco **Applicazioni** selezionare quella appena creata, **B2CUserMigration**.

8. Selezionare **Proprietà**, copiare l'**ID applicazione** e salvarlo per un uso successivo.

### <a name="step-12-create-the-application-secret"></a>Passaggio 1.2: Creare il segreto dell'applicazione
1. Nella finestra **App registrata** del portale di Azure selezionare **Chiavi**.

2. Aggiungere una nuova chiave (anche nota come segreto client) e quindi copiarla per un uso successivo.

    ![ID applicazione e Chiavi](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Passaggio 1.3: Concedere l'autorizzazione amministrativa all'applicazione
1. Nella finestra **App registrata** del portale di Azure selezionare **Autorizzazioni necessarie**.

2. Selezionare **Windows Azure Active Directory**.

3. Nel riquadro **Abilita accesso** in **Autorizzazioni applicazione** selezionare **Leggi e scrivi i dati della directory** e quindi **Salva**.

4. Nel riquadro **Autorizzazioni necessarie** selezionare **Concedi autorizzazioni**.

    ![Autorizzazioni dell'applicazione](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

A questo punto è disponibile un'applicazione con le autorizzazioni per creare, leggere e aggiornare gli utenti dal tenant Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Passaggio 1.4: (Facoltativo) Pulizia dell'ambiente
Le autorizzazioni di lettura e scrittura dei dati della directory *non* includono il diritto di eliminare utenti. Per consentire all'applicazione di eliminare utenti, ovvero di pulire l'ambiente, è necessario un passaggio aggiuntivo che prevede l'esecuzione di PowerShell per impostare le autorizzazioni di amministratore account utente. In caso contrario, è possibile passare alla sezione successiva.

> [!IMPORTANT]
> È necessario usare un account di amministratore tenant B2C che sia *locale* in relazione al tenant B2C. La sintassi del nome dell'account è *admin@contosob2c.onmicrosoft.com*.

>[!NOTE]
> Lo script di PowerShell seguente richiede [Azure Active Directory PowerShell versione 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

In questo script di PowerShell eseguire le operazioni seguenti:
1. Connettersi al servizio online. A tale scopo, eseguire il cmdlet `Connect-AzureAD` al prompt dei comandi di Windows PowerShell e immettere le credenziali. 

2. Usare l'**ID applicazione** per assegnare all'applicazione il ruolo di amministratore di account utente. Questi ruoli hanno identificatori noti, pertanto è sufficiente immettere l'**ID applicazione** nello script.

```PowerShell
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
[Scaricare ed eseguire il codice di esempio](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). È possibile scaricarlo come file con estensione zip.

### <a name="step-21-edit-the-migration-data-file"></a>Passaggio 2.1: Modificare il file dei dati di migrazione
L'app di esempio usa un file JSON che contiene dati utente fittizi. Dopo aver eseguito correttamente l'esempio è possibile modificare il codice per usare i dati del proprio database. In alternativa, è possibile esportare il profilo utente in un file JSON e impostare l'app in modo che usi questo file.

Per modificare il file JSON, aprire la soluzione Visual Studio `AADB2C.UserMigration.sln`. Nel progetto `AADB2C.UserMigration` aprire il file `UsersData.json`.

![File dei dati utente](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Come si può notare, il file contiene un elenco di entità utente. Ogni entità utente ha le proprietà seguenti:
* email
* displayName
* firstName
* lastName
* password (il campo può essere vuoto)

> [!NOTE]
> In fase di compilazione, Visual Studio copia il file nella directory `bin`.

### <a name="step-22-configure-the-application-settings"></a>Passaggio 2.2: Configurare le impostazioni dell'applicazione
Nel progetto `AADB2C.UserMigration` aprire il file *App.config*. Sostituire le impostazioni dell'app seguenti con valori personalizzati:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
    
</appSettings>
```

> [!NOTE]
> * L'uso della stringa di connessione di una tabella di Azure è descritto nelle sezioni successive.
> * Il nome del tenant B2C è il dominio immesso durante la creazione del tenant ed è visualizzato nel portale di Azure. Il nome del tenant termina in genere con il suffisso *.onmicrosoft.com* (ad esempio, *contosob2c.onmicrosoft.com*).
>

### <a name="step-23-run-the-pre-migration-process"></a>Passaggio 2.3: Eseguire il processo di pre-migrazione
Fare clic con il pulsante destro del mouse sulla soluzione `AADB2C.UserMigration` e ricompilare l'esempio. Se l'operazione riesce, sarà disponibile un file eseguibile `UserMigration.exe` in `AADB2C.UserMigration\bin\Debug`. Per eseguire il processo di migrazione, usare un parametro della riga di comando tra i seguenti:

* Per **eseguire la migrazione degli utenti con password**, usare il comando `UserMigration.exe 1`.

* Per **eseguire la migrazione degli utenti con una password casuale**, usare il comando `UserMigration.exe 2`. Questa operazione crea anche un'entità tabella di Azure. Più avanti verranno configurati i criteri per chiamare il servizio API REST. Il servizio usa una tabella di Azure per monitorare e gestire il processo di migrazione.

![Demo del processo di migrazione](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Passaggio 2.4: Controllare il processo di pre-migrazione
Per convalidare la migrazione, usare uno dei due metodi seguenti:

* Per cercare un utente in base al nome visualizzato, usare il portale di Azure:
    
    a. Aprire **Azure AD B2C** e quindi selezionare **Utenti e gruppi**.

    b. Nella casella di ricerca digitare il nome visualizzato dell'utente e quindi visualizzarne il profilo. 

* Per recuperare un utente in base all'indirizzo e-mail di accesso, usare questa applicazione di esempio:

    a. Eseguire il comando seguente:

    ```Console
        UserMigration.exe 3 {email address}
    ```
        
    > [!TIP]
    > È anche possibile salvare l'output in un file JSON usando il comando seguente:
    >
    >```Console
    >    UserMigration.exe 3 {email address} >> UserProfile.json
    >```

    > [!TIP]
    > È anche possibile recuperare un utente in base al nome visualizzato usando il comando seguente: `UserMigration.exe 4 <Display name>`.

    b. Aprire il file *UserProfile.json* in un editor JSON. Con Visual Studio Code è possibile formattare un documento JSON selezionando MAIUSC+ALT+F o **Formatta documento** nel menu di scelta rapida.

    ![Il file UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Passaggio 2.5: (Facoltativo) Pulizia dell'ambiente
Per eseguire la pulizia del tenant Azure AD e rimuovere utenti dalla directory di Azure AD, eseguire il comando `UserMigration.exe 5`.

> [!NOTE]
> * Per eseguire la pulizia del tenant, configurare le autorizzazioni di amministratore account utente per l'applicazione.
> * L'app di migrazione di esempio esegue la pulizia di tutti gli utenti elencati nel file JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Passaggio 2.6: Accedere con gli utenti di cui è stata eseguita la migrazione (con password)
Dopo aver eseguito il processo di pre-migrazione con le password degli utenti, gli account saranno pronti e gli utenti potranno accedere all'applicazione usando Azure AD B2C. Se l'accesso alle password degli utenti non è disponibile, passare alla sezione successiva.

## <a name="step-3-help-users-reset-their-password"></a>Passaggio 3: Aiutare gli utenti a reimpostare la password
Nel caso in cui si esegua la migrazione degli utenti con una password casuale, questi dovranno reimpostare la propria password. Per facilitare la reimpostazione della password, inviare un messaggio di benvenuto contenente un collegamento per reimpostare la password.

Per ottenere il collegamento ai criteri di reimpostazione della password, seguire questa procedura:

1. Selezionare **Impostazioni di Azure AD B2C** e quindi le proprietà del criterio **Reimposta password**.

2. Selezionare l'applicazione.

    >[!NOTE]
    >Il comando Esegui adesso richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md). 

3. Selezionare **Esegui adesso** e verificare i criteri.

4. Nella casella **Endpoint Esegui adesso** copiare l'URL e inviarlo agli utenti.

    ![Impostare i log di diagnostica](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Passaggio 4: (Facoltativo) Modificare i criteri per controllare e impostare lo stato di migrazione degli utenti

> [!NOTE]
> Per controllare e modificare lo stato di migrazione degli utenti è necessario usare criteri personalizzati. Per altre informazioni, vedere [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).
>

Quando gli utenti provano a eseguire l'accesso senza prima reimpostare la password, i criteri dovrebbero restituire un messaggio di errore descrittivo, Ad esempio:  
>*La password è scaduta. Per reimpostarla, selezionare il collegamento Reimposta password.* 

Questo passaggio facoltativo richiede l'uso di criteri personalizzati in Azure AD B2C, come descritto nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

In questa sezione vengono modificati i criteri per controllare lo stato di migrazione dell'utente all'accesso. Se l'utente non ha cambiato la password, verrà restituito un messaggio di errore HTTP 409 in cui si chiede all'utente di selezionare il collegamento **Password dimenticata?**. 

Per monitorare il cambio della password, usare una tabella di Azure. L'esecuzione del processo di pre-migrazione con il parametro `2` della riga di comando crea un'entità utente in una tabella di Azure. Il servizio esegue le operazioni seguenti:

* In fase di accesso, i criteri di Azure AD B2C richiamano il servizio RESTful di migrazione inviando un messaggio e-mail come attestazione di input. Il servizio cerca l'indirizzo e-mail nella tabella di Azure. Se l'indirizzo esiste, il servizio genera un messaggio di errore che richiede di *cambiare la password*.

* Dopo che l'utente ha cambiato la password, rimuovere l'entità dalla tabella di Azure.

>[!NOTE]
>L'uso di una tabella di Azure consente di semplificare l'esempio. È possibile archiviare lo stato di migrazione in qualsiasi database o come proprietà personalizzata nell'account Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: Aggiornare l'impostazione dell'applicazione
1. Per testare la demo dell'API RESTful, aprire la soluzione Visual Studio `AADB2C.UserMigration.sln` in Visual Studio. 

2. Nel progetto `AADB2C.UserMigration.API` aprire il file *App.config*. Sostituire l'impostazione dell'app con un valore personalizzato:

    ```XML
    <appSettings>
        <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
    </appSettings>
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Passaggio 4.2: Distribuire l'applicazione Web nel Servizio app di Azure
Pubblicare il servizio API nel Servizio app di Azure. Per altre informazioni, vedere [Distribuire l'app nel Servizio app di Azure](https://docs.microsoft.com/azure/app-service-web/web-sites-deploy).

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Passaggio 4.3: Aggiungere un profilo tecnico e la relativa convalida ai criteri 
1. Aprire il file dei criteri di estensione *TrustFrameworkExtensions.xml* nella directory di lavoro. 

2. Cercare il nodo `<ClaimsProviders>` e quindi, nel nodo, aggiungere il frammento XML seguente. Modificare il valore di `ServiceUrl` in modo che punti all'URL dell'endpoint. 

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
    
    <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
    
        <!-- This technical profile uses a validation technical profile to authenticate the user. -->
        <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    
        <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
            <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

Il profilo tecnico precedente definisce un'attestazione di input: `signInName` (invia come e-mail). All'accesso l'attestazione viene inviata all'endpoint RESTful.

Dopo aver definito il profilo tecnico per l'API RESTful, indicare ai criteri di Azure AD B2C di chiamarlo. Il frammento XML esegue l'override di `SelfAsserted-LocalAccountSignin-Email`, definito nei criteri di base. Il frammento XML aggiunge anche `ValidationTechnicalProfile` con ReferenceId che punta al profilo tecnico `LocalAccountUserMigration`. 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Passaggio 4.4: Caricare i criteri nel tenant
1. Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e quindi selezionare **Azure AD B2C**.

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Selezionare **Tutti i criteri**.

4. Selezionare **Carica criteri**.

5. Selezionare la casella di controllo **Sovrascrivi il criterio se esistente**.

6. Caricare il file *TrustFrameworkExtensions.xml* e assicurarsi che superi la convalida.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Passaggio 4.5: Testare i criteri personalizzati tramite Esegui adesso
1. Selezionare **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.

2. Aprire **B2C_1A_signup_signin**, i criteri personalizzati della relying party caricati in precedenza e quindi selezionare **Esegui adesso**.

3. Provare ad accedere con le credenziali di uno degli utenti di cui è stata eseguita la migrazione e quindi selezionare **Accedi**. L'API REST genera il messaggio di errore seguente:

    ![Impostare i log di diagnostica](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Passaggio 4.6: (Facoltativo) Risoluzione dei problemi dell'API REST
È possibile visualizzare e monitorare le informazioni di registrazione quasi in tempo reale.

1. Nel menu delle impostazioni dell'applicazione RESTful, in **Monitoraggio** selezionare **Log di diagnostica**. 

2. Impostare **Registrazione applicazioni (file system)** su **Attiva**. 

3. Impostare il **Livello** su **Dettagliato**.

4. Selezionare **Salva**

    ![Impostare i log di diagnostica](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. Nel menu **Impostazioni** selezionare **Flusso di registrazione**.

6. Controllare l'output dell'API RESTful.

Per altre informazioni, vedere [Log di streaming e console](https://docs.microsoft.com/azure/app-service-web/web-sites-streaming-logs-and-console).

> [!IMPORTANT]
> Usare i log di diagnostica solo in fase di sviluppo e test. L'output dell'API RESTful può contenere informazioni riservate che non devono essere esposte in produzione.
>

## <a name="optional-download-the-complete-policy-files"></a>(Facoltativo) Scaricare i file dei criteri completi
Dopo aver completato la procedura [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md), è consigliabile usare file di criteri personalizzati per definire scenari specifici. Per riferimento, sono disponibili [file di criteri di esempio](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). 
