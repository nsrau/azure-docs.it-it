<properties
   pageTitle="Autenticazione di un'entità servizio con Azure Resource Manager | Microsoft Azure"
   description="Descrive come concedere l'accesso a un'entità servizio e autenticarla tramite il controllo degli accessi in base al ruolo. Mostra come eseguire queste attività con PowerShell e l'interfaccia della riga di comando di Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="tomfitz"/>

# Autenticazione di un'entità servizio con Gestione risorse di Azure

Questo argomento mostra come consentire un'entità servizio (ad esempio un processo, un'applicazione o un servizio automatizzato) per accedere ad altre risorse nella sottoscrizione. Con Gestione risorse di Azure è possibile usare il controllo degli accessi in base al ruolo per l'esecuzione di azioni consentite in un'entità servizio e l'autenticazione di tale entità.

Questo argomento illustra come usare Azure PowerShell o l'interfaccia della riga di comando di Azure per Mac, Linux e Microsoft Azure per creare un'applicazione e un'entità servizio, assegnare un ruolo all'entità servizio e autenticarsi come entità servizio. Se Azure PowerShell non è installato, vedere [Come installare e configurare Azure PowerShell](./powershell-install-configure.md). Se non è installato CLI Azure, vedere [Installare e configurare CLI Azure](xplat-cli-install.md). Per informazioni sull'utilizzo del portale per eseguire questa procedura, vedere [Creare un'applicazione e un'entità servizio di Active Directory tramite il portale](resource-group-create-service-principal-portal.md)

## Concetti
1. Azure Active Directory (AAD): servizio di gestione delle identità e degli accessi per il cloud. Per altre informazioni, vedere [Informazioni su Azure Active Directory](active-directory/active-directory-whatis.md)
2. Entità servizio: istanza di un'applicazione in una directory che richiede l’accesso ad altre risorse.
3. Applicazione Active Directory: record di directory che identifica un'applicazione in AAD.

Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e Oggetti entità servizio](active-directory/active-directory-application-objects.md). Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](active-directory/active-directory-authentication-scenarios.md).

Questo argomento illustra 4 percorsi per la creazione di un'applicazione di Active Directory e l'autenticazione dell'applicazione. I percorsi variano a seconda che si voglia usare una password o un certificato per l'autenticazione e che si preferisca usare PowerShell o l'interfaccia della riga di comando di Azure. Ecco i percorsi:

- [Autenticazione con password - PowerShell](#authenticate-with-password---powershell)
- [Autenticazione con certificato - PowerShell](#authenticate-with-certificate---powershell)
- [Autenticazione con password - Interfaccia della riga di comando di Azure](#authenticate-with-password---azure-cli)
- [Autenticazione con certificato - Interfaccia della riga di comando di Azure](#authenticate-with-certificate---azure-cli)

## Autenticazione con password - PowerShell

In questa sezione, si eseguiranno i passaggi per creare un’entità servizio per un'applicazione Azure Active Directory, assegnare un ruolo per l'entità servizio e autenticare come entità servizio specificando l'identificatore dell'applicazione e la password.

1. Accedere al proprio account.

        PS C:\> Login-AzureRmAccount

1. Creare una nuova applicazione di Active Directory eseguendo il comando **New-AzureRmADApplication**. Specificare un nome visualizzato per l'applicazione, l'URI a una pagina che descrive l'applicazione (il collegamento non è verificato), gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Esaminare il nuovo oggetto dell'applicazione. La proprietà **ApplicationId** è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione del token di accesso.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}

2. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     È stata creata un'entità servizio nella directory, ma al servizio non sono assegnate autorizzazioni o ambiti. È necessario concedere esplicitamente le autorizzazioni dell'entità servizio per eseguire operazioni in un ambito.

3. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

È stata creata un'applicazione di Active Directory e un'entità servizio per tale applicazione. È stata assegnata l'entità servizio a un ruolo. A questo punto, è necessario accedere come entità servizio per eseguire operazioni come tale. Questo argomento illustra tre opzioni:

- [Fornire manualmente le credenziali tramite PowerShell](#manually-provide-credentials-through-powershell)
- [Fornire le credenziali tramite uno script di PowerShell automatizzato](#provide-credentials-through-automated-powershell-script)
- [Fornire le credenziali tramite codice in un'applicazione](#provide-credentials-through-code-in-an-application)

<a id="manually-provide-credentials-through-powershell" />
### Fornire manualmente le credenziali tramite PowerShell

È possibile fornire manualmente le credenziali per l'entità servizio durante l'esecuzione di comandi o di uno script su richiesta.

1. Creare un nuovo oggetto **PSCredential** che contenga le credenziali eseguendo il comando **Get-Credential**.

        PS C:\> $creds = Get-Credential

2. Verrà richiesto di immettere le credenziali. Per il nome utente, usare **ApplicationId** o **IdentifierUris** specificato durante la creazione dell'applicazione. Per la password, usare quella specificata durante la creazione dell'account.

     ![immettere le credenziali][1]

3. Recuperare la sottoscrizione in cui è stata creata l'assegnazione di ruolo. La sottoscrizione verrà usata per ottenere il **TenantId** del tenant in cui si trova l'assegnazione di ruolo dell'entità servizio.

        PS C:\> $subscription = Get-AzureRmSubscription

     Se l'assegnazione di ruolo è stata creata in una sottoscrizione diversa da quella attualmente selezionata, è possibile specificare il parametro **SubscriptoinId** o **SubscriptionName** per recuperare una sottoscrizione diversa.

4. Accedere come entità servizio con il cmdlet **Login-AzureRmAccount**, ma fornire l'oggetto credenziali e specificare che questo account è un'entità servizio.

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

<a id="provide-credentials-through-automated-powershell-script" />
### Fornire le credenziali tramite uno script di PowerShell automatizzato

Questa sezione illustra come eseguire l'accesso come entità servizio senza dover immettere manualmente le credenziali. Non è necessario specificare manualmente la password perché viene recuperata da un insieme di credenziali delle chiavi.

> [AZURE.NOTE] Includere una password direttamente nello script di PowerShell non è un'operazione sicura, perché la password viene esposta come testo. Usare invece un servizio come l'insieme di credenziali delle chiavi per archiviare la password e recuperarla quando si esegue lo script.

L'esecuzione di questi passaggi presuppone che siano stati configurati un insieme di credenziali delle chiavi e un segreto che archivia la password. Per distribuire un insieme di credenziali delle chiavi e un segreto tramite un modello, vedere il [formato del modello dell'insieme di credenziali delle chiavi](). Per informazioni sull'insieme di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](./key-vault/key-vault-get-started.md).

1. Recuperare la password dall'insieme di credenziali delle chiavi. Nell'esempio seguente è archiviata come segreto con il nome **appPassword**.

        PS C:\> $secret = Get-AzureKeyVaultSecret -VaultName examplevault -Name appPassword
        
2. Ottenere l'applicazione di Active Directory Al momento dell'accesso sarà necessario l'ID dell'applicazione.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"

3. Creare un nuovo oggetto **PSCredential** fornendo l'ID dell'applicazione e la password come credenziali.

        PS C:\> $creds = New-Object System.Management.Automation.PSCredential ($azureAdApplication.ApplicationId, $secret.SecretValue)
    
4. Recuperare la sottoscrizione in cui è stata creata l'assegnazione di ruolo. La sottoscrizione verrà usata successivamente per ottenere il **TenantId** del tenant in cui si trova l'assegnazione di ruolo dell'entità servizio.

        PS C:\> $subscription = Get-AzureRmSubscription

     Se l'assegnazione di ruolo è stata creata in una sottoscrizione diversa da quella attualmente selezionata, è possibile specificare il parametro **SubscriptoinId** o **SubscriptionName** per recuperare una sottoscrizione diversa.

5. Accedere come entità servizio con il cmdlet **Login-AzureRmAccount**, ma fornire l'oggetto credenziali e specificare che questo account è un'entità servizio.
    
        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

<a id="provide-credentials-through-code-in-an-application" />
### Fornire le credenziali tramite codice in un'applicazione

Per eseguire l'autenticazione da un'applicazione .NET, includere il codice seguente. Saranno necessari l'ID dell'applicazione per l'applicazione di Active Directory, la password e l'ID tenant per la sottoscrizione. Dopo il recupero del token di accesso, è possibile utilizzare le risorse nella sottoscrizione.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }


## Autenticazione con certificato - PowerShell

In questa sezione, si eseguiranno i passaggi per creare un’entità servizio per un'applicazione Azure Active Directory, assegnare un ruolo per l'entità servizio e autenticare come entità servizio specificando fornendo un certificato. In questo argomento si presuppone che si disponga di un certificato.

Mostra due modi per lavorare con certificati - credenziali della chiave e valori della chiave. È possibile utilizzare entrambi gli approcci.

In primo luogo, è necessario configurare alcuni valori di PowerShell che verranno utilizzati in un secondo momento durante la creazione dell'applicazione.

1. Accedere al proprio account.

        PS C:\> Login-AzureRmAccount

1. Per entrambi gli approcci creare un oggetto X509Certificate2 dal certificato e recuperare il valore della chiave. Utilizzare il percorso del certificato e la password per il certificato.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Se si usano credenziali della chiave, creare l'oggetto credenziali della chiave e impostare il valore al `$keyValue` dal passaggio precedente. L'esempio seguente include la chiamata a `Add-Type` per aggiungere un tipo da un assembly. Il percorso illustrato nell'esempio sarà simile al percorso locale, ma potrebbe essere leggermente diverso.

        Add-Type -Path 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ResourceManager\AzureResourceManager\AzureRM.Resources\Microsoft.Azure.Commands.Resources.dll'
        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. Creare un'applicazione nella directory. Il primo comando illustra come utilizzare i valori della chiave.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    In alternativa, utilizzare il secondo esempio per assegnare le credenziali di chiave.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "example" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyCredentials $keyCredential

    Esaminare il nuovo oggetto dell'applicazione. La proprietà **ApplicationId** è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token di accesso.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}       

4. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    È stata creata un'entità servizio nella directory, ma al servizio non sono assegnate autorizzazioni o ambiti. È necessario concedere esplicitamente le autorizzazioni dell'entità servizio per eseguire operazioni in un ambito.

5. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

È stata creata un'applicazione di Active Directory e un'entità servizio per tale applicazione. È stata assegnata l'entità servizio a un ruolo. A questo punto, è necessario accedere come entità servizio per eseguire operazioni come tale. Questo argomento illustra due opzioni:

- [Fornire il certificato tramite uno script di PowerShell automatizzato](#provide-certificate-through-automated-powershell-script)
- [Fornire il certificato tramite codice in un'applicazione](#provide-certificate-through-code-in-an-application)

<a id="provide-certificate-through-automated-powershell-script" />
### Fornire il certificato tramite uno script di PowerShell automatizzato

1. Ottenere l'applicazione di Active Directory Al momento dell'accesso sarà necessario l'ID dell'applicazione

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"
        
2. Recuperare la sottoscrizione in cui è stata creata l'assegnazione di ruolo. La sottoscrizione verrà usata successivamente per ottenere il **TenantId** del tenant in cui si trova l'assegnazione di ruolo dell'entità servizio.

        PS C:\> $subscription = Get-AzureRmSubscription

     Se l'assegnazione di ruolo è stata creata in una sottoscrizione diversa da quella attualmente selezionata, è possibile specificare il parametro **SubscriptoinId** o **SubscriptionName** per recuperare una sottoscrizione diversa.

3. Ottenere il certificato da usare per l'autenticazione.

        PS C:\> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")

4. Per autenticarsi in PowerShell, fornire l'identificazione personale del certificato, l'ID dell'applicazione e l'ID tenant.

        PS C:\> Login-AzureRmAccount -CertificateThumbprint $cert.Thumbprint -ApplicationId $azureAdApplication.ApplicationId -ServicePrincipal -TenantId $subscription.TenantId

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

<a id="provide-certificate-through-code-in-an-application" />
### Fornire il certificato tramite codice in un'applicazione

Per eseguire l'autenticazione da un'applicazione .NET, includere il codice seguente. Dopo il recupero del client, è possibile accedere alle risorse nella sottoscrizione.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred);
    // If using the new resource manager package like "Microsoft.Azure.ResourceManager" version="1.0.0-preview" use below
    var creds = new TokenCredentials(token.AccessToken); 
    // Else if using older package versions like Microsoft.Azure.Management.Resources" version="3.4.0-preview" use below
    // var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds); 
        

## Autenticazione con password - Interfaccia della riga di comando di Azure

Iniziare creando un'entità servizio. Per farlo, è necessario creare un'applicazione nella directory. Questa sezione fornisce spiegazioni su come creare una nuova applicazione nella directory.

1. Passare al modulo Gestione risorse di Azure e accedere al proprio account.

        azure config mode arm
        azure login

2. Creare una nuova applicazione di Active Directory eseguendo il comando **azure app create**. Specificare un nome visualizzato per l'applicazione, l'URI a una pagina che descrive l'applicazione (il collegamento non è verificato), gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Viene restituita l'applicazione di Active Directory. La proprietà AppId è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token di accesso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

3. Creare un'entità servizio per l'applicazione. Fornire l'id dell'applicazione che è stato restituito nel passaggio precedente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

    È stata creata un'entità servizio nella directory, ma al servizio non sono assegnate autorizzazioni o ambiti. È necessario concedere esplicitamente le autorizzazioni dell'entità servizio per eseguire operazioni in un ambito.

4. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

È stata creata un'applicazione di Active Directory e un'entità servizio per tale applicazione. È stata assegnata l'entità servizio a un ruolo. A questo punto, è necessario accedere come entità servizio per eseguire operazioni come tale. Questo argomento illustra tre opzioni:

- [Fornire manualmente le credenziali tramite l'interfaccia della riga di comando di Azure](#manually-provide-credentials-through-azure-cli)
- [Fornire le credenziali tramite uno script dell'interfaccia della riga di comando di Azure automatizzato](#provide-credentials-through-automated-azure-cli-script)
- [Fornire le credenziali tramite codice in un'applicazione](#provide-credentials-through-code-in-an-application-cli)

<a id="manually-provide-credentials-through-Azure-cli" />
### Fornire manualmente le credenziali tramite l'interfaccia della riga di comando di Azure

Se si vuole eseguire manualmente l'accesso come entità servizio, è possibile usare il comando **azure login**. È necessario fornire l'ID tenant, l'ID applicazione e la password. Includere la password direttamente in uno script non è un'operazione sicura, perché la password viene archiviata nel file. Quando si esegue uno script automatizzato, è preferibile vedere la sezione successiva.

1. Determinare il **TenantId** per la sottoscrizione che contiene l'entità servizio. Se si recupera l'ID tenant per la sottoscrizione attualmente autenticata, non è necessario specificare l'ID sottoscrizione come parametro. L'opzione **-r** recupera il valore senza le virgolette.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. Come nome utente usare l'**AppId** usato durante la creazione dell'entità servizio. Se è necessario recuperare l'ID dell'applicazione, usare il comando seguente. Specificare il nome dell'applicazione di Active Directory nel parametro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Accedere come entità servizio:

        azure login -u "$appId" --service-principal --tenant "$tenantId"

Quando richiesto, fornire la password specificata durante la creazione dell'account.

    info:    Executing command login
    Password: ********

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

<a id="provide-credentials-through-automated-azure-cli-script" />
### Fornire le credenziali tramite uno script dell'interfaccia della riga di comando di Azure automatizzato

Questa sezione illustra come eseguire l'accesso come entità servizio senza dover immettere manualmente le credenziali.

> [AZURE.NOTE] Includere una password nello script dell'interfaccia della riga di comando di Azure non è un'operazione sicura, perché la password viene esposta come testo. Usare invece un servizio come l'insieme di credenziali delle chiavi per archiviare la password e recuperarla quando si esegue lo script.

L'esecuzione di questi passaggi presuppone che siano stati configurati un insieme di credenziali delle chiavi e un segreto che archivia la password. Per distribuire un insieme di credenziali delle chiavi e un segreto tramite un modello, vedere il [formato del modello dell'insieme di credenziali delle chiavi](). Per informazioni sull'insieme di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](./key-vault/key-vault-get-started.md).

1. Recuperare la password dall'insieme di credenziali delle chiavi. Nell'esempio seguente è archiviata come segreto con il nome **appPassword**. Includere l'opzione **-r** per rimuovere le virgolette doppie iniziali e finali restituite dall'output JSON.

        secret=$(azure keyvault secret show --vault-name examplevault --secret-name appPassword --json | jq -r '.value')
    
2. Determinare il **TenantId** per la sottoscrizione che contiene l'entità servizio. Se si recupera l'ID tenant per la sottoscrizione attualmente autenticata, non è necessario specificare l'ID sottoscrizione come parametro.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. Come nome utente usare l'**AppId** usato durante la creazione dell'entità servizio. Se è necessario recuperare l'ID dell'applicazione, usare il comando seguente. Specificare il nome dell'applicazione di Active Directory nel parametro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Accedere come entità servizio fornendo l'ID applicazione, la password dall'insieme di credenziali delle chiavi e l'ID tenant.

        azure login -u "$appId" -p "$secret" --service-principal --tenant "$tenantId"
    
A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

<a id="provide-credentials-through-code-in-an-application-cli" />
### Fornire le credenziali tramite codice in un'applicazione

Per eseguire l'autenticazione da un'applicazione .NET, includere il codice seguente. Saranno necessari l'ID dell'applicazione per l'applicazione di Active Directory, la password e l'ID tenant per la sottoscrizione. Dopo il recupero del token di accesso, è possibile utilizzare le risorse nella sottoscrizione.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }

## Autenticazione con certificato - Interfaccia della riga di comando di Azure

In questa sezione si eseguiranno i passaggi per creare un’entità servizio per un'applicazione Azure Active Directory che utilizza un certificato per l'autenticazione. In questo argomento si presuppone che si disponga di un certificato e di [OpenSSL](http://www.openssl.org/) installato.

1. Creare un file **.pem** con:

        openssl pkcs12 -in C:\certificates\examplecert.pfx -out C:\certificates\examplecert.pem -nodes

2. Aprire il file **.pem** e copiare i dati del certificato. Cercare la lunga sequenza di caratteri tra **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----**.

3. Creare una nuova applicazione di Active Directory eseguendo il comando **azure ad app create** e fornire i dati del certificato copiati nel passaggio precedente come valore della chiave.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Viene restituita l'applicazione di Active Directory. La proprietà AppId è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token di accesso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

4. Creare un'entità servizio per l'applicazione. Fornire l'id dell'applicazione che è stato restituito nel passaggio precedente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
5. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

È stata creata un'applicazione di Active Directory e un'entità servizio per tale applicazione. È stata assegnata l'entità servizio a un ruolo. A questo punto, è necessario accedere come entità servizio per eseguire operazioni come tale. Questo argomento illustra due opzioni:

- [Fornire il certificato tramite uno script dell'interfaccia della riga di comando di Azure automatizzato](#provide-certificate-through-automated-azure-cli-script)
- [Fornire il certificato tramite codice in un'applicazione](#provide-certificate-through-code-in-an-application-cli)

<a id="provide-certificate-through-automated-azure-cli-script" />
### Fornire il certificato tramite uno script dell'interfaccia della riga di comando di Azure automatizzato

1. È necessario recuperare l'identificazione personale del certificato e rimuovere i caratteri non necessari.

        cert=$(openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g')
    
     Viene restituito un valore di identificazione personale simile a:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Determinare il **TenantId** per la sottoscrizione che contiene l'entità servizio. Se si recupera l'ID tenant per la sottoscrizione attualmente autenticata, non è necessario specificare l'ID sottoscrizione come parametro. L'opzione **-r** recupera il valore senza le virgolette.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

3. Come nome utente usare l'**AppId** usato durante la creazione dell'entità servizio. Se è necessario recuperare l'ID dell'applicazione, usare il comando seguente. Specificare il nome dell'applicazione di Active Directory nel parametro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

4. Per autenticarsi con l'interfaccia della riga di comando di Azure, fornire l'identificazione personale del certificato, il file del certificato, l'ID dell'applicazione e l'ID tenant.

        azure login --service-principal --tenant "$tenantId" -u "$appId" --certificate-file C:\certificates\examplecert.pem --thumbprint "$cert"

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

<a id="provide-certificate-through-code-in-an-application-cli" />
### Fornire il certificato tramite codice in un'applicazione

Per eseguire l'autenticazione da un'applicazione .NET, includere il codice seguente. Dopo il recupero del client, è possibile accedere alle risorse nella sottoscrizione.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    // If using the new resource manager package like "Microsoft.Azure.ResourceManager" version="1.0.0-preview" use below
    var creds = new TokenCredentials(token.AccessToken); 
    // Else if using older package versions like Microsoft.Azure.Management.Resources" version="3.4.0-preview" use below
    // var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds); 
       
Per ottenere altre informazioni sull'uso dei certificati e dell'interfaccia della riga di comando di Azure, vedere il blog relativo all'[autenticazione basata su certificati con le entità servizio di Azure dalla riga di comando di Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)

## Passaggi successivi
  
- Per altre informazioni sull'uso del portale con le entità servizio, vedere [Creare una nuova entità servizio di Azure tramite il portale di Azure](./resource-group-create-service-principal-portal.md)  
- Per indicazioni su come implementare la sicurezza con Gestione risorse di Azure, vedere [Considerazioni sulla sicurezza per Gestione risorse di Azure](best-practices-resource-manager-security.md)


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_0330_2016-->