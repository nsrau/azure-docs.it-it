---
title: Semantica di RunToCompletion in Service Fabric
description: Descrive la semantica di RunToCompletion in Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000267"
---
# <a name="runtocompletion"></a>RunToCompletion

A partire dalla versione 7,1, Service Fabric supporta la semantica **RunToCompletion** per [contenitori][containers-introduction-link] e applicazioni [eseguibili Guest][guest-executables-introduction-link] . Queste semantiche consentono alle applicazioni e ai servizi di completare un'attività e di uscire, al contrario, a eseguire sempre le applicazioni e i servizi.

Prima di procedere con questo articolo, è consigliabile acquisire familiarità con il [modello di applicazione Service Fabric][application-model-link] e con il [modello di hosting Service Fabric][hosting-model-link].

> [!NOTE]
> La semantica RunToCompletion non è attualmente supportata per i servizi scritti con il modello di programmazione [Reliable Services][reliable-services-link] .
 
## <a name="runtocompletion-semantics-and-specification"></a>Semantica e specifica RunToCompletion
La semantica RunToCompletion può essere specificata come **ExecutionPolicy** durante [l'importazione di ServiceManifest][application-and-service-manifests-link]. Il criterio specificato viene ereditato da tutti i CodePackage che comprendono ServiceManifest. Il seguente frammento di ApplicationManifest.xml fornisce un esempio.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** consente i due attributi seguenti:
* **Tipo:** **RunToCompletion** è attualmente l'unico valore consentito per questo attributo.
* **Riavvio:** Questo attributo specifica i criteri di riavvio applicati ai CodePackage che comprendono ServicePackage, in caso di errore. Un CodePackage che termina con un **codice di uscita diverso da zero** viene considerato non riuscito. I valori consentiti per questo attributo sono **OnFailure** e **mai** con **OnFailure** come valore predefinito.

Con i criteri di riavvio impostati su **OnFailure**, in caso di errore di un CodePackage **(codice di uscita diverso da zero)**, questo viene riavviato, con i back-off tra gli errori ripetuti. Con i criteri di riavvio impostati su **Never**, se si verifica un errore in un CodePackage, lo stato della distribuzione di DeployedServicePackage è contrassegnato come **failed** , ma è possibile continuare l'esecuzione di altri CodePackage. Se tutti i CodePackage che includono ServicePackage vengono eseguiti fino al completamento **(codice di uscita 0)**, lo stato della distribuzione di DeployedServicePackage è contrassegnato come **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Esempio completo con la semantica RunToCompletion

Viene ora esaminato un esempio completo che usa la semantica RunToCompletion.

> [!IMPORTANT]
> Nell'esempio seguente si presuppone una certa familiarità con la creazione di [applicazioni contenitore Windows con Service Fabric e Docker][containers-getting-started-link].
>
> Questo esempio fa riferimento a mcr.microsoft.com/windows/nanoserver:1809. I contenitori di Windows Server non sono compatibili con tutte le versioni del sistema operativo host. Per altre informazioni, vedere [Compatibilità delle versioni dei contenitori di Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Nell'ServiceManifest.xml seguente viene descritto un ServicePackage costituito da due CodePackage, che rappresentano i contenitori. *RunToCompletionCodePackage1* registra semplicemente un messaggio in **stdout** e viene chiuso. *RunToCompletionCodePackage2* effettua il ping dell'indirizzo di loopback per un periodo di tempo e quindi viene chiuso con un codice di uscita pari a **0**, **1** o **2**.

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

Il ApplicationManifest.xml seguente descrive un'applicazione basata sul ServiceManifest.xml descritto in precedenza. Specifica **RunToCompletion** **ExecutionPolicy** per *WindowsRunToCompletionServicePackage* con i criteri di riavvio di **OnFailure**. Al momento dell'attivazione di *WindowsRunToCompletionServicePackage*, verranno avviati i relativi CodePackage costitutivi. *RunToCompletionCodePackage1* dovrebbe uscire correttamente al primo attivazione. Tuttavia, *RunToCompletionCodePackage2* può avere esito negativo **(codice di uscita diverso da zero)**, nel qual caso verrà riavviato perché i criteri di riavvio sono **OnFailure**.

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
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Esecuzione di query sullo stato della distribuzione di un DeployedServicePackage
È possibile eseguire query sullo stato della distribuzione di un DeployedServicePackage da PowerShell usando [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] o da C# usando [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (String, Uri, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Considerazioni sull'uso della semantica RunToCompletion

Per il supporto RunToCompletion corrente, è necessario notare i punti seguenti.
* Queste semantiche sono supportate solo per i [contenitori][containers-introduction-link] e le applicazioni [eseguibili Guest][guest-executables-introduction-link] .
* Gli scenari di aggiornamento per le applicazioni con semantica RunToCompletion non sono consentiti. Se necessario, gli utenti devono eliminare e ricreare tali applicazioni.
* Gli eventi di failover possono causare la ripetizione dell'esecuzione di CodePackage dopo il completamento, nello stesso nodo o in altri nodi del cluster. Esempi di eventi di failover sono i riavvii dei nodi e Service Fabric aggiornamenti in fase di esecuzione in un nodo.

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
