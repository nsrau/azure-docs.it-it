---
title: "Esercitazione: Usare Azure Key Vault da un'applicazione Web | Microsoft Docs"
description: In questa esercitazione si apprenderà a usare Azure Key Vault da un'applicazione Web.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: barclayn
ms.openlocfilehash: b66c9912ba0b6508c2beb786d2327efa779c6645
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079464"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>Esercitazione: Usare Azure Key Vault da un'applicazione Web

In questa esercitazione si apprenderà a usare Azure Key Vault da un'applicazione Web in Azure. Mostra il processo di accesso a un segreto da Azure Key Vault per l'uso in un'applicazione Web. L'esercitazione quindi si basa sul processo e usa un certificato al posto di un segreto client. Questa esercitazione è destinata agli sviluppatori Web che conoscono le nozioni di base della creazione di applicazioni Web in Azure.

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Aggiungere le impostazioni dell'applicazione al file web.config
> * Aggiungere un metodo per ottenere un token di accesso
> * Recuperare il token all'avvio dell'applicazione
> * Autenticazione con un certificato

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un URI di un segreto in Azure Key Vault.
* Un ID client e un segreto client per un'applicazione Web registrata con Azure Active Directory che abbia accesso Azure Key Vault.
* Un'applicazione Web. Questa esercitazione mostra i passaggi per un'applicazione ASP.NET MVC distribuita in Azure come app Web.

Completare i passaggi descritti in [Introduzione ad Azure Key Vault](key-vault-get-started.md) per ottenere l'URI per un segreto, ID client e segreto client e registrare l'applicazione. L'applicazione Web accede all'insieme di credenziali e deve essere registrata in Azure Active Directory. Deve anche disporre dei diritti di accesso a Key Vault. Se non è così, tornare a Registrare un'applicazione nell'esercitazione introduttiva e ripetere i passaggi elencati. Per altre informazioni sulla creazione di app Web di Azure, vedere [Panoramica delle app Web](../app-service/app-service-web-overview.md).

Questo esempio dipende dall'esecuzione manuale del provisioning delle identità di Azure Active Directory. È invece consigliabile usare [identità gestite per risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md), che effettua automaticamente il provisioning delle identità di Azure AD. Per altre informazioni, vedere l'[esempio in GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) e l'[esercitazione correlata su Servizio app e Funzioni](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). È anche possibile eseguire l'[esercitazione Configurare un'applicazione Web di Azure per la lettura di un segreto da un insieme di credenziali delle chiavi](tutorial-web-application-keyvault.md) specifica del Key Vault.

## <a id="packages"></a>Aggiungere i pacchetti NuGet

Sono due i pacchetti che devono essere installati nell'applicazione Web.

* Active Directory Authentication Library: dispone dei metodi per interagire con Azure Active Directory e gestire l'identità utente
* Azure Key Vault Library: dispone dei metodi per interagire con Azure Key Vault

È possibile installare entrambi i pacchetti con la Console di Gestione pacchetti usando il comando Install-Package.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modificare web.config

Sono tre le impostazioni che devono essere aggiunte al file web.config, come indicato di seguito. I valori effettivi verranno aggiunti nel portale di Azure per un ulteriore livello di sicurezza.

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>Aggiungere il metodo per ottenere un token di accesso

Per usare l'API dell'insieme di credenziali chiave, è necessario un token di accesso. Il client dell'insieme di credenziali chiave gestisce le chiamate all'API dell'insieme di credenziali chiave, ma è necessario fornirgli una funzione che ottenga il token di accesso. Il seguente esempio mostra il codice per ottenere un accesso token da Azure Active Directory. Questo codice può far accedere ovunque nell'applicazione. È possibile aggiungere una classe Utils o EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>Recuperare il segreto all'avvio dell'applicazione

A questo punto è necessario il codice per chiamare l'API dell'insieme di credenziali chiave e recuperare il segreto. Purché venga chiamato prima che sia necessario usarlo, è possibile inserire il codice seguente in qualsiasi punto. In questo caso, viene inserito nell'evento Application Start in Global.asax in modo che venga eseguito una volta all'avvio e renda il segreto disponibile per l'applicazione.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Aggiungere le impostazioni dell'app nel portale di Azure

Nell'app Web di Azure è ora possibile aggiungere i valori effettivi per AppSettings nel portale di Azure. Eseguendo questo passaggio, i valori effettivi non saranno in web.config, ma saranno protetti tramite il portale in cui si dispone di funzionalità di controllo di accesso separate. Questi valori verranno sostituiti per i valori immessi in web.config. Assicurarsi che i nomi siano uguali.

![Impostazioni dell'applicazione visualizzate nel portale di Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autenticazione con un certificato anziché un client secret

Dopo aver compreso l'autenticazione di un'app di Azure AD usando l'ID client e il segreto client, vediamo come usare un ID Client e un certificato. Per utilizzare un certificato in un'applicazione Web di Azure, eseguire i seguenti passaggi:

1. Ottenere o creare un certificato
2. Associare il certificato a un'applicazione di Azure AD
3. Aggiungere codice all'applicazione Web per utilizzare il certificato
4. Aggiungere un certificato a un'applicazione Web

### <a name="get-or-create-a-certificate"></a>Ottenere o creare un certificato

 Per questa esercitazione verrà creato un certificato di prova. Ecco uno script per creare un certificato autofirmato. Cambiare la directory in cui si vuole creare i file del certificato.  Per la data di inizio e di fine del certificato è possibile usare la data corrente più un anno.

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = 'KVWebApp.pfx'
$CerFilePath = 'KVWebApp.cer'
$DNSName = 'MyComputer.Contoso.com'
$Password = 'MyPassword"'

$StoreLocation = 'CurrentUser' #be aware that LocalMachine requires elevated privileges
$CertBeginDate = Get-Date
$CertExpiryDate = $CertBeginDate.AddYears(1)

$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\$StoreLocation\My" -NotBefore $CertBeginDate -NotAfter $CertExpiryDate -KeySpec Signature
Export-PfxCertificate -cert $Cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $Cert -FilePath $CerFilePath 
```

Prendere nota della data finale e della password per il formato con estensione pfx (in questo esempio: 15 maggi 2019 e MyPassword). Saranno necessarie per lo script seguente. 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Associare il certificato a un'applicazione di Azure AD

Ora che si dispone di un certificato, è necessario associarlo a un'applicazione di Azure AD. L'associazione può essere completata in PowerShell. Eseguire questi comandi per associare il certificato con l'applicazione Azure AD:

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Dopo avere eseguito questi comandi, è possibile visualizzare l'applicazione in Azure AD. Quando si cercano le registrazioni di app, selezionare **Le mie app** anziché "Tutte le app" nella finestra di dialogo di ricerca. 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Aggiungere codice all'applicazione Web per utilizzare il certificato

Ora il codice verrà aggiunto all'applicazione Web per accedere al certificato e utilizzarlo per l'autenticazione. 

Prima di tutto viene il codice per accedere al certificato. Si noti che StoreLocation è CurrentUser anziché LocalMachine. Si sta specificando 'false' per il metodo Find, poiché si sta utilizzando un certificato di prova.

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



Poi viene il codice che utilizza il CertificateHelper e crea un ClientAssertionCertificate necessario per l'autenticazione.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Questo è il nuovo codice per ottenere il token di accesso. Questo codice sostituisce il metodo GetToken nell'esempio precedente. È stato indicato un nome diverso per comodità. È stato inserito tutto il codice nella classe Utils del progetto Web App per semplificarne l'utilizzo.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



L'ultima modifica del codice è nel metodo Application_Start. Innanzitutto è necessario richiamare il metodo GetCert() per caricare il ClientAssertionCertificate. Quindi si modifica il metodo di callback che viene fornito quando si crea un nuovo KeyVaultClient. Questo codice sostituisce il codice dell'esempio precedente.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Aggiungere un certificato all'app Web tramite il portale di Azure

L'aggiunta di un certificato all'app Web è un semplice processo in due passaggi. In primo luogo, accedere al portale di Azure e passare all'applicazione Web. Nelle impostazioni per l'applicazione Web, fare clic sulla voce relativa a **Impostazioni SSL**. Quando si apre, caricare il certificato creato nell'esempio precedente, KVWebApp.pfx. Assicurarsi di ricordare la password per il file pfx.

![Aggiunta di un certificato a un'applicazione Web nel portale di Azure][2]

Come ultima cosa, occorre aggiungere un'impostazione dell'applicazione all'app Web denominata WEBSITE\_LOAD\_CERTIFICATES e il valore *. Eseguendo questo passaggio viene fatto in modo che siano caricati tutti i certificati. Se si desidera caricare solo i certificati caricati, è possibile immettere un elenco delimitato da virgole delle identificazioni personali.


## <a name="clean-up-resources"></a>Pulire le risorse
Quando non sono più necessari, eliminare il servizio app, l'insieme di credenziali delle chiavi e l'applicazione di Azure AD che è stata usata per l'esercitazione.  


## <a id="next"></a>Passaggi successivi
> [!div class="nextstepaction"]
>[Riferimento all'API di gestione di Azure Key Vault](/dotnet/api/overview/azure/keyvault/management).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 