<properties
   pageTitle="Creare un'applicazione Active Directory con PowerShell | Microsoft Azure"
   description="Descrive come usare Azure PowerShell per creare un'applicazione Active Directory e concedere l'accesso alle risorse tramite il controllo degli accessi in base al ruolo. Illustra come autenticare l'applicazione con una password o un certificato."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# Usare Azure PowerShell per creare un'applicazione Active Directory per l'accesso alle risorse

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
- [Portale](resource-group-create-service-principal-portal.md)


Questo argomento illustra come usare [Azure PowerShell](powershell-install-configure.md) per creare un'applicazione Active Directory (AD), ad esempio un processo automatizzato, un'applicazione o servizio che possa accedere ad altre risorse nella sottoscrizione. Con Azure Resource Manager è possibile usare il controllo degli accessi in base al ruolo per concedere azioni consentite nell'applicazione.

In questo articolo si creeranno due oggetti, ovvero l'applicazione Active Directory e l'entità servizio. L'applicazione Active Directory risiede nel tenant dove viene registrata l'app e definisce l'esecuzione del processo. L'entità servizio contiene l'identità dell'applicazione Active Directory e viene usata per l'assegnazione delle autorizzazioni. Dall'applicazione Active Directory è possibile creare molte entità servizio. Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e Oggetti entità servizio](./active-directory/active-directory-application-objects.md). Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Sono disponibili 2 opzioni per l'autenticazione dell'applicazione:

 - password: adatta quando un utente vuole accedere in modo interattivo durante l'esecuzione
 - certificato: adatta per script automatici che devono autenticarsi senza interazione dell'utente

## Creare l'applicazione Active Directory con password

In questa sezione si eseguiranno i passaggi per creare l'applicazione Active Directory con una password.

1. Accedere al proprio account.

        Add-AzureRmAccount

1. Creare una nuova applicazione Active Directory specificando un nome visualizzato per l'applicazione, l'URI a una pagina che descrive l'applicazione (il collegamento non è verificato), gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Esaminare il nuovo oggetto dell'applicazione.

        $azureAdApplication
        
     Si noti in particolare la proprietà **ApplicationId**, necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione del token di accesso.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}


### Creare l'entità servizio e assegnarla al ruolo

Dall'applicazione Active Directory è necessario creare un'entità servizio e assegnarvi un ruolo.

1. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Fornire manualmente le credenziali tramite PowerShell

È stata creata un'applicazione di Active Directory e un'entità servizio per tale applicazione. È stata assegnata l'entità servizio a un ruolo. A questo punto è necessario accedere come applicazione per eseguire operazioni. È possibile fornire manualmente le credenziali per l'apèplicazione durante l'esecuzione di comandi o uno script su richiesta.

1. Creare un nuovo oggetto **PSCredential** che contenga le credenziali eseguendo il comando **Get-Credential**.

        $creds = Get-Credential

2. Verrà richiesto di immettere le credenziali. Per il nome utente, usare **ApplicationId** o **IdentifierUris** specificato durante la creazione dell'applicazione. Per la password, usare quella specificata durante la creazione dell'account.

     ![immettere le credenziali](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

3. Recuperare la sottoscrizione in cui è stata creata l'assegnazione di ruolo. La sottoscrizione verrà usata per ottenere il **TenantId** del tenant in cui risiede l'assegnazione di ruolo dell'entità servizio.

        $subscription = Get-AzureRmSubscription

     Se l'account è collegato a più di una sottoscrizione, specificare il nome o l'ID di una sottoscrizione per ottenere la sottoscrizione che si vuole utilizzare.
     
        $subscription = Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate"

4. Accedere come entità servizio specificando che questo account è un'entità servizio e fornendo l'oggetto credenziali.

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId
        
     A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

5. Per usare il token di accesso corrente in una sessione successiva, è possibile salvare il profilo.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     È possibile aprire il profilo ed esaminare il relativo contenuto. Si noti che contiene un token di accesso.
        
6. Invece di accedere di nuovo quando in seguito si vuole eseguire codice come entità servizio, caricare semplicemente il profilo.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Il token di accesso scadrà, quindi l'uso di un profilo salvato funziona solo finché il token è valido. Per eseguire in modo permanente script automatici, usare un certificato.
        
## Creare l'applicazione Active Directory con certificato

In questa sezione si eseguiranno i passaggi per creare un'applicazione Active Directory con un certificato.

1. Creare un certificato autofirmato.

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     Verranno visualizzate informazioni sul certificato, inclusa l'identificazione personale.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

2. Recuperare il valore della chiave dal certificato.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

3. Accedere all'account Azure.

        Add-AzureRmAccount

4. Creare un'applicazione nella directory.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
        
    Esaminare il nuovo oggetto dell'applicazione.

        $azureAdApplication

    La proprietà **ApplicationId** è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token di accesso.

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}    


### Creare l'entità servizio e assegnarla al ruolo

1. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

2. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

### Preparare i valori per lo script

Nello script si passeranno tre valori necessari per accedere come entità servizio. Saranno necessari:

- ID applicazione
- tenant id 
- Identificazione personale del certificato

L'ID applicazione e l'identificazione personale del certificato sono stati visualizzati nei passaggi precedenti. Tuttavia, se occorre recuperare questi valori in un secondo momento, di seguito sono visualizzati i comandi necessari, insieme al comando per ottenere l'ID tenant.

1. Per recuperare l'ID tenant, usare:

        (Get-AzureRmSubscription).TenantId 

    In alternativa, se sono disponibili più sottoscrizioni, fornire il nome della sottoscrizione:

        (Get-AzureRmSubscription -SubscriptionName "Azure MSDN - Visual Studio Ultimate").TenantId
        
2. Per recuperare l'ID applicazione, usare:

        (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
3. Per recuperare l'identificazione personale del certificato, usare:

        (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

### Fornire il certificato tramite uno script di PowerShell automatizzato

È stata creata un'applicazione di Active Directory e un'entità servizio per tale applicazione. È stata assegnata l'entità servizio a un ruolo. A questo punto, è necessario accedere come entità servizio per eseguire operazioni come tale.

Per l'autenticazione nello script, specificare che l'account è un'entità servizio e fornire l'identificazione personale del certificato, l'ID dell'applicazione e l'ID tenant.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint {thumbprint} -ApplicationId {applicationId} -TenantId {tenantid}

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

## Passaggi successivi
  
- Per esempi di autenticazione .NET, vedere [Azure Resource Manager SDK per .NET](resource-manager-net-sdk.md).
- Per esempi di autenticazione Java, vedere [Azure Resource Manager SDK per Java](resource-manager-java-sdk.md). 
- Per esempi di autenticazione Python, vedere l'articolo relativo all'[autenticazione di gestione risorse per Python](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html).
- Per esempi di autenticazione REST, vedere [API REST di Resource Manager](resource-manager-rest-api.md).
- Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori sull'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).

<!---HONumber=AcomDC_0601_2016-->