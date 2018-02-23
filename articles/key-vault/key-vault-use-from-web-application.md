---
title: Usare l'insieme di credenziali delle chiavi di Azure da un'applicazione Web | Microsoft Docs
description: "In questa esercitazione si apprenderà a usare l'insieme di credenziali chiave di Azure da un'applicazione Web."
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 09/15/2017
ms.author: adhurwit
ms.openlocfilehash: 107be940b4c105056c63f793fb0111b03469bf66
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Usare l'insieme di credenziali chiave di Azure da un'applicazione Web

## <a name="introduction"></a>Introduzione

In questa esercitazione si apprenderà a usare l'insieme di credenziali chiave di Azure da un'applicazione Web in Azure. Viene illustrato il processo di accesso a un segreto da un insieme di credenziali chiave di Azure, in modo da poterlo usare nell'applicazione Web.

**Tempo previsto per il completamento:** 15 minuti

Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un URI di un segreto in un insieme di credenziali chiave di Azure.
* Un ID client e un segreto client per un'applicazione Web registrata con Azure Active Directory che abbia accesso all'insieme di credenziali chiave.
* Un'applicazione Web. Verranno mostrati i passaggi per un'applicazione ASP.NET MVC distribuita in Azure come app Web.

>[!IMPORTANT]
>* Questo esempio dipende da una modalità meno recente di eseguire il provisioning manuale delle identità AAD. Attualmente esiste una nuova funzionalità in anteprima, denominata [Managed Service Identity (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), che consente di eseguire automaticamente il provisioning di identità AAD. Per altri dettagli, fare riferimento all'esempio seguente in [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/).

> [!NOTE]
>* Per questa esercitazione è essenziale aver completato i passaggi elencati in [Introduzione all'insieme di credenziali chiave di Azure](key-vault-get-started.md) in modo da disporre dell'URI di un segreto e dell'ID client e del segreto client per un'applicazione Web.


L'applicazione Web che accederà all'insieme di credenziali chiave è quella registrata in Azure Active Directory e a cui è stato concesso l'accesso all'insieme di credenziali chiave. Se non è così, tornare a Registrare un'applicazione nell'esercitazione introduttiva e ripetere i passaggi elencati.

Questa esercitazione è destinata agli sviluppatori Web che conoscono le nozioni di base della creazione di applicazioni Web in Azure. Per altre informazioni sulle app Web di Azure, vedere [Panoramica delle app Web](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Aggiungere i pacchetti NuGet

Sono due i pacchetti che devono essere installati nell'applicazione Web.

* Active Directory Authentication Library: contiene i metodi per interagire con Azure Active Directory e gestire l'identità utente
* Azure Key Vault Library: contiene i metodi per interagire con l'insieme di credenziali chiave di Azure

È possibile installare entrambi i pacchetti con la Console di Gestione pacchetti usando il comando Install-Package.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modificare Web.Config

Sono tre le impostazioni che devono essere aggiunte al file web.config, come indicato di seguito.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Se non si ospiterà l'applicazione come app Web di Azure, è consigliabile aggiungere i valori effettivi di ID client, segreto client e URI segreto a web.config. In caso contrario, lasciare questi valori fittizi perché i valori effettivi verranno aggiunti nel portale di Azure per un ulteriore livello di sicurezza.

## <a id="gettoken"></a>Aggiungere il metodo per ottenere un token di accesso

Per usare l'API dell'insieme di credenziali chiave, è necessario un token di accesso. Il client dell'insieme di credenziali chiave gestisce le chiamate all'API dell'insieme di credenziali chiave, ma è necessario fornirgli una funzione che ottenga il token di accesso.  

Di seguito è riportato il codice per ottenere un accesso token da Azure Active Directory. Questo codice può far accedere ovunque nell'applicazione. È possibile aggiungere una classe Utils o EncryptionHelper.  

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
```

> [!NOTE]
>* Attualmente, la nuova funzionalità di identità del servizio gestito è il modo più semplice per l'autenticazione. Per altre informazioni, visitare il collegamento seguente all'esempio che usa [Key Vault con l'identità del servizio gestito in un'applicazione in .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) e la relativa [esercitazione con l'identità del servizio gestito con il servizio app e le funzioni](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). 
>* L'uso di un ID client e di un segreto client è un altro modo per autenticare un'applicazione Azure AD. Utilizzarlo nell'applicazione Web consente una separazione dei compiti e maggiore controllo sulla gestione delle chiavi. Ma si basa sull'inserimento del client secret nelle impostazioni di configurazione, che per alcuni può essere rischioso quanto inserire la chiave privata che si desidera proteggere nelle impostazioni di configurazione. Per una discussione su come utilizzare un ID Client e un certificato anziché un ID Client e il client secret per autenticare l'applicazione di Azure AD, vedere di seguito.

## <a id="appstart"></a>Recuperare il segreto all'avvio dell'applicazione

A questo punto è necessario il codice per chiamare l'API dell'insieme di credenziali chiave e recuperare il segreto. Purché venga chiamato prima che sia necessario usarlo, è possibile inserire il codice seguente in qualsiasi punto. In questo caso, viene inserito nell'evento Application Start in Global.asax in modo che venga eseguito una volta all'avvio e renda il segreto disponibile per l'applicazione.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Aggiungere le impostazioni dell'app nel portale di Azure (facoltativo)

Se si dispone di un'app Web di Azure, è ora possibile aggiungere i valori effettivi per AppSettings nel portale di Azure. In questo modo, i valori effettivi non saranno in web.config, ma saranno protetti tramite il portale in cui si dispone di funzionalità di controllo di accesso separate. Questi valori verranno sostituiti per i valori immessi in web.config. Assicurarsi che i nomi siano uguali.

![Impostazioni dell'applicazione visualizzate nel portale di Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autenticazione con un certificato anziché un client secret

È possibile autenticare un'applicazione di Azure AD utilizzando un ID Client e un certificato anziché un ID Client e un client secret. Di seguito sono indicati i passaggi per utilizzare un certificato in un'applicazione Web di Azure:

1. Ottenere o creare un certificato
2. Associare il certificato a un'applicazione di Azure AD
3. Aggiungere codice all'applicazione Web per utilizzare il certificato
4. Aggiungere un certificato a un'applicazione Web

### <a name="get-or-create-a-certificate"></a>Ottenere o creare un certificato

Per i nostri scopi rendiamo un certificato di prova. Ecco un paio di comandi che è possibile utilizzare in un prompt dei comandi sviluppatore per creare un certificato. Cambiare la directory in cui si vuole creare i file del certificato.  Inoltre, per la data di inizio e di fine del certificato usare la data corrente più 1 anno.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2017 -e 07/31/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Prendere nota della data finale e della password per il formato con estensione pfx (in questo esempio: 07/31/2017 e test123). Saranno necessarie più avanti.

Per ulteriori informazioni sulla creazione di un certificato di prova, vedere [Procedura: creare il proprio certificato di prova](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Associare il certificato a un'applicazione di Azure AD

Ora che si dispone di un certificato, è necessario associarlo a un'applicazione di Azure AD. Attualmente il portale di Azure non supporta questo flusso di lavoro. Questa operazione può essere completata in PowerShell. Eseguire questi comandi per associare il certificato con l'applicazione Azure AD:

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Dopo avere eseguito questi comandi, è possibile visualizzare l'applicazione in Azure AD. Durante la ricerca assicurarsi di selezionare "Applicazioni di proprietà dell'azienda" anziché "Applicazioni usate dall'azienda" nella finestra di ricerca.

Per ulteriori informazioni sugli oggetti dell'applicazione di Azure AD e sugli oggetti ServicePrincipal, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md).

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Aggiungere codice all'applicazione Web per utilizzare il certificato

Ora il codice verrà aggiunto all'applicazione Web per accedere al certificato e utilizzarlo per l'autenticazione.

Prima di tutto viene il codice per accedere al certificato.

```cs
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

Si noti che StoreLocation è CurrentUser anziché LocalMachine. Si sta specificando 'false' per il metodo Find, poiché si sta utilizzando un certificato di prova.

Poi viene il codice che utilizza il CertificateHelper e crea un ClientAssertionCertificate necessario per l'autenticazione.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Questo è il nuovo codice per ottenere il token di accesso. Sostituisce il metodo GetToken nell'esempio precedente. È stato indicato un nome diverso per comodità.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

È stato inserito tutto il codice nella classe Utils del progetto Web App per semplificarne l'utilizzo.

L'ultima modifica del codice è nel metodo Application_Start. Innanzitutto è necessario richiamare il metodo GetCert() per caricare il ClientAssertionCertificate. Quindi si modifica il metodo di callback che viene fornito quando si crea un nuovo KeyVaultClient. Si noti che questo sostituisce il codice utilizzato nell'esempio precedente.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Aggiungere un certificato all'app Web tramite il portale di Azure

L'aggiunta di un certificato all'app Web è un semplice processo in due passaggi. In primo luogo, accedere al portale di Azure e passare all'applicazione Web. Nel pannello Impostazioni per l'applicazione Web, fare clic sulla voce relativa a "domini personalizzati e SSL". Nel pannello che si apre sarà possibile caricare il certificato creato nell'esempio precedente, KVWebApp.pfx; assicurarsi di ricordare la password per il pfx.

![Aggiunta di un certificato a un'applicazione Web nel portale di Azure][2]

Come ultima cosa, occorre aggiungere un'impostazione dell'applicazione all'app Web denominata WEBSITE\_LOAD\_CERTIFICATES e il valore *. Ciò garantisce che siano caricati tutti i certificati. Se si desidera caricare solo i certificati caricati, è possibile immettere un elenco delimitato da virgole delle identificazioni personali.

Per ulteriori informazioni sull'aggiunta di un certificato a un'applicazione Web, vedere [Utilizzo di certificati nelle applicazioni di siti Web di Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Aggiungere un certificato all'insieme di credenziali delle chiavi come segreto

Invece di caricare il certificato direttamente nel servizio App Web, è possibile archiviarlo nell'insieme di credenziali delle chiavi come segreto e distribuirlo da questa posizione. Si tratta di un processo in due fasi descritto nel post di blog seguente, [Deploying Azure Web App Certificate through Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Passaggi successivi

Per i riferimenti alla programmazione, vedere [Informazioni di riferimento sull'API client C# dell'insieme di credenziali chiave di Azure](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
