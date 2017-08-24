---
title: Connettersi a un cluster sicuro di Azure Service Fabric | Microsoft Docs
description: Descrive come autenticare l'accesso client a un cluster di Service Fabric e come proteggere la comunicazione tra i client e un cluster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/01/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a24b82243cb9758b0b256c40138222357bf6e72c
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="connect-to-a-secure-cluster"></a>Connettersi a un cluster sicuro
Quando un client si connette a un nodo di un cluster di Service Fabric, è possibile autenticare il client e proteggere la comunicazione stabilita usando la sicurezza basata su certificati o Azure Active Directory (AAD). Questa autenticazione garantisce che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite ed eseguire attività di gestione.  La sicurezza basata su certificati o AAD deve essere stata abilitata in precedenza nel cluster durante la creazione del cluster stesso.  Per altre informazioni sugli scenari di sicurezza dei cluster, vedere [Sicurezza del cluster](service-fabric-cluster-security.md). Se ci si connette a un cluster protetto con certificati, [configurare il certificato client](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) nel computer che si connette al cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-cli"></a>Connettersi a un cluster protetto usando l'interfaccia della riga di comando

Esistono modi diversi per connettersi a un cluster protetto usando i comandi dell'interfaccia della riga di comando di Azure 2.0 di Service Fabric o l'interfaccia della riga di comando di XPlat.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Connettersi a un cluster sicuro con un certificato client

Quando si usa un certificato client per l'autenticazione, i dettagli del certificato devono corrispondere al certificato distribuito ai nodi del cluster. Se il certificato dispone di autorità di certificazione (CA), è necessario specificare anche le autorità di certificazione attendibili. Usare gli esempi seguenti sia per l'interfaccia della riga di comando di Azure 2.0 che per l'interfaccia della riga di comando di XPlat per la connessione.

#### <a name="xplat-cli"></a>Interfaccia della riga di comando di XPlat

Quando si usa l'interfaccia della riga di comando di XPlat, eseguire il comando seguente per la connessione:

```bash
azure servicefabric cluster connect --connection-endpoint https://ip:19080 \
--client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2
```

È possibile specificare più certificati CA usando `,` per separare i percorsi.

Se il nome comune nel certificato non corrisponde all'endpoint di connessione, è possibile usare il parametro `--strict-ssl-false` per ignorare la verifica. Ad esempio:

```bash
azure servicefabric cluster connect --connection-endpoint https://ip:19080 \
--client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

Per ignorare la verifica della CA, è possibile aggiungere il parametro ``--reject-unauthorized-false``. Ad esempio:

```bash
azure servicefabric cluster connect --connection-endpoint https://ip:19080 \
--client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

Per connettersi a un cluster protetto con un certificato autofirmato, usare il comando seguente rimuovendo sia la verifica della CA che la verifica del nome comune:

```bash
azure servicefabric cluster connect --connection-endpoint https://ip:19080 \
--client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

#### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Quando si usa l'interfaccia della riga di comando di Azure 2.0, è possibile connettersi a un cluster con il comando `az sf cluster select`.

È possibile specificare i certificati client in due modi diversi, come una coppia chiave-certificato o come un file con estensione pem singolo. Per i file `pem` protetti da password verrà chiesto automaticamente di immettere la password.

Per specificare il certificato client come file con estensione pem, specificare il percorso del file nell'argomento `--pem`. Ad esempio:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

I file con estensione pem protetti da password chiederanno la password prima di eseguire qualsiasi altro comando.

Per specificare una coppia certificato-chiave, usare gli argomenti `--cert` e `--key` per specificare i percorsi di file a ogni rispettivo file.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```
In alcuni casi i certificati usati per proteggere i cluster di test o di sviluppo non superano la convalida. Per ignorare la verifica del certificato, specificare l'opzione `--no-verify`. Ad esempio:

> [!WARNING]
> Non usare l'opzione `no-verify` quando ci si connette a cluster di Service Fabric di produzione.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

È anche possibile specificare i percorsi a directory di certificati CA attendibili o a certificati individuali. Per specificare questi percorsi, usare l'argomento `--ca`. Ad esempio:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Dopo la connessione, sarà possibile [eseguire altri comandi dell'interfaccia della riga di comando](service-fabric-azure-cli.md) per interagire con il cluster.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Connessione a un cluster mediante PowerShell
Prima di eseguire operazioni su un cluster tramite PowerShell, stabilire una connessione al cluster. La connessione al cluster verrà usata per tutti i successivi comandi nella specifica sessione di PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Connettersi a un cluster non sicuro

Per connettersi a un cluster non sicuro, specificare l'indirizzo dell'endpoint del cluster nel comando **Connect-ServiceFabricCluster**:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Connettersi a un cluster sicuro con Azure Active Directory

Per connettersi a un cluster sicuro che usa Azure Active Directory per autorizzare l'accesso di amministratore al cluster, specificare l'identificazione personale del certificato del cluster e usare il flag *AzureActiveDirectory*.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Connettersi a un cluster sicuro con un certificato client
Per connettersi a un cluster sicuro che usa certificati client per autorizzare l'accesso di amministratore, eseguire il comando di PowerShell seguente. Specificare l'identificazione personale del certificato del cluster e l'identificazione personale del certificato client a cui sono state concesse le autorizzazioni per la gestione del cluster. I dettagli del certificato devono corrispondere a un certificato sui nodi del cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* è l'identificazione personale del certificato del server installato nei nodi del cluster. *FindValue* è l'identificazione personale del certificato client di amministrazione.
Dopo l'immissione dei parametri, il comando sarà simile all'esempio seguente: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Connettersi a un cluster sicuro con Windows Active Directory
Se il cluster autonomo viene distribuito usando la protezione di Active Directory, connettersi al cluster aggiungendo l'opzione "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Connettersi a un cluster mediante le API FabricClient
Service Fabric SDK fornisce la classe [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) per la gestione del cluster. Per usare le API di FabricClient, è necessario disporre del pacchetto NuGet Microsoft.ServiceFabric.

### <a name="connect-to-an-unsecure-cluster"></a>Connettersi a un cluster non sicuro

Per connettersi a un cluster remoto non protetto, creare un'istanza di FabricClient e specificare l'indirizzo del cluster:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Per il codice in esecuzione in un cluster, ad esempio in un servizio Reliable Services, creare un'istanza di FabricClient *senza* specificare l'indirizzo del cluster. FabricClient si connette al gateway di gestione locale nel nodo in cui il codice è attualmente in esecuzione, evitando un hop di rete aggiuntivo.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Connettersi a un cluster sicuro con un certificato client

I nodi del cluster devono avere certificati validi il cui nome comune o nome DNS nella rete SAN è contenuto nella [proprietà RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames) impostata in [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Questo processo consente l'autenticazione reciproca tra il client e i nodi del cluster.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Connessione interattiva a un cluster protetto con Azure Active Directory

Nell'esempio seguente viene utilizzata Azure Active Directory per l'identità del client e il certificato del server per l'identità del server.

Al momento della connessione al cluster viene visualizzata automaticamente una finestra di dialogo per l'accesso interattivo.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Connessione non interattiva a un cluster protetto con Azure Active Directory

L'esempio è basato su Microsoft.IdentityModel.Clients.ActiveDirectory, Versione: 2.19.208020213.

Per ulteriori informazioni sull'acquisizione di token di AAD, vedere [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Connessione a un cluster protetto senza conoscere a priori i metadati tramite Azure Active Directory

L'esempio seguente usa l'acquisizione dei token non interattiva, ma lo stesso approccio consente di creare un'esperienza di acquisizione dei token interattiva personalizzata. I metadati di Azure Active Directory necessari per l'acquisizione dei token vengono letti dalla configurazione del cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Connettersi a un cluster sicuro usando Service Fabric Explorer
Per raggiungere [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) per un determinato cluster, inserire nel browser l'indirizzo seguente:

`http://<your-cluster-endpoint>:19080/Explorer`

L'URL completo è disponibile anche nel riquadro essentials del cluster del portale di Azure.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Connettersi a un cluster sicuro con Azure Active Directory

Per connettersi a un cluster protetto con AAD, inserire nel browser l'indirizzo seguente:

`https://<your-cluster-endpoint>:19080/Explorer`

Viene automaticamente richiesto di accedere con AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Connettersi a un cluster sicuro con un certificato client

Per connettersi a un cluster protetto con certificati, inserire nel browser l'indirizzo seguente:

`https://<your-cluster-endpoint>:19080/Explorer`

Viene automaticamente richiesto di selezionare un certificato del client.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configurare un certificato client nel computer remoto
È necessario usare almeno due certificati per proteggere il cluster, uno per il certificato del server e del cluster e un altro per l'accesso client.  È consigliabile usare anche altri certificati secondari e certificati di accesso client.  Per proteggere la comunicazione tra un client e un nodo del cluster con la sicurezza basata su certificati, è prima necessario ottenere e installare il certificato client. Il certificato può essere installato nell'archivio personale del computer locale o dell'utente corrente.  È necessaria anche l'identificazione personale del certificato del server in modo che il client possa autenticare il cluster.

Per configurare il certificato del client nel computer che si userà per accedere al cluster, eseguire il cmdlet PowerShell seguente.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Se si tratta di un certificato autofirmato, è necessario importarlo nell'archivio "Persone attendibili" del computer prima di poterlo usare per la connessione a un cluster sicuro.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>Passaggi successivi
* [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](service-fabric-cluster-upgrade.md)
* [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
* [Introduzione al modello di integrità di Infrastruttura di servizi](service-fabric-health-introduction.md)
* [Sicurezza delle applicazioni e RunAs](service-fabric-application-runas-security.md)

## <a name="related-articles"></a>Articoli correlati

* [Introduzione a Service Fabric e all'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introduzione all'interfaccia della riga di comando di XPlat per Service Fabric](service-fabric-azure-cli.md)

