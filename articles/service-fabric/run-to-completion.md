---
title: RunToCompletion semantics in Service Fabric
description: Descrive la semantica RunToCompletion in Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431293"
---
# <a name="runtocompletion"></a>RunToCompletion (Completamento delle runToCompletion)

A partire dalla versione 7.1, Service Fabric supporta la semantica **RunToCompletion** per [contenitori][containers-introduction-link] e applicazioni [eseguibili guest.][guest-executables-introduction-link] Questa semantica consente ad applicazioni e servizi che completano un'attività e terminano, a differenza di applicazioni e servizi sempre in esecuzione.

Prima di procedere con questo articolo, è consigliabile acquisire familiarità con il modello di [applicazione di Service Fabric][application-model-link] e il modello di hosting di Service [Fabric][hosting-model-link].

> [!NOTE]
> La semantica RunToCompletion non è attualmente supportata per i servizi scritti utilizzando il modello di programmazione Reliable Services.RunToCompletion semantics are currently not supported for services written using the [Reliable Services][reliable-services-link] programming model.
 
## <a name="runtocompletion-semantics-and-specification"></a>Semantica e specifica RunToCompletion
La semantica RunToCompletion può essere specificata come **ExecutionPolicy** durante [l'importazione di ServiceManifest][application-and-service-manifests-link]. I criteri specificati vengono ereditati da tutti i CodePackage che comprendono ServiceManifest. Il frammento ApplicationManifest.xml seguente fornisce un esempio.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** consente i due attributi seguenti:ExecutionPolicy allows the following two attributes:
* **Tipo:** **RunToCompletion** è attualmente l'unico valore consentito per questo attributo.
* **Riavviare:** Questo attributo specifica i criteri di riavvio applicati ai CodePackage che comprendono ServicePackage, in caso di errore. Un CodePackage in uscita con un codice di **uscita diverso da zero** è considerato non riuscito. I valori consentiti per questo attributo sono **OnFailure** e **Mai** con **OnFailure** come impostazione predefinita.

Con i criteri di riavvio impostati **su OnFailure**, se un CodePackage ha esito negativo (codice di **uscita diverso da zero),** viene riavviato, con back-off tra errori ripetuti. Con il criterio di riavvio impostato su **Never**, se un CodePackage ha esito negativo, lo stato di distribuzione di DeployedServicePackage viene contrassegnato come **Failed** ma ad altri CodePackage è consentito continuare l'esecuzione. Se tutti i CodePackage che comprendono ServicePackage vengono eseguiti correttamente (codice di **uscita 0)**, lo stato di distribuzione di DeployedServicePackage viene contrassegnato come **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Esempio completo di utilizzo della semantica RunToCompletionComplete example using RunToCompletion semantics

Esaminiamo un esempio completo usando la semantica RunToCompletion.Let's look at a complete example using RunToCompletion semantics.

> [!IMPORTANT]
> Nell'esempio seguente si presuppone la familiarità con la creazione di [applicazioni contenitore Windows utilizzando Service Fabric e Docker][containers-getting-started-link].
>
> Questo esempio fa riferimento a mcr.microsoft.com/windows/nanoserver:1809. I contenitori di Windows Server non sono compatibili con tutte le versioni del sistema operativo host. Per altre informazioni, vedere [Compatibilità delle versioni dei contenitori di Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Il file ServiceManifest.xml seguente descrive un ServicePackage costituito da due CodePackage, che rappresentano i contenitori. *RunToCompletionCodePackage1* registra semplicemente un messaggio in **stdout** ed esce. *RunToCompletionCodePackage2* esegue il ping dell'indirizzo di loopback per un po' e quindi esce con un codice di uscita pari a **0**, **1** o **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

ApplicationManifest.xml seguente descrive un'applicazione basata sul file ServiceManifest.xml descritto in precedenza. Specifica **RunToCompletion** **ExecutionPolicy** per *WindowsRunToCompletionServicePackage* con un criterio di riavvio **OnFailure**. Dopo l'attivazione di *WindowsRunToCompletionServicePackage*, verranno avviati i codePackage costitutivi. *RunToCompletionCodePackage1* deve essere chiuso correttamente alla prima attivazione. Tuttavia, *RunToCompletionCodePackage2* può avere esito negativo (codice di **uscita diverso da zero),** nel qual caso verrà riavviato poiché il criterio di riavvio è **OnFailure**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Esecuzione di query sullo stato di distribuzione di un DeployedServicePackageQuerying deployment status of a DeployedServicePackage
Lo stato di distribuzione di un DeployedServicePackage può essere interrogato da PowerShell utilizzando [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] o da C , utilizzando [fabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync(String, Uri, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Considerazioni sull'utilizzo della semantica RunToCompletionConsiderations when using RunToCompletion semantics

I punti seguenti devono essere annotati per il supporto RunToCompletion corrente.
* Questa semantica è supportata solo per [i contenitori][containers-introduction-link] e le applicazioni [eseguibili guest.][guest-executables-introduction-link]
* Gli scenari di aggiornamento per le applicazioni con semantica RunToCompletion non sono consentiti. Gli utenti devono eliminare e ricreare tali applicazioni, se necessario.
* Gli eventi di failover possono causare la riesecuzione di CodePackage dopo il completamento, nello stesso nodo o in altri nodi del cluster. Esempi di eventi di failover sono, riavvii dei nodi e aggiornamenti di runtime di Service Fabric in un nodo.

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

