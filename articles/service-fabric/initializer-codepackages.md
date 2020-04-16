---
title: Inizializzatore CodePackage in Service Fabric
description: Descrive Initializer CodePackages in Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430630"
---
# <a name="initializer-codepackages"></a>Inizializzatore CodePackage

A partire dalla versione 7.1, Service Fabric supporta i **codePackage inizializzatori** per [contenitori][containers-introduction-link] e applicazioni [eseguibili guest.][guest-executables-introduction-link] Inizializzatore CodePackages offrono l'opportunità di eseguire inizializzazioni nell'ambito ServicePackage prima dell'inizio dell'esecuzione di altri pacchetti di codice. La loro relazione con un ServicePackage è analoga a ciò che un SetupEntryPoint è per un CodePackage.Their relationship to a ServicePackage is analogo to what a [SetupEntryPoint][setup-entry-point-link] is for a CodePackage.

Prima di procedere con questo articolo, è consigliabile acquisire familiarità con il modello di [applicazione di Service Fabric][application-model-link] e il modello di hosting di Service [Fabric][hosting-model-link].

> [!NOTE]
> I codePackage dell'inizializzatore non sono attualmente supportati per i servizi scritti utilizzando il modello di programmazione [Reliable Services.][reliable-services-link]
 
## <a name="semantics"></a>Semantics

Un CodePackage dell'inizializzatore deve essere eseguito **correttamente (codice**di uscita 0) . Un CodePackage dell'inizializzatore non riuscito viene riavviato fino al completamento. Più pacchetti CodePackage Inizializzatore sono consentiti e vengono eseguiti al **completamento corretto,** **in sequenza,** **in un ordine specificato** prima che altri CodePackage nell'inizio dell'esecuzione di ServicePackage inizino l'esecuzione.

## <a name="specifying-initializer-codepackages"></a>Specifica di CodePackage inizializzatoriSpecifying Initializer CodePackages
È possibile contrassegnare un CodePackage come inizializzatore impostando il Initializer attributo true nel ServiceManifest.You can mark a CodePackage as an Initializer by setting the **Initializer** attribute to **true** in the ServiceManifest. Quando sono presenti più pacchetti di codice Inizializzatore, il relativo ordine di esecuzione può essere specificato tramite il **ExecOrder** attributo. **ExecOrder** deve essere un numero intero non negativo ed è valido solo per i codePackage dell'inizializzatore. I pacchetti Codedell'inizializzatore con valori inferiori di **ExecOrder** vengono eseguiti per primi. Se **ExecOrder** non è specificato per un Initializer CodePackage, viene utilizzato un valore predefinito pari a 0. Ordine di esecuzione relativo di Initializer CodePackages con lo stesso valore di **ExecOrder** non è specificato.

Il frammento di codice ServiceManifest seguente descrive tre CodePackages due dei quali sono contrassegnati come Initializers. Quando questo ServicePackage viene attivato, *InitCodePackage0* viene eseguito per primo poiché ha il valore più basso di **ExecOrder**. Al completamento corretto (codice di uscita 0) di *InitCodePackage0*, *InitCodePackage1* viene eseguito. Infine, al completamento di *InitCodePackage1*, *WorkloadCodePackage* viene eseguito.

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
## <a name="complete-example-using-initializer-codepackages"></a>Esempio completo di utilizzo di Initializer CodePackagesComplete example using Initializer CodePackages

Esaminiamo un esempio completo usando I pacchetti code dell'inizializzatore.

> [!IMPORTANT]
> Nell'esempio seguente si presuppone la familiarità con la creazione di [applicazioni contenitore Windows utilizzando Service Fabric e Docker][containers-getting-started-link].
>
> Questo esempio fa riferimento a mcr.microsoft.com/windows/nanoserver:1809. I contenitori di Windows Server non sono compatibili con tutte le versioni del sistema operativo host. Per altre informazioni, vedere [Compatibilità delle versioni dei contenitori di Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Il file ServiceManifest.xml seguente si basa sul frammento di codice ServiceManifest descritto in precedenza. *InitCodePackage0*, *InitCodePackage1* e *WorkloadCodePackage* sono codePackage che rappresentano contenitori. Al momento dell'attivazione, *InitCodePackage0* viene eseguito per primo. Registra un messaggio in un file e viene chiuso. Successivamente, *InitCodePackage1* viene eseguito che registra anche un messaggio in un file e chiude. Infine, il *WorkloadCodePackage* inizia l'esecuzione. Registra anche un messaggio in un file, restituisce il contenuto del file in **stdout** e quindi esegue il ping per sempre.

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

ApplicationManifest.xml seguente descrive un'applicazione basata sul file ServiceManifest.xml descritto in precedenza. Si noti che specifica lo stesso **montaggio volume** per tutti i contenitori, ad esempio C: , **WorkspaceOnHost** è montato in **C: , WorkspaceOnContainer** in tutti e tre i contenitori. L'effetto netto è che tutti i contenitori scrivono nello stesso file di log nell'ordine in cui vengono attivati.

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
Una volta che il ServicePackage è stato attivato correttamente, il contenuto di **C:**

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni correlate, vedere gli articoli seguenti.

* [Service Fabric e contenitori.][containers-introduction-link]
* [Service Fabric ed eseguibili guest.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

