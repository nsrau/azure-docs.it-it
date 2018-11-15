---
title: Visualizzare i log dei contenitori in Azure Service Fabric | Microsoft Docs
description: Viene descritto come visualizzare i log dei contenitori per un servizio contenitore di Service Fabric in esecuzione tramite Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: twhitney
ms.openlocfilehash: 85623f5acfb33d73774e5898e3f8937b4e07a5a9
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299079"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Visualizzare i log per un servizio contenitore di Service Fabric
Azure Service Fabric è un agente di orchestrazione dei contenitori e supporta [contenitori Linux e Windows](service-fabric-containers-overview.md).  Questo articolo descrive come visualizzare i log dei contenitori di un servizio contenitore in esecuzione o di un contenitore non utilizzato, in modo da diagnosticare e risolvere i problemi.

## <a name="access-the-logs-of-a-running-container"></a>Accedere ai log di un contenitore in esecuzione
I log dei contenitori sono accessibili mediante [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  In un Web browser aprire Service Fabric Explorer dall'endpoint di gestione del cluster, visitando l'indirizzo [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

I log dei contenitori sono disponibili nel nodo del cluster in cui è in esecuzione l'istanza del servizio contenitore. Ad esempio, è possibile visualizzare i log del contenitore front-end Web dell'[applicazione di voto di esempio per Linux](service-fabric-quickstart-containers-linux.md). Nella visualizzazione struttura ad albero espandere **Cluster**>**Applicazioni**>**VotingType**>**fabric:/Voting/azurevotefront**.  Espandere quindi la partizione (d1aa737e-f22a-e347-be16-eec90be24bc1 in questo esempio) e verificare che il contenitore sia in esecuzione nel nodo del cluster *_lnxvm_0*.

Nella visualizzazione struttura ad albero individuare il pacchetto di codice nel nodo *_lnxvm_0* espandendo **Nodi**>**_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Pacchetti di codice**>**codice**.  Selezionare quindi l'opzione **Log contenitori** per visualizzare i log del contenitore.

![Piattaforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Accedere ai log di un contenitore non utilizzato o arrestato in modo anomalo
A partire dalla versione 6.2, è anche possibile recuperare i log per un contenitore non utilizzato o arrestato in modo anomalo tramite [API REST](/rest/api/servicefabric/sfclient-index) o comandi dell'[interfaccia della riga di comando di Service Fabric (SFCTL)](service-fabric-cli.md).

### <a name="set-container-retention-policy"></a>Configurare i criteri di conservazione dei contenitori
Per semplificare la diagnosi degli errori di avvio dei contenitori, Service Fabric (versione 6.1 o successive) supporta la conservazione di contenitori terminati o il cui avvio non è riuscito. Questo criterio può essere configurato nel file **ApplicationManifest.xml**, come mostrato nel frammento di codice seguente:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

L'impostazione **ContainersRetentionCount** specifica il numero di contenitori da conservare in caso di errore. Se viene specificato un valore negativo, verranno conservati tutti i contenitori con errori. Quando l'attributo **ContainersRetentionCount** non viene specificato, non verrà conservato alcun contenitore. L'attributo **ContainersRetentionCount** supporta anche i parametri dell'applicazione, quindi gli utenti possono specificare valori diversi per cluster di test e di produzione. Usare vincoli di posizionamento per specificare come destinazione un nodo specifico per il servizio contenitore quando si usa questa funzionalità, per evitare che il servizio contenitore passi ad altri nodi. Eventuali contenitori conservati tramite questa funzionalità devono essere rimossi manualmente.

### <a name="rest"></a>REST
Usare l'operazione per [ottenere i log dei contenitori distribuiti nel nodo](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) per ottenere i log per un contenitore arrestato in modo anomalo. Specificare il nome del nodo in cui era in esecuzione il contenitore, il nome dell'applicazione, il nome del manifesto del servizio e il nome del pacchetto di codice.  Specificare `&Previous=true`. La risposta conterrà i log dei contenitori per il contenitore non utilizzato dell'istanza del pacchetto di codice.

L'URI di richiesta si presenta nel formato seguente:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Richiesta di esempio:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

Corpo della risposta 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Usare il comando [sfctl service get-container-logs](service-fabric-sfctl-service.md) per recuperare i log per un contenitore arrestato in modo anomalo.  Specificare il nome del nodo in cui era in esecuzione il contenitore, il nome dell'applicazione, il nome del manifesto del servizio e il nome del pacchetto di codice. Specificare il flag `-previous`.  La risposta conterrà i log dei contenitori per il contenitore non utilizzato dell'istanza del pacchetto di codice.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –previous
```
Risposta:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Passaggi successivi
- Esaminare l'esercitazione [Creare un'applicazione contenitore Linux](service-fabric-tutorial-create-container-images.md).
- Altre informazioni su [Service Fabric e contenitori](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
