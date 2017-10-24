---
title: 'Azure Active Directory B2C: approcci alla migrazione degli utenti'
description: Discutere concetti di base e avanzati sulla migrazione degli utenti usando l'API Graph e, facoltativamente, i criteri personalizzati di Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: b102edd997e69fb3568780a42dfe93fc9a90e332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: migrazione degli utenti
Per eseguire la migrazione del provider di identità ad Azure Active Directory B2C, potrebbe essere necessario eseguire anche la migrazione degli account utente. Questo articolo spiega come eseguire la migrazione degli account utente esistenti da qualsiasi provider di identità ad Azure Active Directory B2C. Questo articolo non fornisce informazioni prescrittive, ma descrive due di vari approcci diversi.  Lo sviluppatore è responsabile dell'idoneità.

## <a name="user-migration-flows"></a>Flussi di migrazione degli utenti
Azure Active Directory B2C consente di eseguire la migrazione degli utenti usando l'[API Graph](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Il processo di migrazione degli utenti rientra in due flussi:

* **Pre-migrazione** - Questo flusso è indicato quando si ha accesso alle credenziali utente (nome utente e password) in testo non crittografato oppure le credenziali sono crittografate, ma è possibile decrittografarle. Il processo include: lettura degli utenti dal vecchio provider di identità e creazione di nuovi account nella directory di Azure Active Directory B2C.

* **Pre-migrazione e reimpostazione della password** - Questo flusso è indicato se la password dell'utente non è accessibile. ad esempio:
    * Le password sono archiviate in formato HASH
    * Le password sono archiviate in un provider di identità non accessibile. Il vecchio provider di identità convalida le credenziali utente chiamando un servizio Web

In entrambi i flussi, eseguire prima il processo di __pre-migrazione__, leggere gli utenti dal vecchio provider di identità e creare nuovi account nella directory di Azure Active Directory B2C. Se la password non è disponibile, creare l'account con una password casuale generata dall'utente. Chiedere agli utenti di modificare la password. In alternativa, quando l'utente accede per la prima volta, B2C chiede di reimpostare la password.

## <a name="password-policy"></a>Criteri password
I criteri password di Azure AD B2C (per gli account locali) si basano su quelli di Azure AD. I criteri di iscrizione o accesso e reimpostazione della password di Azure Active Directory B2C usano un livello di complessità "avanzato" e non prevedono la scadenza delle password. Per altre informazioni dettagliate, vedere [Criteri password in Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) .

Se gli account di cui si vuole eseguire la migrazione hanno un livello di complessità della password inferiore al [livello avanzato applicato da Azure Active Directory B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), è possibile disabilitare il requisito per la password complessa. Per modificare i criteri password predefiniti, impostare la proprietà `passwordPolicies` su `DisableStrongPassword`. È ad esempio possibile modificare la richiesta di creazione utente come segue: 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-using-graph-api-to-migrate-users"></a>Passaggio 1: Uso dell'API Graph per eseguire la migrazione degli utenti
Creare l'account utente di Azure Active Directory B2C con l'API Graph (con la password personale o con una password casuale). Questa sezione descrive il processo per creare account utente nella directory di Azure Active Directory B2C usando l'API Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Passaggio 1.1 Registrare l'applicazione nel tenant
Per comunicare con l'API Graph, è necessario disporre di un account di servizio con privilegi amministrativi. In Azure AD è possibile registrare un'applicazione ed eseguire l'autenticazione in Azure AD. Le credenziali dell'applicazione sono: **ID applicazione** e **segreto dell'applicazione**. L'applicazione agisce autonomamente, non come utente, per chiamare l'API Graph.

Per prima cosa registrare l'applicazione di migrazione in Azure AD. Creare la chiave dell'applicazione (segreto) e impostare l'applicazione con i privilegi appropriati.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Scegliere il tenant di Azure AD **B2C** selezionando l'account nell'angolo superiore destro della pagina.
3. Nel pannello sinistro fare clic su **Azure Active Directory** (non Azure AD B2C). Potrebbe essere necessario selezionare **Altri servizi** per individuarlo.
4. Selezionare **Registrazioni per l'app**.
5. Fare clic su **Registrazione nuova applicazione**.

    ![Registrazione nuova applicazione](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
6. Seguire le istruzioni e creare una nuova applicazione
    * Per **Nome** usare **B2CUserMigratioin** o qualsiasi altro nome.
    * Per **Tipo di applicazione** usare **App Web/API**.
    * Per **URL di accesso** usare https://localhost (non pertinente per questa applicazione).
    * Fare clic su **Crea**.
7. Dopo aver creato l'applicazione, nell'elenco delle applicazioni selezionare quella appena creata, **B2CUserMigratioin**.
Selezionare **Proprietà**, copiare l'**ID applicazione** e salvarlo per un uso successivo.

### <a name="step-12-create-application-secret"></a>Passaggio 1.2 Creare il segreto dell'applicazione
8. Proseguire in App registrata del portale di Azure. Fare clic su **Chiavi** e aggiungere una nuova chiave (nota anche come segreto client). Copiare anche la chiave per un uso successivo.

    ![ID applicazione e Chiavi](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Passaggio 1.3 Concedere l'autorizzazione amministrativa all'applicazione
9. Proseguire in **App registrata** del portale di Azure
10. Fare clic su **Autorizzazioni necessarie**.
11. Fare clic su **Windows Azure Active Directory**.
12. Nel menu **Abilita accesso**, in **Autorizzazioni dell'applicazione**, selezionare l'autorizzazione **Leggi e scrivi i dati della directory** e fare clic su **Salva**.
13. Infine, in **Autorizzazioni necessarie** fare clic sul pulsante **Concedi autorizzazioni**.

    ![Autorizzazioni dell'applicazione](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Ora è disponibile un'applicazione con le autorizzazioni per creare, leggere e aggiornare gli utenti dal tenant B2C.

### <a name="step-14-optional-environment-cleanup"></a>Passaggio 1.4 [facoltativo] Pulizia dell'ambiente
L'autorizzazione Leggi e scrivi i dati della directory attualmente NON prevede la possibilità di eliminare utenti. Per consentire all'applicazione di eliminare utenti (per pulire l'ambiente), è necessario eseguire un passaggio aggiuntivo. Il passaggio prevede l'esecuzione di PowerShell per impostare le autorizzazioni __Amministratore account utente__. In caso contrario, è possibile passare alla sezione successiva.


> [!IMPORTANT]
> È necessario usare un account di amministratore tenant B2C che sia **locale** in relazione al tenant B2C. Questi account hanno un aspetto simile a questo: admin@contosob2c.onmicrosoft.com.

>[!NOTE]
> Lo script di PowerShell seguente richiede [Azure Active Directory PowerShell **versione 2**](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

Nello script di PowerShell seguente:
* Connettersi al servizio online. A tale scopo, eseguire il cmdlet `Connect-AzureAD` al prompt dei comandi di Windows PowerShell e fornire le credenziali. 
* Usare l'**ID applicazione** per assegnare all'applicazione il ruolo di amministratore di account utente. Questi ruoli hanno identificatori noti, pertanto è sufficiente eseguire l'input dell'**ID applicazione** nello script.

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

Modificare il valore `$AppId` con l'**ID applicazione** di Azure AD

## <a name="step-2-pre-migration-application-sample"></a>Passaggio 2: Esempio di applicazione di pre-migrazione
Scaricare il codice di esempio ed eseguirlo. È possibile [scaricare il codice di esempio come file con estensione zip](http://www.github.com).

### <a name="step-21-edit-the-migration-data-file"></a>Passaggio 2.1 Modificare il file di dati di migrazione
L'app di esempio usa il file JSON che contiene dati utente fittizi. Dopo aver eseguito correttamente l'esempio, modificare il codice per usare i dati del database personale. In alternativa, esportare il profilo utente in un file JSON e impostare l'app per l'uso di questo file.
Per modificare il file JSON, aprire la soluzione Visual Studio `AADB2C.UserMigration.sln`. Nel progetto `AADB2C.UserMigration` aprire il file `UsersData.json`.


![File dei dati utente](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Come si può notare, il file contiene l'elenco delle entità utente. Ogni entità utente dispone di:
* email
* displayName
* firstName
* lastName
* password (il campo può essere vuoto)

> [!NOTE]
> In fase di compilazione, Visual Studio copia il file nella directory `bin`.

### <a name="step-22-configure-the-application-settings"></a>Passaggio 2.2 Configurare le impostazioni dell'applicazione
Nel progetto `AADB2C.UserMigration` aprire il file App.config. Sostituire le impostazioni dell'app seguenti con valori personalizzati:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users data e.g. UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure Table string}" />
    
</appSettings>
```

> [!NOTE]
> * L'uso della stringa di connessione Azure Table (Tabella di Azure) è descritto nelle sezioni successive
> * Il nome del tenant B2C è il dominio immesso durante la creazione del tenant e viene visualizzato nel portale di Azure. In genere termina con il suffisso `.onmicrosoft.com`, ad esempio, `contosob2c.onmicrosoft.com`.
>

### <a name="step-23-run-the-pre-migration-process"></a>Passaggio 2.3 Eseguire il processo di pre-migrazione
Fare clic con il pulsante destro del mouse sulla soluzione `AADB2C.UserMigration` e ricompilare l'esempio. Se l'operazione riesce, sarà disponibile un file eseguibile `UserMigration.exe` in `AADB2C.UserMigration\bin\Debug`. Per eseguire il processo di migrazione, usare un parametro della riga di comando tra i seguenti:

* Per **eseguire la migrazione degli utenti con la password personale**, usare il comando `UserMigration.exe 1`.

* Per **eseguire la migrazione degli utenti con una password casuale**, usare il comando `UserMigration.exe 2`. Questa operazione crea anche entità Azure Table. Configurare quindi i criteri per chiamare il servizio API REST. Il servizio usa Azure Table per monitorare e gestire il processo di migrazione.

![Demo del processo di migrazione](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Passaggio 2.4 Controllare il processo di pre-migrazione
Per controllare il risultato, dal portale di Azure aprire **Azure AD B2C** e fare clic su **Utenti e gruppi**. Nella casella di ricerca digitare un nome visualizzato degli utenti e osservare il profilo utente. In alternativa è possibile usare questa applicazione di esempio per recuperare l'utente dall'**indirizzo e-mail di accesso**. Per cercare un utente in base all'indirizzo e-mail di accesso, eseguire il comando seguente

```Console
UserMigration.exe 3 {email address}
```

È anche possibile salvare l'output nel file JSON, come indicato di seguito:
```Console
UserMigration.exe 3 {email address} >> UserProfile.json
```


Aprire il file UserProfile.json con l'editor JSON desiderato. Con Visual Studio Code è possibile formattare il documento JSON usando `Shift+Alt+F` oppure Formatta documento dal menu di scelta rapida.

![Profilo utente - JSON](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

È anche possibile recuperare l'utente in base al **nome visualizzato** con il comando `UserMigration.exe 4 <Display name>`

### <a name="step-25-optional-environment-cleanup"></a>Passaggio 2.5 [facoltativo] Pulizia dell'ambiente
Per eseguire la pulizia del tenant di Azure AD e rimuovere gli utenti dalla directory di Azure AD, eseguire il comando `UserMigration.exe 5`

> [!NOTE]
> * Per eseguire la pulizia del tenant, configurare le autorizzazioni __Amministratore account utente__ per l'applicazione
> * L'app di migrazione di esempio pulisce tutti gli utenti elencati nel file JSON

### <a name="step-26-sing-in-with-migrated-users-with-password"></a>Passaggio 2.6 Eseguire l'accesso con gli utenti di migrazione (con password)
Dopo aver eseguito il processo di pre-migrazione con la password degli utenti, gli account saranno pronti e gli utenti potranno accedere all'applicazione usando Azure Active Directory B2C. Se l'accesso alla password degli utenti non è disponibile, passare alla sezione successiva.

## <a name="step-3-password-reset"></a>Passaggio 3: Reimpostazione della password
Nel caso in cui si esegua la migrazione di utenti con password casuali, gli utenti devono reimpostare la password. Per reimpostare la password:
* Inviare un messaggio e-mail di benvenuto con un link per reimpostare la password
* [Facoltativo] Modificare i criteri per gestire la situazione in cui l'utente non reimposti la password e tenti di eseguire l'accesso. In fase di accesso, i criteri controllano lo stato di migrazione. Se l'utente non ha modificato la password, viene generato un messaggio di errore descrittivo in cui si chiede all'utente di fare clic su "Password dimenticata?"

    > [!NOTE]
    > Per controllare e modificare lo stato di migrazione dell'utente, è necessario usare criteri personalizzati. Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).
    >

### <a name="step-31-send-a-welcome-email-with-link-to-reset-password"></a>Passaggio 3.1 Inviare un messaggio e-mail di benvenuto con un link per reimpostare la password
Per ottenere il link ai criteri di reimpostazione della password:

1.  Aprire **Impostazioni di Azure AD B2C** e le proprietà dei criteri **Reimposta password**.

2. Selezionare l'applicazione.
    >[!NOTE]
    >
    >Il comando **Esegui adesso** richiede che nel tenant sia preregistrata almeno un'applicazione. Per informazioni su come registrare le applicazioni, vedere l'articolo di [introduzione](active-directory-b2c-get-started.md) ad Azure AD B2C o l'articolo relativo alla [registrazione delle applicazioni](active-directory-b2c-app-registration.md).  

2.  Selezionare **Esegui adesso** e controllare i criteri
3.  **Copiare** l'URL e inviarlo agli utenti

    ![Impostare i log di diagnostica](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-user-migration-status"></a>Passaggio 4: [facoltativo] Modificare i criteri per controllare e impostare lo stato di migrazione degli utenti

Quando gli utenti provano a eseguire l'accesso senza prima reimpostare la password, i criteri dovrebbero restituire un messaggio di errore descrittivo. Ad esempio: la password è scaduta, per reimpostarla fare clic sul collegamento Reimposta password.  Questo passaggio facoltativo richiede l'uso di Azure Active Directory B2C con criteri personalizzati, come descritto nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

In questa sezione modificare i criteri per controllare lo stato di migrazione all'accesso. Se l'utente non ha modificato la password, viene generato un messaggio di errore HTTP 409 in cui si chiede all'utente di fare clic sul link "Password dimenticata?" link. Per monitorare la modifica della password, usare Azure Table. L'esecuzione del processo di pre-migrazione con il parametro della riga di comando `2` crea entità utente in Azure Table. Il servizio:

* In fase di accesso, i criteri di Azure Active Directory B2C richiamano il servizio RESTful di migrazione, inviando un messaggio e-mail come attestazione di input. Il servizio ricerca l'indirizzo e-mail in Azure Table. Se esiste, viene generato un messaggio di errore che richiede di modificare la password.

* In fase di reimpostazione della password, dopo che l'utente l'avrà modificata, rimuovere l'entità Azure Table.


> [!NOTE]
>L'uso di Azure Table consente di semplificare l'esempio. È possibile archiviare lo stato di migrazione in qualsiasi database o come proprietà personalizzata nell'account Azure Active Directory B2C.

### <a name="41-application-settings"></a>4.1 Impostazioni dell'applicazione
Per testare la demo API REST. Aprire la soluzione Visual Studio `AADB2C.UserMigration.sln` in Visual Studio. Nel progetto `AADB2C.UserMigration.API` aprire il file App.config. Sostituire le tre impostazioni dell'app con valori personalizzati:

```XML
<appSettings>
    <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
</appSettings>
```

### <a name="step-42-deploy-your-web-application-to-azure-app-services"></a>Passaggio 4.2 Distribuire l'applicazione Web in Servizi app di Azure
Pubblicare il servizio API in Servizi app di Azure. Per altre informazioni, vedere [Distribuire l'app nel servizio app di Azure](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy)

### <a name="step-43-add-technical-profile-and-technical-profile-validation-to-your-policy"></a>Passaggio 4.3 Aggiunge un profilo tecnico e la relativa convalida ai criteri 
1.  Aprire il file dei criteri di estensione (TrustFrameworkExtensions.xml) dalla directory di lavoro. 
2. Trovare la sezione `<ClaimsProviders>`
3. Aggiungere il frammento XML seguente nell'elemento `ClaimsProviders`
4. Modificare il valore di `ServiceUrl` in modo che punti all'URL dell'endpoint 

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

Il profilo tecnico definisce un'attestazione di input: `signInName` (invio tramite e-mail). All'accesso l'attestazione viene inviata all'endpoint RESTful.

Dopo aver definito il profilo tecnico per l'API RESTful, indicare ai criteri di Azure Active Directory B2C di chiamarlo. Il frammento XML esegue l'override di `SelfAsserted-LocalAccountSignin-Email`, definito nei criteri di base. Il frammento XML aggiunge anche `ValidationTechnicalProfile` con ReferenceId che punta al profilo tecnico `LocalAccountUserMigration`. 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Passaggio 4.4 Caricare i criteri nel tenant
1.  Nel [portale di Azure](https://portal.azure.com) passare al [contesto del tenant Azure Active Directory B2C](active-directory-b2c-navigate-to-b2c-context.md), e fare clic su **Azure AD B2C**.
2.  Fare clic su **Framework dell'esperienza di gestione delle identità**.
3.  Fare clic su **Tutti i criteri**.
4.  Selezionare **Carica il criterio**.
5.  Selezionare la casella **Sovrascrivi il criterio se esistente**.
6.  **Caricare** TrustFrameworkExtensions.xml e assicurarsi che non presenti errori di convalida
7.  Ripetere l'ultimo passaggio e caricare il file SignUpOrSignIn.xml

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Passaggio 4.5 Testare i criteri personalizzati tramite Esegui adesso
1.  Aprire **Impostazioni di Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità**.
2.  Aprire **B2C_1A_signup_signin**, i criteri personalizzati dalla relying party caricati in precedenza. Selezionare **Esegui adesso**.
3. Provare ad accedere con uno degli utenti di migrazione e fare clic su **Accedi**. Dovrebbe essere visualizzato il messaggio di errore seguente generato dall'API REST.

    ![Impostare i log di diagnostica](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshooting-your-rest-api"></a>Passaggio 4.6 [facoltativo] Risoluzione dei problemi dell'API REST
È possibile monitorare e visualizzare le informazioni di registrazione in tempo quasi reale.

1. Dal menu delle impostazioni dell'applicazione RESTful scorrere verso il basso fino alla sezione **Monitoraggio** e fare clic su **Log di diagnostica**. 
2. Abilitare Registrazione applicazioni (file system) 
3. Impostare il **livello** su **Dettagliato**
4. Fare clic su **Save**

    ![Impostare i log di diagnostica](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. Nel menu delle impostazioni fare clic su **Flusso di registrazione**
6. Controllare l'output dell'API RESTful.

Per altre informazioni, vedere [Log di streaming e console](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console)

> [!IMPORTANT]
> I __log di diagnostica__ devono essere usati solo in fase di sviluppo e test. L'output dell'API RESTful può contenere informazioni riservate che non devono essere esposte in produzione.
>

## <a name="download-the-complete-policy-files"></a>Scaricare i file dei criteri completi
Facoltativo: anziché usare i file di esempio, per creare lo scenario è consigliabile usare file di criteri personalizzati dopo aver completato la procedura Introduzione ai criteri personalizzati.  [File dei criteri di esempio di riferimento](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)