---
title: Aggiungere un endpoint HTTPS a un'applicazione Service Fabric di Azure usando Kestrel | Microsoft Docs
description: Questa esercitazione illustra come aggiungere un endpoint HTTPS a un servizio Web front-end ASP.NET Core usando Kestrel e distribuire l'applicazione in un cluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 48dd09bf70e99adc250027df872266bea39a786b
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302415"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>Esercitazione: Aggiungere un endpoint HTTPS a un servizio front-end API Web ASP.NET Core usando Kestrel

Questa è la terza di una serie di esercitazioni.  Verrà illustrato come abilitare HTTPS in un servizio ASP.NET Core eseguito in Service Fabric. Al termine, si avrà un'applicazione di voto con un front-end Web ASP.NET Core abilitato per HTTPS in ascolto sulla porta 443. Se non si vuole creare manualmente l'applicazione di voto come illustrato in [Creare un'applicazione di Service Fabric .NET](service-fabric-tutorial-deploy-app-to-party-cluster.md), è possibile [scaricare il codice sorgente](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) per l'applicazione completa.

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Definire un endpoint HTTPS nel servizio
> * Configurare Kestrel per l'uso di HTTPS
> * Installare il certificato SSL nei nodi del cluster remoto
> * Concedere a NETWORK SERVICE l'accesso alla chiave privata del certificato
> * Aprire la porta 443 nel servizio di bilanciamento del carico di Azure
> * Distribuire l'applicazione in un cluster remoto

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'applicazione di Service Fabric .NET](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Distribuire l'applicazione in un cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Aggiungere un endpoint HTTPS a un servizio front-end ASP.NET Core
> * [Configurare l'integrazione continua e la distribuzione continua con Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-monitoring-aspnet.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installare Visual Studio 2019](https://www.visualstudio.com/) versione 15.5 o successiva con i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
* [Installare Service Fabric SDK](service-fabric-get-started.md)

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>Ottenere un certificato o creare un certificato di sviluppo autofirmato

Per le applicazioni di produzione, usare un certificato di un'[autorità di certificazione (CA)](https://wikipedia.org/wiki/Certificate_authority). Per finalità di sviluppo e test, è possibile creare e usare un certificato autofirmato. Service Fabric SDK offre lo script *CertSetup.ps1*, che crea un certificato autofirmato e lo importa nell'archivio certificati `Cert:\LocalMachine\My`. Aprire il prompt dei comandi come amministratore ed eseguire questo comando per creare un certificato con soggetto "CN=mytestcert":

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

Se si ha già un file PFX di certificato, eseguire questo comando per importare il certificato nell'archivio certificati `Cert:\LocalMachine\My`:

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>Definire un endpoint HTTPS nel manifesto del servizio

Avviare Visual Studio come **amministratore** e aprire la soluzione Voting. In Esplora soluzioni aprire *VotingWeb/PackageRoot/ServiceManifest.xml*. Il manifesto del servizio definisce gli endpoint di servizio.  Trovare la sezione **Endpoints** e modificare l'endpoint "ServiceEndpoint" esistente.  Modificare il nome in "EndpointHttps" e impostare il protocollo su *https*, il tipo su *Input* e la porta su *443*.  Salvare le modifiche.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>Configurare Kestrel per l'uso di HTTPS

In Esplora soluzioni aprire il file *VotingWeb/VotingWeb.cs*.  Configurare Kestrel per usare HTTPS e cercare il certificato nell'archivio `Cert:\LocalMachine\My`. Aggiungere le istruzioni using seguenti:

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

Aggiornare `ServiceInstanceListener` per l'uso del nuovo endpoint *EndpointHttps* e l'ascolto sulla porta 443. Quando si configura l'host Web per usare un server Kestrel, è necessario configurare Kestrel per l'ascolto di indirizzi IPv6 su tutte le interfacce di rete: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`.

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Aggiungere anche il metodo seguente per consentire a Kestrel di trovare il certificato nell'archivio `Cert:\LocalMachine\My` usando il soggetto.  

Sostituire "&lt;your_CN_value&gt;" con "mytestcert" se si è creato un certificato autofirmato con il comando di PowerShell precedente oppure usare il nome comune del certificato.

```csharp
private X509Certificate2 GetCertificateFromStore()
{
    var store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var currentCerts = certCollection.Find(X509FindType.FindBySubjectDistinguishedName, "CN=<your_CN_value>", false);
        return currentCerts.Count == 0 ? null : currentCerts[0];
    }
    finally
    {
        store.Close();
    }
}
```

## <a name="give-network-service-access-to-the-certificates-private-key"></a>Concedere a NETWORK SERVICE l'accesso alla chiave privata del certificato

In un passaggio precedente si è importato il certificato nell'archivio `Cert:\LocalMachine\My` nel computer di sviluppo.  Ora assegnare in modo esplicito all'account che esegue il servizio (per impostazione predefinita, NETWORK SERVICE) l'accesso alla chiave privata del certificato. È possibile eseguire questo passaggio manualmente (con lo strumento certlm.msc), ma è preferibile eseguire automaticamente uno script di PowerShell [configurando uno script di avvio](service-fabric-run-script-at-service-startup.md) nel nodo **SetupEntryPoint** del manifesto del servizio.

### <a name="configure-the-service-setup-entry-point"></a>Configurare il punto di ingresso dell'installazione del servizio

In Esplora soluzioni aprire *VotingWeb/PackageRoot/ServiceManifest.xml*.  Nella sezione **CodePackage** aggiungere il nodo **SetupEntryPoint** e quindi un nodo **ExeHost**.  In **ExeHost** impostare **Program** su "Setup.bat" e **WorkingFolder** su "CodePackage".  Quando verrà avviato il servizio VotingWeb, verrà eseguito lo script Setup.bat nella cartella CodePackage prima dell'avvio di VotingWeb.exe.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>Aggiungere gli script batch e di configurazione di PowerShell

Per eseguire PowerShell dal punto **SetupEntryPoint**, è possibile eseguire PowerShell.exe in un file batch che punta a un file di PowerShell. Per prima cosa, aggiungere il file batch al progetto del servizio.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **VotingWeb**, scegliere **Aggiungi**->**Nuovo elemento** e aggiungere un nuovo file denominato "Setup.bat".  Modificare il file *Setup.bat* e aggiungere il comando seguente:

```bat
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

Modificare le proprietà del file *Setup.bat* impostando **Copia nella directory di output** su "Copia se più recente".

![Impostare le proprietà del file][image1]

In Esplora soluzioni fare clic con il pulsante destro del mouse su **VotingWeb**, scegliere **Aggiungi**->**Nuovo elemento** e aggiungere un nuovo file denominato "SetCertAccess.ps1".  Modificare il file *SetCertAccess.ps1* e aggiungere lo script seguente:

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

Modificare le proprietà del file *SetCertAccess.ps1* impostando **Copia nella directory di output** su "Copia se più recente".

### <a name="run-the-setup-script-as-a-local-administrator"></a>Eseguire lo script di configurazione come amministratore locale

Per impostazione predefinita, l'eseguibile del punto di ingresso di configurazione del servizio viene eseguito con le stesse credenziali di Service Fabric (in genere, l'account NetworkService). *SetCertAccess.ps1* richiede privilegi di amministratore. Nel manifesto dell'applicazione, è possibile modificare le autorizzazioni di sicurezza per eseguire lo script di avvio con un account amministratore locale.

In Esplora soluzioni aprire *Voting/ApplicationPackageRoot/ApplicationManifest.xml*. Per prima cosa, creare una sezione **Principals** e aggiungere un nuovo utente, ad esempio "SetupAdminUser". Aggiungere l'account utente SetupAdminUser al gruppo di sistema Administrators.
Nella sezione **ServiceManifestImport** di VotingWebPkg configurare quindi un criterio **RunAsPolicy** per applicare l'entità di sicurezza SetupAdminUser al punto di ingresso di configurazione. Il criterio indica a Service Fabric che il file Setup.bat verrà eseguito con l'account SetupAdminUser, con privilegi di amministratore.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale

In Esplora soluzioni selezionare l'applicazione **Voting** e impostare la proprietà **URL applicazione** su "https:\//localhost:443".

Salvare tutti i file e premere F5 per eseguire l'applicazione in locale.  Al termine della distribuzione dell'applicazione, l'URL https:\//localhost:443 verrà aperto in un Web browser. Se si usa un certificato autofirmato, verrà visualizzato un avviso che informa che la sicurezza del sito Web non è considerata attendibile dal PC.  Continuare e passare alla pagina Web.

![Applicazione di voto][image2]

## <a name="install-certificate-on-cluster-nodes"></a>Installare il certificato nei nodi del cluster

Prima di distribuire l'applicazione in Azure, installare il certificato nell'archivio `Cert:\LocalMachine\My` di tutti i nodi del cluster remoto.  I servizi possono essere spostati in nodi diversi del cluster.  All'avvio del servizio Web front-end in un nodo del cluster, lo script di avvio cercherà il certificato e configurerà le autorizzazioni di accesso.

Per prima cosa, esportare il certificato in un file PFX. Aprire l'applicazione certlm.msc e passare a **Personale**>**Certificati**.  Fare clic con il pulsante destro del mouse sul certificato di *mytestcert* e scegliere **Tutte le attività**>**Esporta**.

![Esportare il certificato][image4]

Nella procedura guidata di esportazione scegliere **Sì, esporta la chiave privata** e quindi il formato PFX (Personal Information Exchange).  Esportare il file in *C:\Users\sfuser\votingappcert.pfx*.

Installare quindi il certificato nel cluster remoto con il cmdlet [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate).

> [!Warning]
> Per le applicazioni di sviluppo e test è sufficiente un certificato autofirmato. Per le applicazioni di produzione, usare invece un certificato di un'[autorità di certificazione (CA)](https://wikipedia.org/wiki/Certificate_authority).

```powershell
Connect-AzAccount

$vaultname="sftestvault"
$certname="VotingAppPFX"
$certpw="!Password321#"
$groupname="voting_RG"
$clustername = "votinghttps"
$ExistingPfxFilePath="C:\Users\sfuser\votingappcert.pfx"

$appcertpwd = ConvertTo-SecureString -String $certpw -AsPlainText -Force

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $certpw

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $certpw
   } | ConvertTo-Json

$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
Write-Host "Writing secret to $certname in vault $vaultname"
$secret = Set-AzureKeyVaultSecret -VaultName $vaultname -Name $certname -SecretValue $secretValue

# Add a certificate to all the VMs in the cluster.
Add-AzServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $secret.Id -Verbose
```

## <a name="open-port-443-in-the-azure-load-balancer"></a>Aprire la porta 443 nel servizio di bilanciamento del carico di Azure

Aprire la porta 443 nel servizio di bilanciamento del carico, se non è già aperta.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzLoadBalancer
```

## <a name="deploy-the-application-to-azure"></a>Distribuzione dell'applicazione in Azure

Salvare tutti i file, passare da Debug a Release e premere F6 per ricompilare.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Voting** e scegliere **Pubblica**. Selezionare l'endpoint di connessione del cluster creato in [Distribuire un'applicazione in un cluster](service-fabric-tutorial-deploy-app-to-party-cluster.md) oppure selezionare un altro cluster.  Fare clic su **Pubblica** per pubblicare l'applicazione nel cluster remoto.

Al termine della distribuzione dell'applicazione, aprire un Web browser e passare a [https://mycluster.region.cloudapp.azure.com:443](https://mycluster.region.cloudapp.azure.com:443), aggiornando l'URL con l'endpoint di connessione del cluster. Se si usa un certificato autofirmato, verrà visualizzato un avviso che informa che la sicurezza del sito Web non è considerata attendibile dal PC.  Continuare e passare alla pagina Web.

![Applicazione di voto][image3]

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Definire un endpoint HTTPS nel servizio
> * Configurare Kestrel per l'uso di HTTPS
> * Installare il certificato SSL nei nodi del cluster remoto
> * Concedere a NETWORK SERVICE l'accesso alla chiave privata del certificato
> * Aprire la porta 443 nel servizio di bilanciamento del carico di Azure
> * Distribuire l'applicazione in un cluster remoto

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Configurare l'integrazione continua e la distribuzione continua con Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
