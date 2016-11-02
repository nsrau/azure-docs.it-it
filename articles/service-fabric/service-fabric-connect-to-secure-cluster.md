<properties
   pageTitle="Autenticare l'accesso client a un cluster | Microsoft Azure"
   description="Descrive come autenticare l'accesso client a un cluster Service Fabric tramite i certificati e come proteggere le comunicazioni tra i client e un cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="connect-to-a-secure-cluster-without-aad"></a>Connettersi a un cluster sicuro senza AAD
Quando un client si connette a un nodo di un cluster Service Fabric, è possibile autenticare il client e proteggere la comunicazione stabilita mediante la sicurezza dei certificati. Questa autenticazione garantisce che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite ed eseguire attività di gestione.  La sicurezza basata su certificati deve essere stata attivata in precedenza sul cluster durante la creazione del cluster stesso.  È necessario usare almeno due certificati per proteggere il cluster, uno per il certificato del server e del cluster e un altro per l'accesso client.  È consigliabile usare anche altri certificati secondari e certificati di accesso client.  Per altre informazioni sugli scenari di sicurezza dei cluster, vedere [Sicurezza del cluster](service-fabric-cluster-security.md).

Per proteggere la comunicazione tra un client e un nodo del cluster con la sicurezza basata su certificati, è prima necessario ottenere e installare il certificato client. Il certificato può essere installato nell'archivio personale del computer locale o dell'utente corrente.  È necessaria anche l'identificazione personale del certificato del server in modo che il client possa autenticare il cluster.


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
<a id="connectsecureclustercli"></a> 
## <a name="connect-to-a-secure-cluster-using-azure-cli-without-aad"></a>Connettersi a un cluster sicuro usando l'interfaccia della riga di comando di Azure senza AAD

I comandi dell'interfaccia della riga di comando di Azure seguenti illustrano come connettersi a un cluster sicuro. I dettagli del certificato devono corrispondere a un certificato sui nodi del cluster. 
 
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

Dopo la connessione, sarà possibile eseguire altri comandi dell'interfaccia della riga di comando per interagire con il cluster. 

<a id="connectsecurecluster"></a>
## <a name="connect-to-a-secure-cluster-using-powershell-without-aad"></a>Connettersi a un cluster sicuro usando PowerShell senza AAD

Eseguire il comando di PowerShell seguente per connettersi a un cluster sicuro. I dettagli del certificato devono corrispondere a un certificato sui nodi del cluster.

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




## <a name="connect-to-a-secure-cluster-using-the-fabricclient-apis"></a>Connettersi a un cluster sicuro mediante le API FabricClient

Per altre informazioni sulle API FabricClient, vedere [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). I nodi del cluster devono avere certificati validi il cui nome comune o nome DNS nella rete SAN è contenuto nella [proprietà RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) impostata in [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Questo processo consente l'autenticazione reciproca tra il client e i nodi del cluster.

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


## <a name="next-steps"></a>Passaggi successivi

- [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](service-fabric-cluster-upgrade.md)
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introduzione al modello di integrità di Infrastruttura di servizi](service-fabric-health-introduction.md)
- [Sicurezza delle applicazioni e RunAs](service-fabric-application-runas-security.md)



<!--HONumber=Oct16_HO2-->


