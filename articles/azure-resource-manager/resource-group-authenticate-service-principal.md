---
title: "Creare un&quot;identità per un&quot;app Azure con PowerShell | Documentazione Microsoft"
description: "Descrive come usare Azure PowerShell per creare un&quot;applicazione Active Directory e un&quot;entità servizio e concedere l&quot;accesso alle risorse tramite il controllo degli accessi in base al ruolo. Illustra come autenticare l&quot;applicazione con una password o un certificato."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 31495f402b810c524bd7b906498774302500b732


---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portale](resource-group-create-service-principal-portal.md)
> 
> 

Quando si ha un'app o uno script che deve accedere alle risorse, è possibile configurare un'identità per l'app ed eseguirne l'autenticazione con credenziali specifiche. Questo approccio è preferibile all'esecuzione dell'app con le credenziali dell'utente per i motivi seguenti:

* È possibile assegnare all'identità dell'app autorizzazioni diverse rispetto a quelle dell'utente. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.
* Non è necessario modificare le credenziali dell'app in caso di cambiamento delle responsabilità dell'utente. 
* È possibile usare un certificato per automatizzare l'autenticazione in caso di esecuzione di uno script automatico.

Questo argomento illustra come usare [Azure PowerShell](/powershell/azureps-cmdlets-docs) per impostare tutte le informazioni necessarie a un'applicazione per l'esecuzione con credenziali e identità proprie.

Con PowerShell sono disponibili due opzioni per l'autenticazione dell'applicazione AD:

* password
* certificato

Questo argomento illustra come usare entrambe le opzioni in PowerShell. Se si intende accedere ad Azure da un framework di programmazione, ad esempio Python, Ruby o Node.js, l'autenticazione della password può essere la soluzione ottimale. Prima di decidere se usare una password o un certificato, vedere la sezione [Applicazioni di esempio](#sample-applications) per esempi di autenticazione nei diversi framework.

## <a name="active-directory-concepts"></a>Concetti relativi ad Active Directory
In questo articolo vengono creati due oggetti: l'applicazione Active Directory (AD) e l'entità servizio. L'applicazione AD è la rappresentazione globale dell'applicazione e contiene le credenziali, ovvero un ID applicazione e una password o un certificato. L'entità servizio è la rappresentazione locale dell'applicazione in un'istanza di Active Directory e contiene l'assegnazione di ruolo. Questo argomento è incentrato su un'applicazione con un tenant singolo in cui è previsto che l'applicazione venga eseguita all'interno del sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione. In un'applicazione con un tenant singolo vengono usate un'app AD e un'entità servizio.

Ci si potrebbe chiedere perché siano necessari entrambi gli oggetti. Questo approccio risulta più opportuno quando si considerano le applicazioni multi-tenant. Le applicazioni multi-tenant vengono in genere usate per applicazioni SaaS (Software-as-a-Service), che prevedono l'esecuzione delle applicazioni in molte sottoscrizioni diverse. Per le applicazioni multi-tenant vengono usate un'app AD e più entità servizio (una in ogni istanza di Active Directory che concede l'accesso all'app). Per configurare un'applicazione multi-tenant, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per completare questo argomento è necessario avere autorizzazioni sufficienti sia nell'istanza di Azure Active Directory che nella sottoscrizione di Azure. In particolare, è necessario poter creare un'app in Active Directory e assegnare l'entità servizio a un ruolo. 

Il modo più semplice per verificare se l'account dispone delle autorizzazioni appropriate è tramite il portale. Vedere [Controllare le autorizzazioni necessarie](resource-group-create-service-principal-portal.md#required-permissions).

Passare ora alla sezione relativa all'autenticazione della [password](#create-service-principal-with-password) o del [certificato](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Creare un'entità servizio con password
In questa sezione vengono eseguiti i passaggi per:

* creare l'applicazione Active Directory con password
* creare l'entità servizio
* Assegnare il ruolo Lettore all'entità servizio

Per eseguire rapidamente questa procedura, usare i cmdlet seguenti:

```powershell
$app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
Start-Sleep 15
New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
```

Lo script viene sospeso per 15 secondi per consentire la propagazione della nuova entità servizio in Active Directory. Se la durata dell'attesa dello script non è sufficiente, viene visualizzato un errore simile al seguente: "PrincipalNotFound: L'entità {id} non esiste nella directory". Se viene visualizzato questo errore, è possibile eseguire nuovamente il cmdlet per assegnarlo a un ruolo.

Verranno ora esaminati più attentamente questi passaggi per comprendere meglio il processo.

1. Accedere al proprio account.
   
   ```powershell
   Add-AzureRmAccount
   ```

2. Creare una nuova applicazione Active Directory specificando un nome visualizzato, l'URI che descrive l'applicazione, gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.

   ```powershell   
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "{Your_Password}"
   ```

     Per le applicazioni con un tenant singolo, gli URI non vengono convalidati.
   
     Se l'account non ha le [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica che l'autenticazione non è stata autorizzata o non è stata trovata alcuna sottoscrizione nel contesto.
3. Esaminare il nuovo oggetto dell'applicazione. 
   
   ```powershell
   $app
   ```
   
     Si noti in particolare la proprietà `ApplicationId`, necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione del token di accesso.
   
   ```powershell
   DisplayName             : exampleapp
   ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
   IdentifierUris          : {https://www.contoso.org/example}
   HomePage                : https://www.contoso.org
   Type                    : Application
   ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
   AvailableToOtherTenants : False
   AppPermissions          : 
   ReplyUrls               : {}
   ```
4. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.
   
   ```powershell
   New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
   ```

5. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio viene aggiunta l'entità servizio al ruolo Lettore, concedendo così l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md). Per il parametro `ServicePrincipalName`, specificare il valore `ApplicationId` usato quando è stata creata l'applicazione. Prima di eseguire questo cmdlet, è necessario attendere che la nuova entità servizio si propaghi in Active Directory. Quando si eseguono questi cmdlet manualmente, in genere tra i cmdlet trascorre tempo sufficiente. In uno script, è necessario aggiungere un passaggio di sospensione tra i cmdlet (ad esempio `Start-Sleep 15`). Se viene visualizzato un errore simile a "PrincipalNotFound: L'entità {id} non esiste nella directory", eseguire nuovamente il cmdlet.

   ```powershell   
   New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
   ```

    Se l'account non ha autorizzazioni sufficienti per assegnare un ruolo, verrà visualizzato un messaggio di errore. Il messaggio segnala che l'account non è autorizzato a eseguire l'azione Microsoft.Authorization/roleAssignments/write sull'ambito /subscriptions/{guid}. 

L'operazione è terminata. L'applicazione AD e l''entità servizio sono così configurate. La sezione successiva illustra come accedere con le credenziali tramite PowerShell. Se si desidera utilizzare le credenziali nell'applicazione di codice, è possibile passare a [Applicazioni di esempio](#sample-applications). 

### <a name="provide-credentials-through-powershell"></a>Fornire le credenziali tramite PowerShell
A questo punto è necessario accedere come applicazione per eseguire operazioni.

1. Creare un oggetto `PSCredential` contenente le credenziali eseguendo il comando `Get-Credential`. Prima di eseguire questo comando è necessario il valore `ApplicationId`, quindi accertarsi che sia disponibile per poterlo incollare.

   ```powershell   
   $creds = Get-Credential
   ```

2. A questo punto si devono immettere le credenziali. Per il nome utente, usare il valore `ApplicationId` usato quando è stata creata l'applicazione. Per la password, usare quella specificata durante la creazione dell'account.
   
     ![immettere le credenziali](./media/resource-group-authenticate-service-principal/arm-get-credential.png)
3. Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Active Directory. Se è disponibile solo una sottoscrizione, è possibile usare:

   ```powershell   
   $tenant = (Get-AzureRmSubscription).TenantId
   ```
   
     Se sono disponibili più sottoscrizioni, specificare quella in cui risiede la propria Active Directory. Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

   ```powershell
   $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
   ```

4. Accedere come entità servizio specificando che questo account è un'entità servizio e fornendo l'oggetto credenziali. 
   
   ```powershell
   Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
   ```
   
     A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

### <a name="save-access-token-to-simplify-log-in"></a>Salvare il token di accesso per semplificare l'accesso
Il token di accesso può essere slavato al fine di evitare di dover fornire le credenziali dell'entità servizio ogni volta che viene richiesto di effettuare l'accesso.

1. Salvare il profilo per usare il token di accesso corrente in una sessione successiva.
   
   ```powershell
   Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   ```
   
     Aprire il profilo ed esaminare il relativo contenuto. Si noti che contiene un token di accesso. 
2. Anziché effettuare di nuovo l'accesso manuale, è sufficiente caricare il profilo.
   
   ```powershell
   Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   ```

  > [!NOTE]
  > Il token di accesso scade, quindi l'uso di un profilo salvato funziona solo fino al termine del periodo di validità del token.
  >  

In alternativa, è possibile richiamare operazioni REST da PowerShell per eseguire l'accesso. Dalla risposta di autenticazione è possibile recuperare il token di accesso da usare con altre operazioni. Per un esempio di come recuperare il token di accesso richiamando operazioni REST, vedere [Generazione di un token di accesso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="create-service-principal-with-certificate"></a>Creare un'entità servizio con certificato
In questa sezione vengono eseguiti i passaggi per:

* creare un certificato autofirmato
* creare l'applicazione AD con certificato
* creare l'entità servizio
* Assegnare il ruolo Lettore all'entità servizio

Per eseguire rapidamente questa procedura con Azure PowerShell 2.0 in Windows 10 o Windows Server 2016 Technical Preview, vedere i cmdlet seguenti:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
$app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
Start-Sleep 15
New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
```

Lo script viene sospeso per 15 secondi per consentire la propagazione della nuova entità servizio in Active Directory. Se la durata dell'attesa dello script non è sufficiente, viene visualizzato un errore simile al seguente: "PrincipalNotFound: L'entità {id} non esiste nella directory". Se viene visualizzato questo errore, è possibile eseguire nuovamente il cmdlet per assegnarlo a un ruolo.

Verranno ora esaminati più attentamente questi passaggi per comprendere meglio il processo. In questo articolo viene inoltre illustrato come eseguire le operazioni quando si utilizzano versioni precedenti dei sistemi operativi o di Azure PowerShell.

### <a name="create-the-self-signed-certificate"></a>Creare il certificato autofirmato
La versione di PowerShell disponibile con Windows 10 e Windows Server 2016 Technical Preview include un cmdlet `New-SelfSignedCertificate` aggiornato per generare un certificato autofirmato. I sistemi operativi precedenti hanno il cmdlet New-SelfSignedCertificate, ma questo non offre i parametri necessari per questo argomento. È invece necessario importare un modulo per generare il certificato. Questo argomento illustra entrambi gli approcci per generare il certificato in base al sistema operativo in uso. 

* Se si usa **Windows 10 o Windows Server 2016 Technical Preview**, eseguire il seguente comando per creare un certificato autofirmato: 
   
  ```powershell
  $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
  ```
* Se **non si dispone di Windows 10 o Windows Server 2016 Technical Preview**, è necessario scaricare il [generatore di certificato autofirmato](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) da Microsoft Script Center. Estrarre i contenuti e importare il cmdlet necessario.

  ```powershell  
  # Only run if you could not use New-SelfSignedCertificate
  Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  ```
  
     Dopodiché, generare il certificato.
  
  ```powershell
  $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
  ```

Ora si dispone del certificato e si può procedere con la creazione dell'app AD.

### <a name="create-the-active-directory-app-and-service-principal"></a>Creare l'applicazione Active Directory e un'entità servizio
1. Recuperare il valore della chiave dal certificato.
   
   ```powershell
   $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
   ```
2. Accedere all'account Azure.
   
   ```powershell
   Add-AzureRmAccount
   ```
3. Creare una nuova applicazione Active Directory specificando un nome visualizzato, l'URI che descrive l'applicazione, gli URI che identificano l'applicazione e la password per l'identità dell'applicazione.
   
     Se si dispone di Azure PowerShell 2.0 (versione di agosto 2016 o successive), utilizzare il cmdlet seguente:

   ```powershell   
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   ```
   
    Se si dispone di Azure PowerShell 1.0, utilizzare il cmdlet seguente:

   ```powershell
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore
   ```
   
    Per le applicazioni con un tenant singolo, gli URI non vengono convalidati.
   
    Se l'account non ha le [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica che l'autenticazione non è stata autorizzata o non è stata trovata alcuna sottoscrizione nel contesto.
   
    Esaminare il nuovo oggetto dell'applicazione. 
   
   ```powershell
   $app
   ```
   
    La proprietà **ApplicationId** è necessaria per la creazione di entità servizio, le assegnazioni di ruolo e l'acquisizione di token di accesso.

   ```powershell
   DisplayName             : exampleapp
   ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
   IdentifierUris          : {https://www.contoso.org/example}
   HomePage                : https://www.contoso.org
   Type                    : Application
   ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
   AvailableToOtherTenants : False
   AppPermissions          : 
   ReplyUrls               : {}
   ```
4. Crea un'entità servizio per l'applicazione in uso passando l'ID dell'applicazione di Active Directory.
   
   ```powershell
   New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
   ```
5. Concedere le autorizzazioni dell'entità servizio nella sottoscrizione. In questo esempio viene aggiunta l'entità servizio al ruolo Lettore, concedendo così l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md). Per il parametro `ServicePrincipalName`, specificare il valore `ApplicationId` usato quando è stata creata l'applicazione. Prima di eseguire questo cmdlet, è necessario attendere che la nuova entità servizio si propaghi in Active Directory. Quando si eseguono questi cmdlet manualmente, in genere tra i cmdlet trascorre tempo sufficiente. In uno script, è necessario aggiungere un passaggio di sospensione tra i cmdlet (ad esempio `Start-Sleep 15`). Se viene visualizzato un errore simile a "PrincipalNotFound: L'entità {id} non esiste nella directory", eseguire nuovamente il cmdlet.
   
   ```powershell
   New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
   ```
   
    Se l'account non ha autorizzazioni sufficienti per assegnare un ruolo, verrà visualizzato un messaggio di errore. Il messaggio segnala che l'account non è autorizzato a eseguire l'azione Microsoft.Authorization/roleAssignments/write sull'ambito /subscriptions/{guid}.

L'operazione è terminata. L'applicazione AD e l''entità servizio sono così configurate. La sezione successiva illustra come effettuare l'accesso con certificato tramite PowerShell.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornire il certificato tramite uno script di PowerShell automatizzato
Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Active Directory. Se è disponibile solo una sottoscrizione, è possibile usare:

```powershell
$tenant = (Get-AzureRmSubscription).TenantId
```

Se sono disponibili più sottoscrizioni, specificare quella in cui risiede la propria Active Directory. Per altre informazioni, vedere [Amministrare la directory di Azure AD](../active-directory/active-directory-administer.md).

```powershell
$tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Per l'autenticazione nello script, specificare che l'account è un'entità servizio e fornire l'identificazione personale del certificato, l'ID dell'applicazione e l'ID tenant. Per automatizzare lo script, è possibile archiviare questi valori come variabili di ambiente e recuperarli durante l'esecuzione oppure è possibile includerli nello script.

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant
```

A questo punto è stata eseguita l'autenticazione come entità servizio per l'applicazione di Active Directory creata.

## <a name="change-credentials"></a>Modificare le credenziali

Per modificare le credenziali per un'app AD, a causa di una violazione della sicurezza o della scadenza delle credenziali, usare i cmdlet [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) e [New-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermadappcredential).

Per rimuovere tutte le credenziali per un'applicazione, usare:

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Per aggiungere una password, usare:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Per aggiungere un valore del certificato, creare un certificato autofirmato come illustrato in questo argomento. Successivamente, usare:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="sample-applications"></a>Applicazioni di esempio
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

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
* Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md). 
* Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](../active-directory/active-directory-authentication-scenarios.md).




<!--HONumber=Jan17_HO4-->


