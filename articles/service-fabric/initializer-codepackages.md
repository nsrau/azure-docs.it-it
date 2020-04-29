---
title: Codepackages inizializzatore in Service Fabric
description: Descrive i pacchetti di inizializzazione dell'inizializzatore in Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430630"
---
# <a name="initializer-codepackages"></a>CodePackage dell'inizializzatore

A partire dalla versione 7,1, Service Fabric supporta i **CodePackage degli inizializzatori** per [contenitori][containers-introduction-link] e applicazioni [eseguibili Guest][guest-executables-introduction-link] . I CodePackage dell'inizializzatore offrono la possibilità di eseguire inizializzazioni nell'ambito ServicePackage prima dell'inizio dell'esecuzione di altri CodePackage. La relazione con un ServicePackage è analoga a quella di [SetupEntryPoint][setup-entry-point-link] per un CodePackage.

Prima di procedere con questo articolo, è consigliabile acquisire familiarità con il [modello di applicazione Service Fabric][application-model-link] e con il [modello di hosting Service Fabric][hosting-model-link].

> [!NOTE]
> I CodePackage degli inizializzatori non sono attualmente supportati per i servizi scritti utilizzando il modello di programmazione [Reliable Services][reliable-services-link] .
 
## <a name="semantics"></a>Semantics

È prevista l'esecuzione di un CodePackage dell'inizializzatore per **completare correttamente (codice di uscita 0)**. Un CodePackage inizializzatore non riuscito viene riavviato fino a quando non viene completato correttamente. Sono consentiti più CodePackage dell'inizializzatore e **successful completion**vengono eseguiti in modo sequenziale, in modo **sequenziale**, **in un ordine specificato** prima dell'inizio dell'esecuzione di altri CodePackage in ServicePackage.

## <a name="specifying-initializer-codepackages"></a>Specifica di codepackages dell'inizializzatore
È possibile contrassegnare un CodePackage come inizializzatore impostando l'attributo dell' **inizializzatore** su **true** in ServiceManifest. Quando sono presenti più pacchetti di inizializzazione, l'ordine di esecuzione può essere specificato tramite l'attributo **ExecOrder** . **ExecOrder** deve essere un numero intero non negativo ed è valido solo per i CodePackage degli inizializzatori. I CodePackage inizializzatori con valori inferiori di **ExecOrder** vengono eseguiti per primi. Se **ExecOrder** non è specificato per un CodePackage inizializzatore, viene utilizzato il valore predefinito 0. Non è stato specificato l'ordine di esecuzione relativo dei CodePackage dell'inizializzatore con lo stesso valore di **ExecOrder** .

Il frammento di codice ServiceManifest seguente descrive tre pacchetti di codice due dei quali sono contrassegnati come inizializzatori. Quando il ServicePackage è attivato, *InitCodePackage0* viene eseguito prima perché ha il valore minimo di **ExecOrder**. Al termine dell'operazione (codice di uscita 0) di *InitCodePackage0*, viene eseguito *InitCodePackage1* . Infine, al completamento di *InitCodePackage1*, viene eseguito *WorkloadCodePackage* .

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Esempio completo con codepackages dell'inizializzatore

Viene ora esaminato un esempio completo con i CodePackage di inizializzatori.

> [!IMPORTANT]
> Nell'esempio seguente si presuppone una certa familiarità con la creazione di [applicazioni contenitore Windows con Service Fabric e Docker][containers-getting-started-link].
>
> Questo esempio fa riferimento a mcr.microsoft.com/windows/nanoserver:1809. I contenitori di Windows Server non sono compatibili con tutte le versioni del sistema operativo host. Per altre informazioni, vedere [Compatibilità delle versioni dei contenitori di Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Il ServiceManifest. XML seguente si basa sul frammento di ServiceManifest descritto in precedenza. *InitCodePackage0*, *InitCodePackage1* e *WorkloadCodePackage* sono codepackages che rappresentano i contenitori. Al momento dell'attivazione, *InitCodePackage0* viene eseguito per primo. Registra un messaggio in un file e viene chiuso. Viene quindi eseguito *InitCodePackage1* , che registra anche un messaggio in un file e viene chiuso. Infine, il *WorkloadCodePackage* avvia l'esecuzione. Registra inoltre un messaggio in un file, restituisce il contenuto del file in **stdout** e quindi esegue il ping per sempre.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Nel file ApplicationManifest. XML seguente viene descritta un'applicazione basata su ServiceManifest. XML descritto in precedenza. Si noti che specifica lo stesso montaggio del **volume** per tutti i contenitori, ad esempio **C:\WorkspaceOnHost** viene montato in **C:\WorkspaceOnContainer** in tutti e tre i contenitori. Il risultato finale è che tutti i contenitori scrivono nello stesso file di log nell'ordine in cui sono attivati.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
Una volta che il ServicePackage è stato attivato correttamente, il contenuto di **C:\WorkspaceOnHost\log.txt** dovrebbe essere il seguente.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni correlate, vedere gli articoli seguenti.

* [Service Fabric e contenitori.][containers-introduction-link]
* [Service Fabric e gli eseguibili Guest.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

