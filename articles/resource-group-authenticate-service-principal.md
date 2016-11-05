---
title: Creare un'entità servizio di Azure con PowerShell | Microsoft Docs
description: Descrive come usare Azure PowerShell per creare un'applicazione Active Directory e un'entità servizio e concedere l'accesso alle risorse tramite il controllo degli accessi in base al ruolo. Illustra come autenticare l'applicazione con una password o un certificato.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/12/2016
ms.author: tomfitz

---
# Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portale](resource-group-create-service-principal-portal.md)
> 
> 

Quando si ha un'applicazione o uno script che deve accedere alle risorse, è molto probabile che non si voglia eseguire il processo con le proprie credenziali. È possibile che si vogliano usare autorizzazioni diverse per l'applicazione e che si preferisca che l'applicazione non continui a usare le credenziali in caso di cambiamento delle responsabilità dell'utente. Si crea quindi un'identità per l'applicazione che include credenziali di autenticazione e assegnazioni di ruolo. Ogni volta che viene eseguita, l'app eseguirà l'autenticazione con tali credenziali. Questo argomento illustra come usare [Azure PowerShell](powershell-install-configure.md) per impostare tutte le informazioni necessarie a un'applicazione per l'esecuzione con credenziali e identità proprie.

Con PowerShell sono disponibili due opzioni per l'autenticazione dell'applicazione AD:

* password
* certificato

Questo argomento illustra come usare entrambe le opzioni in PowerShell. Se si intende accedere ad Azure da un framework di programmazione, ad esempio Python, Ruby o Node.js, l'autenticazione della password può essere la soluzione ottimale. Prima di decidere se usare una password o un certificato, vedere la sezione [Applicazioni di esempio](#sample-applications) per esempi di autenticazione nei diversi framework.

## Concetti relativi ad Active Directory
In questo articolo vengono creati due oggetti: l'applicazione Active Directory (AD) e l'entità servizio. L'applicazione AD è la rappresentazione globale dell'applicazione e contiene le credenziali, ovvero un ID applicazione e una password o un certificato. L'entità servizio è la rappresentazione locale dell'applicazione in un'istanza di Active Directory e contiene l'assegnazione di ruolo. Questo argomento è incentrato su un'applicazione con un tenant singolo in cui è previsto che l'applicazione venga eseguita all'interno del sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione. In un'applicazione con un tenant singolo vengono usate un'app AD e un'entità servizio.

Ci si potrebbe chiedere perché siano necessari entrambi gli oggetti. Questo approccio risulta più opportuno quando si considerano le applicazioni multi-tenant. Le applicazioni multi-tenant vengono in genere usate per applicazioni SaaS (Software-as-a-Service), che prevedono l'esecuzione delle applicazioni in molte sottoscrizioni diverse. Per le applicazioni multi-tenant vengono usate un'app AD e più entità servizio (una in ogni istanza di Active Directory che concede l'accesso all'app). Per configurare un'applicazione multi-tenant, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).

## Autorizzazioni necessarie
Per completare questo argomento è necessario avere autorizzazioni sufficienti sia nell'istanza di Azure Active Directory che nella sottoscrizione di Azure. In particolare, è necessario poter creare un'app in Active Directory e assegnare l'entità servizio a un ruolo.

In Active Directory, l'account deve avere un ruolo di amministratore (ad esempio **Amministratore globale** o **Amministratore utenti**). Se l'account è assegnato al ruolo **Utente**, è necessario che le autorizzazioni vengano elevate da un amministratore.

Nella sottoscrizione, l'account deve avere l'accesso `Microsoft.Authorization/*/Write`, che viene concesso tramite il ruolo [Proprietario](active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore Accesso utenti](active-directory/role-based-access-built-in-roles.md#user-access-administrator). Se l'account è assegnato al ruolo **Collaboratore**, quando si prova ad assegnare l'entità servizio a un ruolo viene visualizzato un errore. Anche in questo caso, l'amministratore della sottoscrizione deve concedere diritti di accesso sufficienti.

Passare ora alla sezione relativa all'autenticazione della [password](#create-service-principal-with-password) o del [certificato](#create-service-principal-with-certificate).

## Creare un'entità servizio con password
In questa sezione vengono eseguiti i passaggi per:

* creare l'applicazione Active Directory con password
* creare l'entità servizio
* assegnare il ruolo Lettore all'entità servizio

Per eseguire rapidamente i passaggi, usare i tre cmdlet seguenti.

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Verranno ora esaminati più attentamente questi passaggi per comprendere meglio il processo.

1. Accedere al proprio account.
   
        Add-AzureRmAccount
2. Creare una nuova applicazione Active Directory specificando un nome visualizzato, l'URI che descrive l'applicazione, gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.
   
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"
   
     Per le applicazioni con un tenant singolo, gli URI non vengono convalidati.
   
     Se l'account non ha le [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica che l'autenticazione non è stata autorizzata o non è stata trovata alcuna sottoscrizione nel contesto.
3. Esaminare il nuovo oggetto dell'applicazione.
   
        $app
   
     Si noti in particolare la proprietà **ApplicationId**, necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione del token di accesso.
   
        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}
4. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.
   
        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
5. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio viene aggiunta l'entità servizio al ruolo **Lettore**, concedendo così l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](active-directory/role-based-access-built-in-roles.md). Per il parametro **ServicePrincipalName**, specificare il valore **ApplicationId** usato quando è stata creata l'applicazione.
   
        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid
   
    Se l'account non ha autorizzazioni sufficienti per assegnare un ruolo, verrà visualizzato un messaggio di errore. Il messaggio segnala che l'account **non è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' sull'ambito '/subscriptions/{guid}'**.

L'operazione è terminata. L'applicazione AD e l''entità servizio sono così configurate. La sezione successiva illustra come accedere con le credenziali tramite PowerShell. Se si desidera utilizzare le credenziali nell'applicazione di codice, è possibile passare a [Applicazioni di esempio](#sample-applications).

### Fornire le credenziali tramite PowerShell
A questo punto è necessario accedere come applicazione per eseguire operazioni.

1. Creare un oggetto **PSCredential** che contenga le credenziali eseguendo il comando **Get-Credential**. Prima di eseguire questo comando si richiede la proprietà **ApplicationId**, quindi accertarsi di poterla incollare.
   
        $creds = Get-Credential
2. A questo punto si devono immettere le credenziali. Per il nome utente, usare l'**ApplicationId** usato durante la creazione dell'applicazione. Per la password, usare quella specificata durante la creazione dell'account.
   
     ![immettere le credenziali](./media/resource-group-authenticate-service-principal/arm-get-credential.png)
3. Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Active Directory. Se è disponibile solo una sottoscrizione, è possibile usare:
   
        $tenant = (Get-AzureRmSubscription).TenantId
   
     Se sono disponibili più sottoscrizioni, specificare quella in cui risiede la propria Active Directory. Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).
   
        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
4. Accedere come entità servizio specificando che questo account è un'entità servizio e fornendo l'oggetto credenziali.
   
        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
   
     A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

### Salvare il token di accesso per semplificare l'accesso
Il token di accesso può essere slavato al fine di evitare di dover fornire le credenziali dell'entità servizio ogni volta che viene richiesto di effettuare l'accesso.

1. Salvare il profilo per usare il token di accesso corrente in una sessione successiva.
   
        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   
     Aprire il profilo ed esaminare il relativo contenuto. Si noti che contiene un token di accesso.
2. Anziché effettuare di nuovo l'accesso manuale, è sufficiente caricare il profilo.
   
        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json

> [!NOTE]
> Il token di accesso scade, quindi l'uso di un profilo salvato funziona solo fino al termine del periodo di validità del token.
> 
> 

## Creare un'entità servizio con certificato
In questa sezione vengono eseguiti i passaggi per:

* creare un certificato autofirmato
* creare l'applicazione AD con certificato
* creare l'entità servizio
* assegnare il ruolo Lettore all'entità servizio

Per eseguire rapidamente questi passaggi con Azure PowerShell 2.0 in Windows 10 o Windows Server 2016 Technical Preview, vedere i seguenti cmdlet.

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Verranno ora esaminati più attentamente questi passaggi per comprendere meglio il processo. In questo articolo viene inoltre illustrato come eseguire le operazioni quando si utilizzano versioni precedenti dei sistemi operativi o di Azure PowerShell.

### Creare il certificato autofirmato
La versione di PowerShell disponibile con Windows 10 e Windows Server 2016 Technical Preview dispone di un cmdlet **New-SelfSignedCertificate** aggiornato per generare un certificato autofirmato. I sistemi operativi precedenti hanno il cmdlet New-SelfSignedCertificate, ma questo non offre i parametri necessari per questo argomento. È invece necessario importare un modulo per generare il certificato. Questo argomento illustra entrambi gli approcci per generare il certificato in base al sistema operativo in uso.

* Se si usa **Windows 10 o Windows Server 2016 Technical Preview**, eseguire il seguente comando per creare un certificato autofirmato:
  
        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
* Se **non si dispone di Windows 10 o Windows Server 2016 Technical Preview**, è necessario scaricare il [generatore di certificato autofirmato](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) da Microsoft Script Center. Estrarre i contenuti e importare il cmdlet necessario.
  
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
     Dopodiché, generare il certificato.
  
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Ora si dispone del certificato e si può procedere con la creazione dell'app AD.

### Creare l'applicazione Active Directory e un'entità servizio
1. Recuperare il valore della chiave dal certificato.
   
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
2. Accedere all'account Azure.
   
        Add-AzureRmAccount
3. Creare una nuova applicazione Active Directory specificando un nome visualizzato, l'URI che descrive l'applicazione, gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.
   
     Se si dispone di Azure PowerShell 2.0 (versione di agosto 2016 o successive), utilizzare il cmdlet seguente:
   
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   
    Se si dispone di Azure PowerShell 1.0, utilizzare il cmdlet seguente:
   
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   
    Per le applicazioni con un tenant singolo, gli URI non vengono convalidati.
   
    Se l'account non ha le [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica che l'autenticazione non è stata autorizzata o non è stata trovata alcuna sottoscrizione nel contesto.
   
    Esaminare il nuovo oggetto dell'applicazione.
   
        $app
   
    La proprietà **ApplicationId** è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token di accesso.
   
        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}
4. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.
   
        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
5. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio viene aggiunta l'entità servizio al ruolo **Lettore**, concedendo così l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](active-directory/role-based-access-built-in-roles.md). Per il parametro **ServicePrincipalName**, specificare il valore **ApplicationId** usato quando è stata creata l'applicazione.
   
        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid
   
    Se l'account non ha autorizzazioni sufficienti per assegnare un ruolo, verrà visualizzato un messaggio di errore. Il messaggio segnala che l'account **non è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' sull'ambito '/subscriptions/{guid}'**.

L'operazione è terminata. L'applicazione AD e l''entità servizio sono così configurate. La sezione successiva illustra come effettuare l'accesso con certificato tramite PowerShell.

### Fornire il certificato tramite uno script di PowerShell automatizzato
Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Active Directory. Se è disponibile solo una sottoscrizione, è possibile usare:

    $tenant = (Get-AzureRmSubscription).TenantId

Se sono disponibili più sottoscrizioni, specificare quella in cui risiede la propria Active Directory. Per altre informazioni, vedere [Amministrare la directory di Azure AD](active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Per l'autenticazione nello script, specificare che l'account è un'entità servizio e fornire l'identificazione personale del certificato, l'ID dell'applicazione e l'ID tenant. Per automatizzare lo script, è possibile archiviare questi valori come variabili di ambiente e recuperarli durante l'esecuzione oppure è possibile includerli nello script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

## Applicazioni di esempio
Le applicazioni di esempio seguenti illustrano come effettuare l'accesso come entità servizio.

**.NET**

* [Deploy an SSH Enabled VM with a Template with .NET (Distribuire una macchina virtuale abilitata per SSH con un modello con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Manage Azure resources and resource groups with .NET (Gestire risorse e gruppi di risorse di Azure con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Getting Started with Resources - Deploy Using ARM Template - in Java (Introduzione alle risorse: distribuzione tramite il modello di Azure Resource Manager in Java)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Getting Started with Resources - Manage Resource Group - in Java (Introduzione alle risorse: gestire un gruppo di risorse in Java)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Deploy an SSH Enabled VM with a Template in Python (Distribuire una macchina virtuale abilitata per SSH con un modello in Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Managing Azure Resource and Resource Groups with Python (Gestione di risorse e gruppi di risorse di Azure con Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Deploy an SSH Enabled VM with a Template in Node.js (Distribuire una macchina virtuale abilitata per SSH con un modello in Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Manage Azure resources and resource groups with Node.js (Gestire risorse e gruppi di risorse di Azure con Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Deploy an SSH Enabled VM with a Template in Ruby (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Managing Azure Resource and Resource Groups with Ruby (Gestione di risorse e gruppi di risorse di Azure con Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## Passaggi successivi
* Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
* Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e Oggetti entità servizio](active-directory/active-directory-application-objects.md).
* Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](active-directory/active-directory-authentication-scenarios.md).

<!---HONumber=AcomDC_0914_2016-->