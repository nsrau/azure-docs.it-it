<properties
   pageTitle="Autenticazione di un'entità servizio con Gestione risorse di Azure"
   description="Descrive come concedere l'accesso e autenticare un'entità servizio mediante il controllo degli accessi in base al ruolo. Mostra come eseguire queste attività con PowerShell e l'interfaccia della riga di comando di Azure."
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
   ms.date="11/18/2015"
   ms.author="tomfitz"/>

# Autenticazione di un'entità servizio con Gestione risorse di Azure

Questo argomento mostra come consentire un'entità servizio (ad esempio un processo, un'applicazione o un servizio automatizzato) per accedere ad altre risorse nella sottoscrizione. Con Gestione risorse di Azure è possibile usare il controllo degli accessi in base al ruolo per l'esecuzione di azioni consentite in un'entità servizio e l'autenticazione di tale entità. Questo argomento mostra come usare PowerShell e l'interfaccia della riga di comando di Azure per assegnare un ruolo e autenticazione un'entità servizio.

Viene illustrato come eseguire l'autenticazione con un nome utente e password o con un certificato.

È possibile utilizzare Azure PowerShell o CLI Azure per Mac, Linux e Windows. Se Azure PowerShell non è installato, vedere [Come installare e configurare Azure PowerShell](./powershell-install-configure.md). Se non è installato CLI Azure, vedere [Installare e configurare CLI Azure](xplat-cli-install.md). Per informazioni sull'utilizzo del portale per eseguire questa procedura, vedere [Creare un'applicazione e un'entità servizio di Active Directory tramite il portale](resource-group-create-service-principal-portal.md)

## Concetti
1. Azure Active Directory (AAD): servizio di gestione delle identità e degli accessi per il cloud. Per altre informazioni, vedere [Informazioni su Azure Active Directory](active-directory/active-directory-whatis.md)
2. Entità servizio: istanza di un'applicazione in una directory che richiede l’accesso ad altre risorse.
3. Applicazione AD: record di directory che identifica un'applicazione in AAD. Per altre informazioni, vedere [Nozioni di base sull'autenticazione in Azure AD](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Autenticazione dell'entità servizio con password - PowerShell

In questa sezione, si eseguiranno i passaggi per creare un’entità servizio per un'applicazione Azure Active Directory, assegnare un ruolo per l'entità servizio e autenticare come entità servizio specificando l'identificatore dell'applicazione e la password.

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

1. Accedere al proprio account.

        PS C:\> Login-AzureRmAccount

1. Creare una nuova applicazione AAD eseguendo il comando **New-AzureRmADApplication**. Specificare un nome visualizzato per l'applicazione, l'URI a una pagina che descrive l'applicazione (il collegamento non è verificato), gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     Esaminare il nuovo oggetto dell'applicazione. La proprietà **ApplicationId** è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. Creare un'entità servizio per l'applicazione.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     È stata creata un'entità servizio nella directory, ma al servizio non sono assegnate autorizzazioni o ambiti. È necessario concedere esplicitamente le autorizzazioni dell'entità servizio per eseguire operazioni in un ambito.

3. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md)

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. Recuperare la sottoscrizione in cui è stata creata l'assegnazione di ruolo. La sottoscrizione verrà usata successivamente per ottenere il **TenantId** del tenant in cui si trova l'assegnazione di ruolo dell'entità servizio.

        PS C:\> $subscription = Get-AzureRmSubscription

     Se l'assegnazione di ruolo è stata creata in una sottoscrizione diversa da quella attualmente selezionata, è possibile specificare il parametro **SubscriptoinId** o **SubscriptionName** per recuperare una sottoscrizione diversa.

5. Per eseguire l'accesso come entità di servizio tramite PowerShell, creare un nuovo oggetto **PSCredential** che contenga le credenziali eseguendo il comando **Get-Credential**.

        PS C:\> $creds = Get-Credential

     Verrà richiesto di immettere le credenziali.

     ![][1]

     Per il nome utente, usare **ApplicationId** o **IdentifierUris** specificato durante la creazione dell'applicazione. Per la password, usare quella specificata durante la creazione dell'account.

     Usare le credenziali immesse come input per il cmdlet **Login-AzureRmAccount**, che effettuerà l'accesso dell'entità servizio:

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

     A questo punto dovrebbe essere stata eseguita l'autenticazione per l'applicazione AAD creata come entità servizio.

6. Per eseguire l'autenticazione da un'applicazione, includere il codice .NET seguente. Dopo il recupero del token, è possibile accedere alle risorse nella sottoscrizione.

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



## Autenticazione dell'entità servizio con certificato - PowerShell

In questa sezione, si eseguiranno i passaggi per creare un’entità servizio per un'applicazione Azure Active Directory, assegnare un ruolo per l'entità servizio e autenticare come entità servizio specificando fornendo un certificato. In questo argomento si presuppone che si disponga di un certificato.

Mostra due modi per lavorare con certificati - credenziali della chiave e valori della chiave. È possibile utilizzare entrambi gli approcci.

In primo luogo, è necessario configurare alcuni valori di PowerShell che verranno utilizzati in un secondo momento durante la creazione dell'applicazione.

1. Accedere al proprio account.

        PS C:\> Login-AzureRmAccount

1. Per entrambi gli approcci, creare un oggetto X509Certificate nel certificato e recuperare il valore della chiave. Utilizzare il percorso del certificato e la password per il certificato.

        $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Se si usano credenziali della chiave, creare l'oggetto credenziali della chiave e impostare il valore al `$keyValue` dal passaggio precedente.

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

        $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    In alternativa, utilizzare il secondo esempio per assegnare le credenziali di chiave.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    Esaminare il nuovo oggetto dell'applicazione. La proprietà **ApplicationId** è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token JWT.

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. Creare un'entità servizio per l'applicazione.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    È stata creata un'entità servizio nella directory, ma al servizio non sono assegnate autorizzazioni o ambiti. È necessario concedere esplicitamente le autorizzazioni dell'entità servizio per eseguire operazioni in un ambito.

5. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md)

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. Per eseguire l'autenticazione da un'applicazione, includere il codice .NET seguente. Dopo il recupero del client, è possibile accedere alle risorse nella sottoscrizione.

        string clientId = "<Application ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<Tenant id or tenant name>"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
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
        var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
        var client = new ResourceManagementClient(creds); 
        

## L'autenticazione del principale di servizio con password - CLI Azure

Iniziare creando un'entità servizio. Per farlo, è necessario creare un'applicazione nella directory. Questa sezione fornisce spiegazioni su come creare una nuova applicazione nella directory.

1. Passare al modulo Gestione risorse di Azure e accedere al proprio account.

        azure config mode arm
        azure login

2. Creare una nuova applicazione AAD eseguendo il comando **azure app create**. Specificare un nome visualizzato per l'applicazione, l'URI a una pagina che descrive l'applicazione (il collegamento non è verificato), gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    Viene restituita l'applicazione Azure AD. La proprietà ApplicationId è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token JWT.

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

3. Creare un'entità servizio per l'applicazione. Fornire l'id dell'applicazione che è stato restituito nel passaggio precedente.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    Viene restituita la nuova entità servizio. Quando si concedono autorizzazioni, è necessario l'Id oggetto.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    È stata creata un'entità servizio nella directory, ma al servizio non sono assegnate autorizzazioni o ambiti. È necessario concedere esplicitamente le autorizzazioni dell'entità servizio per eseguire operazioni in un ambito.

4. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md)

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

5. Determinare il **TenantId** del tenant in cui si trova l'assegnazione di ruolo dell'entità servizio elencando gli account e cercando la proprietà **TenantId** nell'output.

        azure account list --json

6. Accedere usando l'entità servizio come identità. Per il nome utente, usare l'**ApplicationId** usato durante la creazione dell'applicazione. Per la password, usare quella specificata durante la creazione dell'account.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    A questo punto dovrebbe essere stata eseguita l'autenticazione per l'applicazione AAD creata come entità servizio.

## Autenticazione dell'entità servizio con certificato - Interfaccia della riga di comando di Azure

In questa sezione si eseguiranno i passaggi per creare un’entità servizio per un'applicazione Azure Active Directory che utilizza un certificato per l'autenticazione. In questo argomento si presuppone che si disponga di un certificato e di [OpenSSL](http://www.openssl.org/) installato.

1. Creare un file **.pem** con:

        openssl.exe pkcs12 -in examplecert.pfx -out examplecert.pem -nodes

2. Aprire il file **.pem** e copiare i dati del certificato.

3. Creare una nuova applicazione AAD eseguendo il comando **azure ad app create** e fornire i dati del certificato che è stato copiato nel passaggio precedente come valore della chiave.

        azure ad app create -n "<your application name>" --home-page "<https://YourApplicationHomePage>" -i "<https://YouApplicationUri>" --key-value <certificate data>

## Passaggi successivi
  
- Per una panoramica sul controllo degli accessi in base al ruolo, vedere [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md)  
- Per altre informazioni sull'uso del portale con le entità servizio, vedere [Creare una nuova entità servizio di Azure tramite il portale di Azure](./resource-group-create-service-principal-portal.md)  
- Per indicazioni su come implementare la sicurezza con Gestione risorse di Azure, vedere [Considerazioni sulla sicurezza per Gestione risorse di Azure](best-practices-resource-manager-security.md)


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=Nov15_HO4-->