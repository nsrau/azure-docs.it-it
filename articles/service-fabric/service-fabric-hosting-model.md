---
title: Modello di hosting di Azure Service Fabric | Microsoft Docs
description: Questo articolo descrive la relazione tra le repliche (o istanze) di un servizio Service Fabric distribuito e il processo host servizio.
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 367f21c63eac3969fb19eada91eae9a8577921de
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348481"
---
# <a name="azure-service-fabric-hosting-model"></a>Modello di hosting di Azure Service Fabric
Questo articolo fornisce una panoramica dei modelli di hosting delle applicazioni offerti da Azure Service Fabric e descrive le differenze tra i modelli **Shared Process** (Processo condiviso) ed **Exclusive Process** (Processo esclusivo). Descrive l'aspetto di un'applicazione in un nodo di Service Fabric e la relazione tra le repliche (o istanze) del servizio e il processo host servizio.

Prima di procedere oltre, assicurarsi di avere compreso i diversi concetti e relazioni illustrati in [Modellare un'applicazione in Service Fabric][a1]. 

> [!NOTE]
> In questo articolo, se non diversamente specificato:
>
> - *Replica* si riferisce sia a una replica di un servizio con stato che a un'istanza di un servizio senza stato.
> - *CodePackage* considerato equivalente a un processo *ServiceHost* che registra un *ServiceType* e ospita repliche dei servizi di tale *ServiceType*.
>

Per comprendere il servizio di hosting, esaminare l'esempio seguente. Si supponga di avere un *ApplicationType* "MyAppType" con un *ServiceType* "MyServiceType". "MyServiceType" viene fornito dal *ServicePackage* "MyServicePackage", che ha un *CodePackage* "MyCodePackage". "MyServiceType" registra *ServiceType* "MyServiceType" quando viene eseguito.

Si supponga anche di avere un cluster a tre nodi e di creare un'*applicazione* **fabric:/App1** di tipo "MyAppType". All'interno di questa applicazione **fabric:/App1** viene creato un servizio **fabric:/App1/ServiceA** di tipo "MyServiceType". Questo servizio ha due partizioni (ad esempio, **P1** e **P2**) e tre repliche per ogni partizione. Il diagramma seguente mostra la visualizzazione di questa applicazione distribuita in un nodo.


![Diagramma della visualizzazione del nodo dell'applicazione distribuita][node-view-one]


Service Fabric ha attivato "MyServicePackage" che ha avviato "MyCodePackage". Quest'ultimo ospita repliche da entrambe le partizioni. Tutti i nodi del cluster hanno la stessa visualizzazione perché è stato scelto un numero di repliche per partizione uguale al numero di nodi del cluster. Verrà ora creato un altro servizio, **fabric:/App1/ServiceB**, nell'applicazione **fabric:/App1**. Questo servizio ha una partizione (ad esempio, **P3**) e tre repliche per ogni partizione. Il diagramma seguente mostra la nuova visualizzazione del nodo:


![Diagramma della visualizzazione del nodo dell'applicazione distribuita][node-view-two]


Service Fabric posiziona la nuova replica per la partizione **P3** del servizio **fabric:/App1/ServiceB** nell'attivazione esistente di "MyServicePackage". Verrà ora creata un'altra applicazione **fabric:/App2** di tipo "MyAppType". In **fabric:/App2** creare un servizio **fabric:/App2/ServiceA**. Questo servizio ha due partizioni (**P4** e **P5**) e tre repliche per ogni partizione. Il diagramma seguente mostra la nuova visualizzazione del nodo:


![Diagramma della visualizzazione del nodo dell'applicazione distribuita][node-view-three]


Service Fabric attiva una nuova copia di "MyServicePackage", che avvia una nuova copia di "MyServicePackage". Repliche di entrambe le partizioni del servizio **fabric:/App2/ServiceA** (**P4** e **P5**) vengono posizionate in questa nuova copia "MyCodePackage".

## <a name="shared-process-model"></a>Modello Shared Process (Processo condiviso)
La sezione precedente descrive il modello di hosting predefinito offerto da Service Fabric, denominato Shared Process (Processo condiviso). In questo modello, per una determinata applicazione, solo una copia di un determinato *ServicePackage* viene attivata in un nodo (avviando così tutti i *CodePackages* contenuti). Tutte le repliche di tutti i servizi di un determinato *ServiceType* vengono inserite nel *CodePackage* che registra tale *ServiceType*. In altre parole, tutte le repliche di tutti i servizi in un nodo con un determinato valore di *ServiceType* condividono lo stesso processo.

## <a name="exclusive-process-model"></a>Modello Exclusive Process (Processo esclusivo)
L'altro modello di hosting offerto da Service Fabric è il modello Exclusive Process (Processo esclusivo). In questo modello ogni replica in un determinato nodo si trova nel proprio processo dedicato. Service Fabric attiva una nuova copia di *ServicePackage* (che avvia tutti i *CodePackages* contenuti). Le repliche vengono inserite nel *CodePackage* che ha registrato il *ServiceType* del servizio a cui appartiene la replica. 

Se si usa Service Fabric versione 5.6 o successiva, è possibile scegliere il modello Exclusive Process (Processo esclusivo) mentre si crea un servizio (usando [PowerShell][p1], [REST][r1] o [FabricClient][c1]). Specificare **ServicePackageActivationMode** come "ExclusiveProcess".

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Se nel manifesto dell'applicazione è presente un servizio predefinito, è possibile scegliere il modello Exclusive Process (Processo esclusivo) specificando l'attributo **ServicePackageActivationMode**:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Verrà ora creato un altro servizio, **fabric:/App1/ServiceC**, nell'applicazione **fabric:/App1**. Questo servizio ha due partizioni (ad esempio, **P6** e **P7**) e tre repliche per ogni partizione. Si imposta **ServicePackageActivationMode** su "ExclusiveProcess". Il diagramma seguente mostra la nuova visualizzazione del nodo:


![Diagramma della visualizzazione del nodo dell'applicazione distribuita][node-view-four]


Come è possibile notare, Service Fabric ha attivato due nuove copie di "MyServicePackage", una per ogni replica dalle partizioni **P6** e **P7**. Service Fabric ha inserito ogni replica nella copia dedicata di *CodePackage*. Quando si usa il modello Exclusive Process (Processo esclusivo), per una determinata applicazione possono essere attive in un nodo più copie di un determinato *ServicePackage*. Nell'esempio precedente le tre copie di "MyServicePackage" sono attive per **fabric:/App1**. A ognuna di queste copie di "MyServicePackage" è associato un valore **ServicePackageActivationId**. Questo ID identifica tale copia nell'applicazione **fabric:/App1**.

Quando si usa solo il modello Shared Process (Processo condiviso) per un'applicazione, è presente una sola copia attiva di *ServicePackage* in un nodo. Il **ServicePackageActivationId** per questa attivazione di *ServicePackage* è una stringa vuota come, ad esempio, in **fabric:/App2**.

> [!NOTE]
>- Con il modello di hosting Shared Process (Processo condiviso), **ServicePackageActivationMode** è uguale a **SharedProcess**. Si tratta del modello di hosting predefinito e non è necessario specificare il valore di **ServicePackageActivationMode** al momento della creazione del servizio.
>
>- Con il modello di hosting Exclusive Process (Processo esclusivo), **ServicePackageActivationMode** è uguale a **ExclusiveProcess**. Per usare questa impostazione, è consigliabile specificarla in modo esplicito al momento della creazione del servizio. 
>
>- Per visualizzare il modello di hosting di un servizio, eseguire una query relativa alla [descrizione del servizio][p2] e cercare il valore di **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Usare un pacchetto del servizio distribuito
Una copia attiva di un *ServicePackage* in un nodo viene definita [pacchetto del servizio distribuito][p3]. Quando si usa il modello Exclusive Process (Processo esclusivo) per la creazione di servizi, per una determinata applicazione potrebbero essere presenti più pacchetti del servizio distribuiti per lo stesso *ServicePackage*. Se si eseguono operazioni specifiche di un pacchetto del servizio distribuito, è consigliabile fornire **ServicePackageActivationId** per identificare un pacchetto del servizio distribuito specifico. Ad esempio, specificare l'ID se si [segnala l'integrità di un pacchetto del servizio distribuito][p4] o si [riavvia il pacchetto di codice di un pacchetto del servizio distribuito][p5].

È possibile trovare il **ServicePackageActivationId** di un pacchetto del servizio distribuito eseguendo una query sull'elenco di [pacchetti del servizio distribuiti][p3] in un nodo. Quando si cercano i [tipi di servizi distribuiti][p6], le [repliche distribuite][p7] e i [pacchetti di codice distribuiti][p8] in un nodo, il risultato della query contiene anche il **ServicePackageActivationId** del pacchetto del servizio distribuito padre.

> [!NOTE]
>- In base al modello di hosting Shared Process (Processo condiviso), in un determinato nodo per un'applicazione specificata viene attivata una sola copia di un *ServicePackage*. Il relativo **ServicePackageActivationId** è uguale a *empty string* e non è necessario specificarlo quando si eseguono operazioni correlate al pacchetto del servizio distribuito. 
>
> - In base al modello di hosting Exclusive Process (Processo esclusivo), in un determinato nodo per un'applicazione specificata può essere attiva una o più copie di un *ServicePackage*. Ogni attivazione ha un **ServicePackageActivationId** *non-empty*, specificato quando si eseguono operazioni correlate al pacchetto del servizio distribuito. 
>
> - Se si omette **ServicePackageActivationId**, viene usata l'impostazione predefinita *empty string*. Se è presente un pacchetto del servizio distribuito attivato in base al modello Shared Process (Processo condiviso), l'operazione verrà eseguita su tale pacchetto, altrimenti l'operazione non riesce.
>
> - Non eseguire una query e quindi memorizzare nella cache il **ServicePackageActivationId**. L'ID viene generato in modo dinamico e può cambiare per vari motivi. Prima di eseguire un'operazione che richiede il valore di **ServicePackageActivationId**, è necessario eseguire una query sull'[elenco di pacchetti del servizio distribuiti][p3] in un nodo. Usare quindi il **ServicePackageActivationId** del risultato della query per eseguire l'operazione originale.
>
>

## <a name="guest-executable-and-container-applications"></a>Applicazioni eseguibili guest e contenitore
Service Fabric tratta le applicazioni di tipo [eseguibile guest][a2] e [contenitore][a3] come servizi senza stato indipendenti. Non esistono runtime di Service Fabric in *ServiceHost* (un processo o un contenitore). Poiché che si tratta di servizi indipendenti, il numero di repliche per *ServiceHost* non è applicabile. La configurazione usata più comunemente con questi servizi è una partizione singola con [InstanceCount][c2] uguale a -1 (in ogni nodo del cluster è in esecuzione una copia del codice del servizio). 

Il valore **ServicePackageActivationMode** predefinito per questi servizi è **SharedProcess**: Service Fabric attiva una sola copia di *ServicePackage* in un nodo per una determinata applicazione.  Questo significa che una sola copia del codice del servizio sarà in esecuzione in un nodo. Per eseguire più copie del codice del servizio in un nodo, specificare **ServicePackageActivationMode** come **ExclusiveProcess** mentre si crea il servizio. È ad esempio possibile procedere in tal modo quando si creano più servizi (da *Service1* a *ServiceN*) di *ServiceType* (specificato in *ServiceManifest*) o quando il servizio ha più partizioni. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Modificare il modello di hosting di un servizio esistente
Non è attualmente possibile modificare il modello di hosting di un servizio esistente da Shared Process (Processo condiviso) a Exclusive Process (Processo esclusivo) o viceversa.

## <a name="choose-between-the-hosting-models"></a>Scegliere tra i modelli di hosting
È consigliabile valutare quale modello di hosting sia il più adatto ai propri requisiti. Il modello Shared Process (Processo condiviso) usa meglio le risorse del sistema operativo perché viene generato un numero minore di processi e più repliche all'interno dello stesso processo possono condividere le porte. Se tuttavia una delle repliche contiene un errore per cui l'host servizio deve essere arrestato, tale errore ha un impatto negativo su tutte le altre repliche nello stesso processo.

 Il modello Exclusive Process (Processo esclusivo) offre un migliore isolamento, con ogni replica in un proprio processo. Se una delle repliche presenta un errore, non influisce sulle altre repliche. Questo modello è utile nei casi in cui la condivisione delle porte non è supportata dal protocollo di comunicazione. Rende inoltre più semplice l'applicazione della governance delle risorse a livello di replica. Tuttavia, il modello Exclusive Process (Processo esclusivo) utilizza una maggiore quantità di risorse del sistema operativo e genera un processo per ogni replica nel nodo.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Considerazioni sul modello Exclusive Process (Processo esclusivo) e sul modello dell'applicazione
È possibile modellare la maggior parte delle applicazioni in Service Fabric mantenendo un *ServiceType* per ogni *ServicePackage*. 

In alcuni casi, Service Fabric consente anche più di un valore *ServiceType* per ogni oggetto *ServicePackage* (e un oggetto *CodePackage* può registrare più di un valore di *ServiceType*). Di seguito sono descritti alcuni scenari in cui queste configurazioni possono essere utili:

- Si vuole ottimizzare l'utilizzo delle risorse generando un minor numero di processi e aumentando la densità di replica per processo.
- Le repliche da diversi *ServiceType* devono condividere alcuni dati comuni caratterizzati da un alto costo di inizializzazione o memoria.
- Si usa una versione gratuita di un servizio e si vuole limitare l'utilizzo delle risorse inserendo tutte le repliche del servizio nello stesso processo.

Il modello di hosting Exclusive Process (Processo esclusivo) non è coerente con un modello dell'applicazione con più *ServiceType* per *ServicePackage*. Questo si verifica perché la configurazione con più *ServiceType* per *ServicePackage* è progettata per ottenere una maggiore condivisione delle risorse e consentire un aumento della densità di replica per processo. Il modello Exclusive Process (Processo esclusivo) è progettato per ottenere risultati diversi.

Si consideri il caso di più *ServiceType* per *ServicePackage* con un diverso *CodePackage* che registra ogni *ServiceType*. Si supponga di avere un *ServicePackage* "MultiTypeServicePackge" con due *CodePackage*:

- "MyCodePackageA", che registra il *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", che registra il *ServiceType* "MyServiceTypeB".

Ora si supponga di creare un'applicazione, **fabric:/SpecialApp**. In **fabric:/SpecialApp** vengono creati i due servizi seguenti con il modello Exclusive Process (Processo esclusivo):

- Servizio **fabric:/SpecialApp/ServiceA** di tipo "MyServiceTypeA", con due partizioni, **P1** e **P2**, e tre repliche per partizione.
- Servizio **fabric:/SpecialApp/ServiceB** di tipo "MyServiceTypeB", con due partizioni, **P3** e **P4**, e tre repliche per partizione.

In un determinato nodo entrambi i servizi hanno due repliche ognuno. Poiché per creare i servizi è stato usato il modello Exclusive Process (Processo esclusivo), Service Fabric attiva una nuova copia di "MyServicePackage" per ogni replica. Ogni attivazione di "MultiTypeServicePackge" avvia una copia di "MyCodePackageA" e "MyCodePackageB". Tuttavia, solo uno tra "MyCodePackageA" e "MyCodePackageB" ospita la replica per la quale è stato attivato "MultiTypeServicePackge". Il diagramma seguente mostra la visualizzazione del nodo:


![Diagramma della visualizzazione del nodo dell'applicazione distribuita][node-view-five]


Nell'attivazione di "MultiTypeServicePackge" per la replica della partizione **P1** del servizio **fabric:/SpecialApp/ServiceA**, "MyCodePackageA" ospita la replica. "MyCodePackageB" è in esecuzione. Analogamente, nell'attivazione di "MultiTypeServicePackge" per la replica della partizione **P3** del servizio **fabric:/SpecialApp/ServiceB**, "MyCodePackageB" ospita la replica. "MyCodePackageA" è in esecuzione. Di conseguenza, maggiore è il numero di *CodePackage* (che registrano *ServiceType* differenti) per *ServicePackage*, più elevato è l'utilizzo ridondante delle risorse. 
 
 Se tuttavia si creano i servizi **fabric:/SpecialApp/ServiceA** e **fabric:/SpecialApp/ServiceB** con il modello Shared Process (Processo condiviso), Service Fabric attiva soltanto una copia di "MultiTypeServicePackge" per l'applicazione **fabric:/SpecialApp**. "MyCodePackageA" ospita tutte le repliche per il servizio **fabric:/SpecialApp/ServiceA**. "MyCodePackageB" ospita tutte le repliche per il servizio **fabric:/SpecialApp/ServiceB**. Il diagramma seguente mostra la visualizzazione del nodo in questa impostazione: 


![Diagramma della visualizzazione del nodo dell'applicazione distribuita][node-view-six]


Nell'esempio precedente, si potrebbe pensare che, se "MyCodePackageA" registra sia "MyServiceTypeA" che "MyServiceTypeB" e non è presente alcun "MyCodePackageB", non è in esecuzione alcun *CodePackage* ridondante. Anche se è corretto, questo modello di applicazione non è coerente con il modello di hosting Exclusive Process (Processo esclusivo). Se l'obiettivo consiste nell'inserire ogni replica in un proprio processo dedicato, non è necessario registrare entrambi i *ServiceType* dallo stesso *CodePackage*. È invece sufficiente inserire ogni *ServiceType* nel rispettivo *ServicePackage*.

## <a name="next-steps"></a>Passaggi successivi
[Creare il pacchetto di un'applicazione][a4] e prepararlo per la distribuzione.

[Distribuire e rimuovere applicazioni][a5]. Questo articolo descrive come usare PowerShell per gestire le istanze dell'applicazione.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
