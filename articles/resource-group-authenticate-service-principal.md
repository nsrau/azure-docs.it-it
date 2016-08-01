<properties
   pageTitle="Creare un'entità servizio di Azure con PowerShell | Microsoft Azure"
   description="Descrive come usare Azure PowerShell per creare un'applicazione Active Directory e un'entità servizio e concedere l'accesso alle risorse tramite il controllo degli accessi in base al ruolo. Illustra come autenticare l'applicazione con una password o un certificato."
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
   ms.date="07/18/2016"
   ms.author="tomfitz"/>

# Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
- [Portale](resource-group-create-service-principal-portal.md)

Quando si ha un'applicazione o uno script che deve accedere alle risorse, è probabile che si voglia eseguire il processo con le credenziali dell'utente. L'utente può avere autorizzazioni diverse da quelle che si vogliono assegnare al processo e responsabilità dell'utente relative al processo potrebbero cambiare. In alternativa, è possibile creare un'identità per l'applicazione che includa le credenziali di autenticazione e le assegnazioni di ruolo. L'applicazione esegue l'accesso con tale identità ogni volta che viene eseguita. Questo argomento illustra come usare [Azure PowerShell](powershell-install-configure.md) per impostare tutte le informazioni necessarie a un'applicazione per l'esecuzione con credenziali e identità proprie.

In questo articolo si creeranno due oggetti: l'applicazione Active Directory (AD) e l'entità servizio. L'applicazione AD include le credenziali, ovvero un ID applicazione e una password o un certificato. L'entità servizio include l'assegnazione di ruolo. Dall'applicazione Active Directory è possibile creare molte entità servizio. Questo argomento è incentrato su un'applicazione con un tenant singolo in cui è previsto che l'applicazione venga eseguita all'interno del sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione. Quando l'applicazione deve essere eseguita in molte organizzazioni, è anche possibile creare applicazioni multi-tenant. Le applicazioni multi-tenant si usano in genere per le applicazioni software come un servizio (SaaS, Software-as-a-Service). Per configurare un'applicazione multi-tenant, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).

Sono molti i concetti che è opportuno comprendere quando si lavora con Active Directory. Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e Oggetti entità servizio](./active-directory/active-directory-application-objects.md). Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Con PowerShell sono disponibili 2 opzioni per l'autenticazione dell'applicazione AD:

 - password
 - certificato

Se, dopo avere impostato l'applicazione AD, si intende accedere ad Azure da un altro framework di programmazione, ad esempio Python, Ruby o Node. js, l'autenticazione della password può essere la soluzione ottimale. Prima di decidere se usare una password o un certificato, vedere la sezione [Applicazioni di esempio](#sample-applications) per esempi di autenticazione nei diversi framework.

## Ottenere l'ID tenant

Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Active Directory. Poiché questo valore sarà necessario per l'autenticazione della password o del certificato, si dovrà ottenerlo.

1. Accedere al proprio account.

        Add-AzureRmAccount

2. Se è disponibile solo una sottoscrizione, è possibile usare:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Se sono disponibili più sottoscrizioni, specificare quella da usare per l'app AD. Selezionare la sottoscrizione in cui risiede Active Directory. Per altre informazioni, vedere [Amministrare la directory di Azure AD](./active-directory/active-directory-administer.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Passare ora a una sezione seguente per l'autenticazione della [password](#create-service-principal-with-password) o del [certificato](#create-service-principal-with-certificate).

## Creare un'entità servizio con password

In questa sezione si eseguiranno i passaggi per creare l'applicazione AD e l'entità servizio con una password.

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


     Dall'applicazione Active Directory è necessario creare un'entità servizio e assegnarvi un ruolo.

2. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

3. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md). Per assegnare un ruolo, è necessario avere l'accesso `Microsoft.Authorization/*/Write`, che viene concesso tramite il ruolo [Proprietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore Accesso utenti](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

L'operazione è terminata. L'applicazione AD e l''entità servizio sono così configurate. La sezione successiva illustra come accedere con le credenziali tramite PowerShell. Tuttavia, se si vogliono usare le credenziali dell'applicazione di codice, non è necessario continuare con questo argomento. È possibile passare ad [Applicazioni di esempio](#sample-applications) per esempi di accesso con l'ID applicazione e la password.

### Fornire le credenziali tramite PowerShell

A questo punto è necessario accedere come applicazione per eseguire operazioni.

1. Creare un nuovo oggetto **PSCredential** che contenga le credenziali eseguendo il comando **Get-Credential**. Prima di eseguire questo comando è necessaria la proprietà **ApplicationId** o **IdentifierUris**, quindi accertarsi che siano disponibili per poterle incollare.

        $creds = Get-Credential

2. Verrà richiesto di immettere le credenziali. Per il nome utente, usare **ApplicationId** o **IdentifierUris** specificato durante la creazione dell'applicazione. Per la password, usare quella specificata durante la creazione dell'account.

     ![immettere le credenziali](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

4. Accedere come entità servizio specificando che questo account è un'entità servizio e fornendo l'oggetto credenziali. È necessario l'ID tenant è recuperato in [Ottenere l'ID tenant](#get-tenant-id).

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

5. Per usare il token di accesso corrente in una sessione successiva, è possibile salvare il profilo.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     È possibile aprire il profilo ed esaminare il relativo contenuto. Si noti che contiene un token di accesso.
        
6. Invece di accedere manualmente quando in seguito si vuole eseguire codice come entità servizio, caricare semplicemente il profilo.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Il token di accesso scadrà, quindi l'uso di un profilo salvato funziona solo finché il token è valido.
        
## Creare un'entità servizio con certificato

In questa sezione si eseguiranno i passaggi per creare l'applicazione AD e l'entità servizio con un certificato.

1. Creare un certificato autofirmato. Se si usa **Windows 10 o Windows Server 2016 Technical Preview**, eseguire questo comando:

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
     La variabile contiene informazioni sul certificato, inclusa l'identificazione personale.
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

        Thumbprint                                Subject
        ----------                                -------
        724213129BD2B950BB3F64FAB0C877E9348B16E9  CN=exampleapp

     Se **non** si usa Windows 10 o Windows Server 2016 Technical Preview, il cmdlet **New SelfSignedCertificate** non sarà disponibile. Scaricare invece lo script di PowerShell [Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6) ed eseguire i comandi seguenti per generare un certificato. Questo passaggio non è necessario se il certificato già stato creato nell'esempio precedente.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\New-SelfSignedCertificateEx.ps1
        New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
        $cert = Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp

2. Recuperare il valore della chiave dal certificato.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

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


5. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

6. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio verrà concessa all'entità servizio l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per il parametro **ServicePrincipalName**, fornire il valore **ApplicationId** o **IdentifierUris** usato quando è stata creata l'applicazione. Per ulteriori informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md). Per assegnare un ruolo, è necessario avere l'accesso `Microsoft.Authorization/*/Write`, che viene concesso tramite il ruolo [Proprietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore Accesso utenti](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

L'operazione è terminata. L'applicazione AD e l''entità servizio sono così configurate. La sezione successiva illustra come effettuare l'accesso con certificato tramite PowerShell.

### Fornire il certificato tramite uno script di PowerShell automatizzato

Per l'autenticazione nello script, specificare che l'account è un'entità servizio e fornire l'identificazione personale del certificato, l'ID dell'applicazione e l'ID tenant. Questi valori sono già disponibili nelle variabili **$azureAdApplication.ApplicationId**, **$cert. Identificazione personale** e **$tenant**. Per automatizzare lo script, è possibile archiviare questi valori come variabili di ambiente e recuperarli durante l'esecuzione oppure è possibile includerli nello script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint 000000 -ApplicationId 000000 -TenantId 0000000

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

Se è necessario recuperare l'ID applicazione in seguito, usare:

    (Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example").ApplicationId
        
Se è necessario recuperare l'identificazione personale del certificato in seguito, usare:

    (Get-ChildItem -Path cert:\CurrentUser\My* -DnsName exampleapp).Thumbprint

Se è necessario recuperare l'ID tenant in seguito, vedere [Ottenere l'ID tenant](#get-tenant-id).

## Applicazioni di esempio

Le applicazioni di esempio seguenti illustrano come effettuare l'accesso come entità servizio.

**.NET**

- [Deploy an SSH Enabled VM with a Template with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello con .NET)
- [Manage Azure resources and resource groups with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/) (Gestire risorse e gruppi di risorse di Azure con .NET)

**Java**

- [Getting Started with Resources - Deploy Using ARM Template - in Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/) (Introduzione alle risorse: distribuzione tramite il modello di Azure Resource Manager in Java)
- [Getting Started with Resources - Manage Resource Group - in Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//) (Introduzione alle risorse: gestire un gruppo di risorse in Java)

**Python**

- [Deploy an SSH Enabled VM with a Template in Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello in Python)
- [Managing Azure Resource and Resource Groups with Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/) (Gestione di risorse e gruppi di risorse di Azure con Python)

**Node.JS**

- [Deploy an SSH Enabled VM with a Template in Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello in Node.js)
- [Manage Azure resources and resource groups with Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/) (Gestire risorse e gruppi di risorse di Azure con Node.js)

**Ruby**

- [Deploy an SSH Enabled VM with a Template in Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)
- [Managing Azure Resource and Resource Groups with Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/) (Gestione di risorse e gruppi di risorse di Azure con Ruby)

## Passaggi successivi
  
- Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).

<!---HONumber=AcomDC_0720_2016-->