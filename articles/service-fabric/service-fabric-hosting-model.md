---
title: Modello di hosting di Azure Service Fabric | Microsoft Docs
description: Questo articolo descrive la relazione tra le repliche (o istanze) di un servizio Servic Fabric distribuito e il processo host del servizio.
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ca7092a06a9ffce8383ca8bc9f70ce312cdf9de4
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="service-fabric-hosting-model"></a>Modello di hosting di Service Fabric
Questo articolo offre una panoramica dei modelli di hosting delle applicazioni offerti da Service Fabric e descrive le differenze tra i modelli **Processo condiviso** ed **Exclusive Process** (Processo esclusivo). Descrive l'aspetto di un'applicazione in un nodo di Service Fabric e la relazione tra le repliche (o istanze) del servizio e il processo host del servizio.

Prima di procedere, verificare di avere acquisito familiarità con il [modello applicativo di Service Fabric][a1] e di aver compreso i diversi concetti e le relazioni esistenti tra di essi. 

> [!NOTE]
> In questo articolo, per semplicità, a meno che non indicato in modo esplicito:
>
> - Tutti gli usi del termine *replica* si riferiscono sia a una replica di un servizio con stato che a un'istanza di un servizio senza stato.
> - *CodePackage* viene trattato in modo equivalente al processo *ServiceHost* che registra un *ServiceType* e ospita repliche dei servizi di tale *ServiceType*.
>

Per comprendere il servizio di hosting, esaminare l'esempio seguente. Si supponga di avere un *ApplicationType* 'MyAppType' con un *ServiceType* 'MyServiceType' fornito dal *ServicePackage* 'MyServicePackage' che ha un *CodePackage* 'MyCodePackage' che registra *ServiceType* 'MyServiceType' quando viene eseguito.

Si supponga inoltre di avere un cluster a 3 nodi e di creare un'*applicazione* **fabric:/App1** di tipo 'MyAppType'. All'interno di questa *applicazione* **fabric:/App1** viene creato un servizio **fabric:/App1/ServiceA** di tipo 'MyServiceType' con 2 partizioni, **P1** & **P2**, e 3 repliche per partizione. Il diagramma seguente mostra la visualizzazione di questa applicazione distribuita in un nodo.

<center>
![Visualizzazione del nodo dell'applicazione distribuita][node-view-one]
</center>

Service Fabric ha attivato 'MyServicePackage' che ha avviato 'MyCodePackage'. Quest'ultimo ospita repliche da entrambe le partizioni, ovvero **P1** & **P2**. Si noti che tutti i nodi del cluster avranno la stessa visualizzazione poiché è stato scelto un numero di repliche per partizione uguale al numero di nodi del cluster. Viene ora creato un altro servizio **fabric:/App1/ServiceB** nell'applicazione **fabric:/App1** con una partizione, **P3**, e 3 repliche per partizione. Il diagramma seguente mostra la nuova visualizzazione del nodo:

<center>
![Visualizzazione del nodo dell'applicazione distribuita][node-view-two]
</center>

Come è possibile notare, Service Fabric posiziona la nuova replica per la partizione **P3** del servizio **fabric:/App1/ServiceB** nell'attivazione esistente di 'MyServicePackage'. Viene ora creata un'altra *applicazione* **fabric:/App2** di tipo 'MyAppType'. All'interno di **fabric:/App2** viene creato il servizio **fabric:/App2/ServiceA** con due partizioni, **P4** & **P5**, e 3 repliche per partizione. La figura seguente mostra la nuova visualizzazione del nodo:

<center>
![Visualizzazione del nodo dell'applicazione distribuita][node-view-three]
</center>

Questa volta Service Fabric ha attivato una nuova copia di 'MyServicePackage', che a sua volta avvia una nuova copia di 'MyCodePackage'. Repliche di entrambe le partizioni del servizio **fabric:/App2/ServiceA**, ovvero **P4** & **P5**, vengono posizionate in questa nuova copia 'MyCodePackage'.

## <a name="shared-process-model"></a>Modello Processo condiviso
Quanto descritto in precedenza è il modello di hosting predefinito offerto da Service Fabric ed è denominato **Processo condiviso**. In questo modello, per una determinata *applicazione* soltanto una copia di un determinato *ServicePackage* viene attivata in un *Nodo* (che avvia tutti i *CodePackage* in esso contenuti). Tutte le repliche di tutti i servizi di un *ServiceType* specificato vengono posizionate nel *CodePackage* che registra tale *ServiceType*. In altre parole, tutte le repliche di tutti i servizi di un determinato *ServiceType* condividono lo stesso processo.

## <a name="exclusive-process-model"></a>Modello Exclusive Process (Processo esclusivo)
L'alto modello di hosting offerto da Service Fabric è il modello **Exclusive Process** (Processo esclusivo). In questo modello, su un determinato *Nodo*, per posizionare ciascuna replica Service Fabric attiva una nuova copia di *ServicePackage*, che avvia tutti i *CodePackage* in esso contenuti. La replica viene posizionata nel *CodePackage* che ha registrato il *ServiceType* del servizio a cui la appartiene la replica. In altri termini, ogni replica si trova nel proprio processo dedicato. 

Questo modello è supportato a partire dalla versione 5.6 di Service Fabric. È possibile scegliere il modello **Exclusive Process** (Processo esclusivo) al momento della creazione del servizio (mediante [PowerShell][p1], [REST][r1] o [FabricClient][c1]) specificando **ServicePackageActivationMode** come 'ExclusiveProcess'.

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

Se nel manifesto dell'applicazione è presente un servizio predefinito, è possibile scegliere il modello **Exclusive Process** (Processo esclusivo) specificando l'attributo **ServicePackageActivationMode**, come mostrato di seguito:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Continuando con l'esempio precedente, viene ora creato un altro servizio **fabric:/App1/ServiceC** nell'applicazione **fabric:/App1** che ha 2 partizioni, **P6** & **P7**, e 3 repliche per partizione con **ServicePackageActivationMode** impostato su 'ExclusiveProcess'. Il diagramma seguente mostra la nuova visualizzazione del nodo:

<center>
![Visualizzazione del nodo dell'applicazione distribuita][node-view-four]
</center>

Come è possibile notare, Service Fabric ha attivato due nuove copie di 'MyServicePackage', una per ciascuna replica dalle partizioni **P6** & **P7**, e ha posizionato ciascuna replica nella relativa copia dedicata di *CodePackage*. Un altro aspetto da sottolineare e che, quando si usa il modello **Exclusive Process** (Processo esclusivo), per una determinata *applicazione* possono essere attive in un *Nodo* più copie di un *ServicePackage* specificato. Nell'esempio precedente è possibile notare che le tre copie di 'MyServicePackage' sono attive per **fabric:/App1**. A ciascuna di queste copie di 'MyServicePackage' è associato un **ServicePackageAtivationId** che identifica la copia all'interno dell'*applicazione* **fabric:/App1**.

Quando per un'*applicazione*, ad esempio **fabric:/App2** nell'esempio precedente, viene usato soltanto il modello **Processo condiviso**, è presente una sola copia attiva di *ServicePackage* su un *Nodo* e il **ServicePackageAtivationId** per questa attivazione di *ServicePackage* è 'empty string'.

> [!NOTE]
>- Il modello di hosting **Processo condiviso** corrisponde a **ServicePackageAtivationMode** uguale a **SharedProcess**. Si tratta del modello di hosting predefinito e non è necessario specificare il valore **ServicePackageAtivationMode** al momento della creazione del servizio.
>
>- Il modello di hosting **Exclusive Process** (Processo esclusivo) corrisponde a **ServicePackageAtivationMode** uguale a **ExclusiveProcess** e deve essere specificato in modo esplicito al momento della creazione del servizio. 
>
>- Per conoscere il modello di hosting del servizio è possibile eseguire una query su [descrizione servizio][p2] e cercare il valore di **ServicePackageAtivationMode**.
>
>

## <a name="working-with-deployed-service-package"></a>Utilizzo del pacchetto del servizio distribuito
Una copia attiva di un *ServicePackage* su un nodo viene definita [pacchetto del servizio distribuito][p3]. Come illustrato in precedenza, quando per la creazione di servizi viene usato il modello **Exclusive Process** (Processo esclusivo), per una determinata *applicazione* possono essere presenti più pacchetti del servizio distribuiti per lo stesso *ServicePackage*. Durante l'esecuzione di operazioni specifiche del pacchetto del servizio distribuito, ad esempio la [creazione di report sull'integrità di un pacchetto del servizio distribuito][p4] o il [riavvio di un pacchetto di codice di un pacchetto del servizio distribuito][p5] e così via, per identificare uno specifico pacchetto del servizio distribuito è necessario specificare il **ServicePackageActivationId**.

 È possibile ottenere il **ServicePackageActivationId** di un pacchetto del servizio distribuito eseguendo una query sull'elenco di [pacchetti del servizio distribuiti][p3] su un nodo. Quando si eseguono query sui [tipi di servizi distribuiti][p6], le [repliche distribuite][p7] e i [pacchetti di codice distribuiti][p8] su un nodo, il risultato della query contiene anche il **ServicePackageActivationId** del pacchetto del servizio distribuito padre.

> [!NOTE]
>- In base al modello di hosting **Processo condiviso**, in un determinato *nodo* per un'*applicazione* specificata viene attivata una sola copia di un *ServicePackage*. Il **ServicePackageActivationId** di tale copia è uguale a *empty string* e non è necessario specificarlo quando si eseguono operazioni correlate al pacchetto del servizio distribuito. 
>
> - In base al modello di hosting **Exclusive Process** (Processo esclusivo), in un determinato *nodo* per un'*applicazione* specificata possono essere attive una o più copie di un *ServicePackage*. Ciascuna attivazione ha un **ServicePackageActivationId** *non-empty*, che deve essere specificato quando si eseguono operazioni correlate al pacchetto del servizio distribuito. 
>
> - Se il **ServicePackageActivationId** non viene specificato, viene usata l'impostazione predefinita 'empty string'. Se è presente un pacchetto del servizio distribuito attivato in base al modello **Processo condiviso**, l'operazione verrà eseguita su tale pacchetto. In caso contrario, tale operazione avrà esito negativo.
>
> - Non è consigliabile eseguire una query e quindi memorizzare nella cache il **ServicePackageActivationId**. Questo infatti viene generato dinamicamente e può cambiare per diversi motivi. Prima di eseguire un'operazione che richiede il **ServicePackageActivationId**, è necessario eseguire una query sull'[elenco di pacchetti del servizio distribuiti][p3] su un nodo e quindi usare il *ServicePackageActivationId** del risultato della query per eseguire l'operazione originale.
>
>

## <a name="guest-executable-and-container-applications"></a>Applicazioni eseguibili guest e contenitore
Service Fabric tratta le applicazioni di tipo [eseguibile guest][a2] e [contenitore][a3] come servizi senza stato indipendenti. In altri termini, in *ServiceHost* non è presente alcun runtime di Service Fabric (processo o contenitore). Dal momento che si tratta di servizi indipendenti, il numero di repliche per *ServiceHost* non è applicabile. La configurazione usata più comunemente con questi servizi è una partizione singola con [InstanceCount][c2] uguale a -1. In altri termini, su ciascun nodo del cluster è in esecuzione una copia del codice del servizio. 

Il valore **ServicePackageActivationMode** predefinito per questi servizi è **SharedProcess**: Service Fabric attiva una sola copia di *ServicePackage* su un *Nodo* per una determinata *applicazione*. Questo significa che una sola copia del codice del servizio sarà in esecuzione su un *Nodo*. Se quando si creano più servizi (da *Service1* a *ServiceN*) di *ServiceType* (specificato in *ServiceManifest*) si vuole che su un *Nodo* vengano eseguite più copie del codice del servizio oppure, se il servizio ha più partizioni, al momento della creazione del servizio è necessario specificare **ServicePackageActivationMode** come **ExclusiveProcess**.

## <a name="changing-hosting-model-of-an-existing-service"></a>Modifica del modello di hosting di un servizio esistente
La modifica del modello di hosting di un servizio esistente da **Processo condiviso** a **Exclusive Process** (Processo esclusivo) e viceversa tramite un meccanismo di aggiornamento (o una specifica predefinita del servizio nel manifesto dell'applicazione) non è attualmente supportata. Il supporto per questa funzionalità è previsto per una versione futura.

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>Scelta tra i modelli Processo condiviso ed Exclusive Process (Processo esclusivo)
Entrambi questi modelli di hosting presentano vantaggi e svantaggi e gli utenti devono scegliere quello più adatto alle specifiche esigenze. Il modello **Processo condiviso** consente un migliore utilizzo delle risorse del sistema operativo in quanto viene generato un numero minore di processi, più repliche all'interno dello stesso processo possono condividere le porte e così via. Se, tuttavia, una delle repliche genera un errore per cui l'host del servizio deve essere arrestato, tale errore avrà un impatto negativo su tutte le altre repliche nello stesso processo.

 Il modello **Exclusive Process** (Processo esclusivo) offre un migliore isolamento con ciascuna replica in un proprio processo e un errore di una replica non avrà conseguenze sulle altre repliche. Questo risulta utile nei casi in cui la condivisione delle porte non è supportata dal protocollo di comunicazione. Rende inoltre più semplice l'applicazione della governance delle risorse a livello di replica. D'altra parte, il modello**Exclusive Process** (Processo esclusivo) utilizza una maggiore quantità di risorse del sistema operativo e genera un processo per ciascuna replica sul nodo.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Considerazioni sul modello Exclusive Process (Processo esclusivo) e sul modello dell'applicazione
Il metodo consigliato per modellare l'applicazione in Service Fabric consiste nel mantenere un *ServiceType* per *ServicePackage*. Questo modello offre buoni risultati per la maggior parte delle applicazioni. 

Tuttavia, per abilitare scenari speciali in cui non è opportuno avere un *ServiceType* per *ServicePackage*, Service Fabric consente di avere più di un *ServiceType* per *ServicePackage*. Un *CodePackage*, inoltre, può registrare più di un *ServiceType*. Di seguito sono descritti alcuni scenari in cui queste configurazioni possono essere utili:

- Si desidera ottimizzare l'utilizzo delle risorse del sistema operativo generando un minor numero di processi e aumentando la densità di replica per processo.
- Le repliche da diversi ServiceType devono condividere alcuni dati comuni caratterizzati da un alto costo di inizializzazione o memoria.
- Si usa una versione gratuita di un servizio e si vuole limitare l'utilizzo delle risorse inserendo tutte le repliche del servizio nello stesso processo.

Il modello di hosting **Exclusive Process** (Processo esclusivo) non è coerente con il modello dell'applicazione con più *ServiceType* per *ServicePackage*. Questo si verifica perché la configurazione con più *ServiceType* per *ServicePackage* è progettata per ottenere una maggiore condivisione delle risorse e consentire un aumento della densità di replica per processo. Si tratta dell'opposto del risultato per cui è stato progettato il modello **Exclusive Process** (Processo esclusivo).

Si consideri il caso di più *ServiceType* per *ServicePackage* con un diverso *CodePackage* che registra ciascun *ServiceType*. Si supponga di avere un *ServicePackage* 'MultiTypeServicePackge' con due *CodePackage*:

- 'MyCodePackageA', che registra il *ServiceType* 'MyServiceTypeA'.
- 'MyCodePackageB', che registra il *ServiceType* 'MyServiceTypeB'.

A questo punto viene creata un'*applicazione* **fabric:/SpecialApp**. All'interno di **fabric:/SpecialApp** vengono quindi creati i due servizi seguenti con il modello **Exclusive Process** (Processo esclusivo):

- Servizio **fabric:/SpecialApp/ServiceA** di tipo 'MyServiceTypeA' con due partizioni, **P1** e **P2**, e 3 repliche per partizione.
- Servizio **fabric:/SpecialApp/ServiceB** di tipo 'MyServiceTypeB' con due partizioni, **P3** e **P4**, e 3 repliche per partizione.

Su un determinato nodo, i servizi avranno due repliche ciascuno. Poiché per creare i servizi è stato usato il modello **Exclusive Process** (Processo esclusivo), Service Fabric attiverà una copia di 'MyServicePackge' per ogni replica. Ogni attivazione di 'MultiTypeServicePackge' avvierà una copia di 'MyCodePackageA' e 'MyCodePackageB'. Tuttavia, solo uno tra 'MyCodePackageA' e 'MyCodePackageB' ospiterà la replica per la quale è stato attivato 'MultiTypeServicePackge'. Il diagramma seguente mostra la visualizzazione del nodo:

<center>
![Visualizzazione del nodo dell'applicazione distribuita][node-view-five]
</center>

 Come si può notare, nell'attivazione di 'MultiTypeServicePackge' per la replica della partizione **P1** del servizio **fabric:/SpecialApp/ServiceA**, 'MyCodePackageA' ospita la replica mentre 'MyCodePackageB' è semplicemente in esecuzione. Analogamente, nell'attivazione di 'MultiTypeServicePackge' per la replica della partizione **P3** del servizio **fabric:/SpecialApp/ServiceB**, 'MyCodePackageB' ospita la replica mentre 'MyCodePackageA' è semplicemente in esecuzione. Di conseguenza, più alto è il numero di *CodePackage* (che registrano *ServiceType* differenti) per *ServicePackage*, più elevato sarà l'utilizzo ridondante delle risorse. 
 
 D'altra parte, se si creano i servizi **fabric:/SpecialApp/ServiceA** e **fabric:/SpecialApp/ServiceB** con il modello **Processo condiviso**, Service Fabric attiverà soltanto una copia di 'MultiTypeServicePackge' per l'*applicazione* **fabric:/SpecialApp** (come mostrato in precedenza). 'MyCodePackageA' ospiterà tutte le repliche del servizio **fabric:/SpecialApp/ServiceA** (o, per maggiore precisione, di qualsiasi servizio di tipo 'MyServiceTypeA') e 'MyCodePackageB' ospiterà tutte le repliche del servizio **fabric:/SpecialApp/ServiceB** (o, per maggiore precisione, di qualsiasi servizio di tipo 'MyServiceTypeB'). Il diagramma seguente mostra la visualizzazione del nodo in questa impostazione: 

<center>
![Visualizzazione del nodo dell'applicazione distribuita][node-view-six]
</center>

Nell'esempio precedente, si potrebbe pensare che, se 'MyCodePackageA' registra sia 'MyServiceTypeA' che 'MyServiceTypeB' e non è presente alcun 'MyCodePackageB', non sarà in esecuzione alcun *CodePackage* ridondante. Si tratta di una considerazione corretta, ma, come indicato in precedenza, questo modello di applicazione non è coerente con il modello di hosting **Exclusive Process** (Processo esclusivo). Se l'obiettivo consiste nell'inserire ogni replica in un proprio processo dedicato, la registrazione di entrambi i *ServiceType* da parte dello stesso *CodePackage* non è necessaria e l'inserimento di ciascun *ServiceType* nel proprio *ServicePacakge* è una scelta più naturale.

## <a name="next-steps"></a>Passaggi successivi
[Creare il pacchetto di un'applicazione][a4] e prepararlo per la distribuzione.

[Distribuire e rimuovere applicazioni con PowerShell][a5]: descrive come usare PowerShell per gestire le istanze dell'applicazione.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-deploy-existing-app.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage
