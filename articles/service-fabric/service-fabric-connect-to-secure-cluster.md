---
title: Autenticare l&quot;accesso client a un cluster | Documentazione Microsoft
description: Descrive come autenticare l&quot;accesso client a un cluster di Service Fabric e come proteggere la comunicazione tra i client e un cluster.
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
ms.date: 11/11/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 65775053918e12ef8881f417dacc0a63f080d093
ms.openlocfilehash: 6de98012e768abc7f8450e97648444a74474b5e9


---
# <a name="connect-to-a-secure-cluster"></a>Connettersi a un cluster sicuro
Quando un client si connette a un nodo di un cluster di Service Fabric, è possibile autenticare il client e proteggere la comunicazione stabilita usando la sicurezza basata su certificati o Azure Active Directory (AAD). Questa autenticazione garantisce che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite ed eseguire attività di gestione.  La sicurezza basata su certificati o AAD deve essere stata abilitata in precedenza nel cluster durante la creazione del cluster stesso.  Per altre informazioni sugli scenari di sicurezza dei cluster, vedere [Sicurezza del cluster](service-fabric-cluster-security.md). Se ci si connette a un cluster protetto con certificati, [configurare il certificato client](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) nel computer che si connette al cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-cli"></a>Connettersi a un cluster sicuro usando l'interfaccia della riga di comando di Azure
I comandi dell'interfaccia della riga di comando di Azure seguenti illustrano come connettersi a un cluster sicuro. 

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Connettersi a un cluster sicuro con un certificato client
I dettagli del certificato devono corrispondere a un certificato sui nodi del cluster. 

Se il certificato ha autorità di certificazione (CA), è necessario aggiungere il parametro `--ca-cert-path` come illustrato nell'esempio seguente: 

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Se sono presenti più CA, usare la virgola come delimitatore. 

Se il nome comune nel certificato non corrisponde all'endpoint di connessione, è possibile usare il parametro `--strict-ssl-false` per ignorare la verifica. 

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

Per ignorare la verifica della CA, è possibile aggiungere il parametro ``--reject-unauthorized-false`` come illustrato nel comando seguente:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

Per connettersi a un cluster protetto con un certificato autofirmato, usare il comando seguente rimuovendo sia la verifica della CA che la verifica del nome comune:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

Dopo la connessione, sarà possibile [eseguire altri comandi dell'interfaccia della riga di comando](service-fabric-azure-cli.md) per interagire con il cluster. 

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-secure-cluster-using-powershell"></a>Connettersi a un cluster sicuro mediante PowerShell
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
Per connettersi a un cluster sicuro che usa certificati client per autorizzare l'accesso di amministratore, eseguire il comando di PowerShell seguente. Specificare l'identificazione personale del certificato del cluster nonché l'identificazione personale del certificato client a cui sono state concesse le autorizzazioni per la gestione del cluster. I dettagli del certificato devono corrispondere a un certificato sui nodi del cluster.

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


<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-secure-cluster-using-the-fabricclient-apis"></a>Connettersi a un cluster sicuro mediante le API FabricClient
Service Fabric SDK fornisce la classe [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) per la gestione del cluster. 

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

I nodi del cluster devono avere certificati validi il cui nome comune o nome DNS nella rete SAN è contenuto nella [proprietà RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) impostata in [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Questo processo consente l'autenticazione reciproca tra il client e i nodi del cluster.

```csharp
string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
FabricClient fc = new FabricClient(xc, connection);
Task<bool> t = fc.PropertyManager.NameExistsAsync(new Uri("fabric:/any"));
try
{
    bool result = t.Result;
    Console.WriteLine("Cluster is connected");
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();

    // Client certificate
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;

    // Server certificate
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);

    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Connettersi a un cluster sicuro con Azure Active Directory

Questo processo abilita Azure Active Directory per l'identità del client e il certificato del server per l'identità del server.

Per usare la modalità interattiva, con visualizzazione di una finestra di dialogo di accesso interattiva di AAD:

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

FabricClient fc = new FabricClient(
    claimsCredentials,
    connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

Per usare la modalità non interattiva, senza intervento dell'utente, usare il codice seguente.

L'esempio è basato su Microsoft.IdentityModel.Clients.ActiveDirectory versione 2.19.208020213.

Per altre informazioni, ad esempio su come acquisire il token, vedere lo [spazio dei nomi Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "c15cfcea-02c1-40dc-8466-fbd0ee0b05d2";
string clientApplicationId = "118473c2-7619-46e3-a8e4-6da8d5f56e12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob"
    );

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";
ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

FabricClient fc = new FabricClient(
   claimsCredentials,
   connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
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
    AuthenticationContext authContext = new AuthenticationContext(authority);

    string token = "";
    try
    {
        var authResult = authContext.AcquireToken(
            resource,
            clientId,
            new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
        token = authResult.AccessToken;
    }
    catch (AdalException ex)
    {
        Console.WriteLine("Get AccessToken failed: {0}", ex.Message);
    }

    return token;
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




<!--HONumber=Dec16_HO2-->


