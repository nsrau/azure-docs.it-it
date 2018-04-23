---
title: Eseguire uno script all'avvio di un servizio di Azure Service Fabric | Microsoft Docs
description: Informazioni su come configurare criteri per un punto di ingresso dell'installazione di un servizio di Service Fabric e su come eseguire uno script al momento dell'avvio del servizio.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: bd2bb0d05029237242b42225a2c846c78a7c6de9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2018
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Eseguire uno script di avvio del servizio come account utente o di sistema locale
Prima dell'avvio del file eseguibile di un servizio di Service Fabric può essere necessario eseguire alcune operazioni di configurazione o di installazione,  ad esempio la configurazione delle variabili di ambiente. Nel manifesto del servizio è possibile specificare di eseguire uno script prima dell'avvio dell'eseguibile del servizio stesso. La configurazione di criteri RunAs per il punto di ingresso dell'installazione del servizio consente di modificare l'account di esecuzione dell'eseguibile di installazione.  Un punto di ingresso dell'installazione separato consente di eseguire una configurazione con privilegi elevati per un breve periodo di tempo, in modo che non sia necessario eseguire con privilegi elevati il file eseguibile dell'host del servizio per periodi di tempo prolungati.

Il punto di ingresso dell'installazione, (**SetupEntryPoint** nel [manifesto del servizio](service-fabric-application-and-service-manifests.md)), è un punto di ingresso con privilegi che per impostazione predefinita viene eseguito con le stesse credenziali di Service Fabric (in genere, l'account *NetworkService*) prima di qualsiasi altro punto di ingresso. L'eseguibile specificato da **EntryPoint** è in genere l'host del servizio a esecuzione prolungata. L'eseguibile **EntryPoint** viene eseguito dopo la conclusione dell'eseguibile **SetupEntryPoint**. Il processo risultante viene monitorato e riavviato ed inizia di nuovo con **SetupEntryPoint**, se termina o si arresta in modo anomalo. 

## <a name="configure-the-service-setup-entry-point"></a>Configurare il punto di ingresso dell'installazione del servizio
Ecco un esempio semplice di manifesto per un servizio senza stato con uno script di installazione *MySetup.bat* nel servizio **SetupEntryPoint**.  **Arguments**è usato per passare argomenti allo script durante l'esecuzione.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configurare i criteri per il punto di ingresso dell'installazione del servizio
Per impostazione predefinita, il file eseguibile del punto di ingresso dell'installazione del servizio viene eseguito con le stesse credenziali di Service Fabric (in genere, l'account *NetworkService*).  Nel manifesto dell'applicazione, è possibile modificare le autorizzazioni di sicurezza per eseguire lo script di avvio con un account di sistema locale o un account amministratore.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Configurare i criteri tramite un account di sistema locale
L'esempio di manifesto dell'applicazione seguente illustra come configurare il punto di ingresso dell'installazione del servizio per l'esecuzione con un account utente amministratore (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
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

Creare prima di tutto una sezione **Principals** con un nome utente, ad esempio SetupAdminUser. L'account utente SetupAdminUser è un membro del gruppo di sistema Administrators.

Successivamente, configurare nella sezione **ServiceManifestImport** un criterio per applicare tale entità a **SetupEntryPoint**. Il criterio indica a Service Fabric che il file **MySetup.bat** deve essere eseguito con l'account SetupAdminUser (con privilegi di amministratore). Dato che *non* sono stati applicati criteri al punto di ingresso principale, il codice in **MyServiceHost.exe** viene eseguito con l'account **NetworkService** di sistema, ossia l'account predefinito con cui vengono eseguiti tutti i punti di ingresso del servizio.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configurare i criteri usando gli account di sistema locale
Spesso è preferibile eseguire lo script di avvio usando un account di sistema locale invece di un account amministratore. L'esecuzione dei criteri RunAs con un account membro del gruppo Administrators in genere non ha esito positivo. Per impostazione predefinita, infatti, per i computer è abilitato il controllo dell'account utente. In questi casi, è consigliabile eseguire SetupEntryPoint come LocalSystem invece che come utente locale aggiunto al gruppo Administrators. L'esempio seguente illustra l'impostazione di SetupEntryPoint per l'esecuzione come LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Per i cluster Linux, per eseguire un servizio o il punto di ingresso della configurazione come **root**, è possibile specificare **AccountType** come **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Eseguire uno script dal punto di ingresso dell'installazione
Aggiungere ora un script di avvio per il progetto da eseguire con privilegi di amministratore. 

In Visual Studio fare clic con il pulsante destro del mouse sul progetto del servizio e aggiungere un nuovo file denominato *MySetup.bat*.

Verificare quindi che il file *MySetup.bat* sia incluso nel pacchetto del servizio. Per impostazione predefinita, non è incluso. Selezionare il file, fare clic con il pulsante destro del mouse per visualizzare il menu di scelta rapida e scegliere **Proprietà**. Nella finestra di dialogo delle proprietà verificare che **Copia nella directory di output** sia impostato su **Copia se più recente**. Vedere lo screenshot seguente.

![CopyToOutput di Visual Studio per il file batch SetupEntryPoint][image1]

Modificare ora il file *MySetup.bat* e aggiungere i comandi seguenti per impostare una variabile di ambiente di sistema e generare un file di testo come output:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Successivamente, compilare e distribuire la soluzione a un cluster di sviluppo locale. Dopo l'avvio del servizio, come illustrato in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), è possibile verificare la corretta esecuzione del file MySetup.bat in due modi. Avviare un prompt dei comandi di PowerShell e digitare:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Annotare quindi il nome del nodo in cui il servizio è stato distribuito e avviato in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), ad esempio Node 2. Passare quindi alla cartella di lavoro dell'istanza dell'applicazione per trovare il file out.txt che mostra il valore di **TestVariable**. Se, ad esempio, il servizio è stato distribuito in Node 2, è possibile passare a questo percorso per **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Eseguire comandi PowerShell da un punto di ingresso dell'installazione
Per eseguire PowerShell dal punto **SetupEntryPoint**, è possibile eseguire **PowerShell.exe** in un file batch che punta a un file di PowerShell. Aggiungere prima di tutto un file PowerShell al progetto del servizio, ad esempio **MySetup.ps1**. Ricordarsi di impostare la proprietà *Copia se più recente* in modo che il file venga incluso anche nel pacchetto servizio. L'esempio seguente illustra un file batch di esempio per avviare un file PowerShell denominato MySetup.ps1, che imposta una variabile di ambiente di sistema denominata **TestVariable**.

MySetup.bat per avviare il file PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Nel file di PowerShell aggiungere quanto segue per impostare una variabile di ambiente di sistema:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Per impostazione predefinita, quando viene eseguito, il file batch cerca i file nella cartella dell'applicazione denominata **work**. In questo caso, quando MySetup.bat viene eseguito, il file MySetup.ps1 deve trovarsi nella stessa cartella, ovvero la cartella **code package** dell'applicazione. Per modificare questa cartella, impostare la cartella di lavoro:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Eseguire il debug di uno script di avvio localmente tramite il reindirizzamento della console
A scopo di debug può talvolta essere utile visualizzare l'output generato nella console dall'esecuzione di uno script di installazione. È possibile impostare criteri di reindirizzamento della console per il punto di ingresso dell'installazione nel manifesto del servizio, che scrive l'output in un file. L'output del file viene scritto nella cartella dell'applicazione denominata **log** nel nodo del cluster in cui l'applicazione viene distribuita ed eseguita. 

> [!WARNING]
> Non usare mai i criteri di reindirizzamento della console in un'applicazione distribuita nell'ambiente di produzione, perché ciò può incidere sul failover dell'applicazione. Usare questa opzione *solo* a scopo di sviluppo e debug locale.  
> 
> 

L'esempio di manifesto del servizio seguente illustra come impostare il reindirizzamento della console con un valore FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Se si modifica il file MySetup.ps1 per scrivere un comando **Echo**, questo verrà scritto nel file di output a scopo di debug:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Dopo avere eseguito il debug dello script, rimuovere immediatamente i criteri di reindirizzamento della console.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sulla sicurezza delle applicazioni e dei servizi](service-fabric-application-and-service-security.md)
* [Informazioni sul modello applicativo](service-fabric-application-model.md)
* [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
