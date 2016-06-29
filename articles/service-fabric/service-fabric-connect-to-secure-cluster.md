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
   ms.date="06/01/2016"
   ms.author="ryanwi"/>

# Connettersi a un cluster sicuro
Quando un client si connette a un nodo di un cluster Service Fabric, è possibile autenticare il client e proteggere la comunicazione stabilita mediante la sicurezza dei certificati. Ciò garantisce che solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite ed eseguire attività di gestione. La sicurezza basata su certificati deve essere stata attivata in precedenza sul cluster durante la creazione del cluster stesso. Per altre informazioni sugli scenari di sicurezza dei cluster, vedere [Sicurezza del cluster](service-fabric-cluster-security.md).

Per proteggere le comunicazioni tra un client e un nodo del cluster mediante la sicurezza basata su certificati, è innanzitutto necessario ottenere e installare il certificato client nell'archivio personale nel computer locale o nell'archivio personale per l'utente corrente.

Per configurare il certificato nel computer che si userà per accedere al cluster, eseguire il seguente cmdlet PowerShell.

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

<a id="connectsecurecluster"></a>
## Connettersi a un cluster sicuro mediante PowerShell

Eseguire il comando di PowerShell seguente per connettersi a un cluster sicuro. I dettagli del certificato devono corrispondere a un certificato sui nodi del cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Ad esempio, il comando di PowerShell precedente sarà simile a quello riportato di seguito.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
          -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
          -StoreLocation CurrentUser -StoreName My
```

## Connettersi a un cluster sicuro mediante le API FabricClient
Il [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) seguente. I nodi del cluster devono disporre di certificati validi il cui nome comune o nome DNS nella rete SAN venga visualizzato nella [proprietà RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx) impostata su [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Questo consente l'autenticazione reciproca tra il client e il nodo del cluster.

```csharp
string thumb = "C179E609BBF0B227844342535142306F3913D6ED";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(thumb, CommonName);
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

static X509Credentials GetCredentials(string thumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```


## Passaggi successivi

- [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](service-fabric-cluster-upgrade.md)
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introduzione al modello di integrità di Infrastruttura di servizi](service-fabric-health-introduction.md)
- [Sicurezza delle applicazioni e RunAs](service-fabric-application-runas-security.md)

<!---HONumber=AcomDC_0615_2016-->