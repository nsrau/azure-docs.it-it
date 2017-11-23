---
title: Panoramica di Azure Service Fabric con Gestione API | Microsoft Docs
description: In questo articolo viene illustrata un'introduzione all'uso di Gestione API di Azure come gateway per le applicazioni Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: ea3b1f50bada3c1301f8661f8f0b4866cb1c732c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Panoramica di Service Fabric con Gestione API di Azure

Le applicazioni cloud necessitano in genere di un gateway front-end per garantire un singolo punto di ingresso per utenti, dispositivi o altre applicazioni. In Service Fabric un gateway può essere qualsiasi servizio senza stato, ad esempio un'[applicazione ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), o un altro servizio progettati per l'ingresso del traffico, ad esempio [Hub eventi](https://docs.microsoft.com/azure/event-hubs/), [Hub IoT](https://docs.microsoft.com/azure/iot-hub/) o [Gestione API di Azure](https://docs.microsoft.com/azure/api-management/).

In questo articolo viene illustrata un'introduzione all'uso di Gestione API di Azure come gateway per le applicazioni Service Fabric. Gestione API si integra direttamente in Service Fabric, consentendo di pubblicare API con un ampio set di regole di routing nei servizi Service Fabric back-end. 

## <a name="architecture"></a>Architettura
Un'architettura Service Fabric comune usa un'applicazione Web di una pagina che esegue chiamate HTTP ai servizi back-end che espongono API HTTP. L'[applicazione introduttive a Service Fabric di esempio](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) mostra un esempio di questa architettura.

In questo scenario, un servizio Web senza stato funge da gateway nell'applicazione Service Fabric. Questo approccio richiede la scrittura di un servizio Web che possa inoltrare richieste HTTP a servizi back-end, come illustrato nel diagramma seguente:

![Panoramica di Service Fabric con topologia di Gestione API di Azure][sf-web-app-stateless-gateway]

Man mano che le applicazioni aumentano in termini di complessità, lo stesso avviene per i gateway che devono presentare un'API a una miriade di servizi back-end. Gestione API di Azure è progettato per gestire API complesse con regole di routing, controllo di accesso, limitazione della velocità, monitoraggio, registrazione degli eventi e memorizzazione delle risposte nella cache con minimo intervento dell'utente. Gestione API di Azure supporta l'individuazione di servizi Service Fabric, la risoluzione delle partizioni e la selezione di repliche per indirizzare in modo intelligente le richieste direttamente ai servizi back-end in Service Fabric, senza richiedere all'utente di scrivere un gateway API senza stato personalizzato. 

In questo scenario, l'interfaccia utente Web viene comunque gestita tramite un servizio Web, mentre le chiamate API HTTP vengono gestite e instradate tramite Gestione API di Azure, come illustrato nel diagramma seguente:

![Panoramica di Service Fabric con topologia di Gestione API di Azure][sf-apim-web-app]

## <a name="application-scenarios"></a>Scenari applicativi

I servizi in Service Fabric possono essere con stato o senza stato ed essere partizionati usando uno di tre schemi: singleton, Int64 range e named. La risoluzione degli endpoint di servizio richiede l'identificazione di una specifica partizione di una determinata istanza del servizio. Durante la risoluzione di un endpoint di un servizio, è necessario specificare il nome dell'istanza del servizio (ad esempio, `fabric:/myapp/myservice`) e la specifica partizione del servizio, tranne nel caso di una partizione singleton.

Gestione API di Azure può essere usato con qualsiasi combinazione di servizi senza stato e con stato o qualsiasi schema di partizionamento.

## <a name="send-traffic-to-a-stateless-service"></a>Inviare traffico a un servizio senza stato

Nel caso più semplice, il traffico viene inoltrato a un'istanza del servizio senza stato. A tale scopo, un'operazione di Gestione API contiene criteri di elaborazione in ingresso con un back-end Service Fabric per l'esecuzione del mapping a una specifica istanza del servizio senza stato nel back-end Service Fabric. Le richieste inviate al servizio vengono inviate a una replica casuale dell'istanza del servizio senza stato.

#### <a name="example"></a>Esempio
Nello scenario seguente un'applicazione Service Fabric contiene un servizio senza stato denominato `fabric:/app/fooservice`, che espone un'API HTTP interna. Il nome dell'istanza del servizio è noto e può essere specificato a livello di codice direttamente nei criteri di elaborazione in ingresso di Gestione API. 

![Panoramica di Service Fabric con topologia di Gestione API di Azure][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Inviare traffico a un servizio con stato

Come per lo scenario del servizio senza stato, il traffico può essere inoltrato a un'istanza del servizio con stato. In questo caso, un'operazione di Gestione API contiene criteri di elaborazione in ingresso con un back-end Service Fabric per l'esecuzione del mapping di una richiesta a una specifica partizione di una specifica istanza del servizio *con stato*. La partizione a cui eseguire il mapping di ogni richiesta viene calcolata tramite un metodo lambda usando un input dalla richiesta HTTP in ingresso, ad esempio un valore nel percorso dell'URL. I criteri possono essere configurati per l'invio di richieste alla sola replica primaria o a una replica casuale per le operazioni di lettura.

#### <a name="example"></a>Esempio

Nello scenario seguente un'applicazione Service Fabric contiene un servizio con stato partizionato denominato `fabric:/app/userservice`, che espone un'API HTTP interna. Il nome dell'istanza del servizio è noto e può essere specificato a livello di codice direttamente nei criteri di elaborazione in ingresso di Gestione API.  

Il servizio viene partizionato usando lo schema di partizione Int64 con due partizioni e un intervallo di chiavi compreso tra `Int64.MinValue` e `Int64.MaxValue`. I criteri back-end calcolano una chiave di partizione entro l'intervallo specificato convertendo il valore `id` indicato nel percorso della richiesta URL in un intero a 64 bit, sebbene sia possibile usare qualsiasi algoritmo per calcolare la chiave di partizione. 

![Panoramica di Service Fabric con topologia di Gestione API di Azure][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Inviare traffico a più servizi senza stato

Negli scenari più avanzati è possibile definire un'operazione di Gestione API che esegua il mapping delle richieste a più di un'istanza del servizio. In questo caso, ogni operazione contiene criteri che eseguono il mapping delle richieste a una specifica istanza del servizio in base ai valori della richiesta HTTP in ingresso, ad esempio la stringa di query o il percorso dell'URL, e nel caso di servizi con stato una partizione nell'istanza del servizio. 

A tale scopo, un'operazione di Gestione API contiene criteri di elaborazione in ingresso con un back-end Service Fabric per l'esecuzione del mapping a un'istanza del servizio senza stato nel back-end Service Fabric in base ai valori recuperati dalla richiesta HTTP in ingresso. Le richieste a un'istanza del servizio vengono inviate a una replica casuale dell'istanza.

#### <a name="example"></a>Esempio

Questo esempio illustra come creare una nuova istanza del servizio senza stato per ogni utente di un'applicazione con un nome generato dinamicamente usando la formula seguente:
 
 - `fabric:/app/users/<username>`

 Ogni servizio dispone di un nome univoco, ma i nomi non sono noti in anticipo, poiché i servizi vengono creati in risposta all'input dell'utente o dell'amministratore e non possono quindi essere codificati in criteri APIM o regole di routing. Al contrario, il nome del servizio a cui inviare una richiesta viene generato nella definizione dei criteri back-end del valore `name` indicato nel percorso della richiesta dell'URL. ad esempio:

  - Una richiesta a `/api/users/foo` viene instradata all'istanza del servizio `fabric:/app/users/foo`
  - Una richiesta a `/api/users/bar` viene instradata all'istanza del servizio `fabric:/app/users/bar`

![Panoramica di Service Fabric con topologia di Gestione API di Azure][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Inviare traffico a più servizi con stato

In modo analogo all'esempio del servizio senza stato, un'operazione di Gestione API può eseguire il mapping di richieste a più istanze del servizio **con stato**, nel qual caso potrebbe anche essere necessario eseguire la risoluzione delle partizioni per ogni istanza del servizio con stato.

A tale scopo, un'operazione di Gestione API contiene criteri di elaborazione in ingresso con un back-end Service Fabric per l'esecuzione del mapping a un'istanza del servizio con stato nel back-end Service Fabric in base ai valori recuperati dalla richiesta HTTP in ingresso. Oltre al mapping di una richiesta a una specifica istanza del servizio, è possibile eseguire il mapping di una richiesta a una specifica partizione all'interno dell'istanza del servizio e, facoltativamente, alla replica primaria o a una replica secondaria casuale all'interno della partizione.

#### <a name="example"></a>Esempio

Questo esempio illustra come creare una nuova istanza del servizio con stato per ogni utente di un'applicazione con un nome generato dinamicamente usando la formula seguente:
 
 - `fabric:/app/users/<username>`

 Ogni servizio dispone di un nome univoco, ma i nomi non sono noti in anticipo, poiché i servizi vengono creati in risposta all'input dell'utente o dell'amministratore e non possono quindi essere codificati in criteri APIM o regole di routing. Al contrario, il nome del servizio a cui inviare una richiesta viene generato nella definizione dei criteri back-end del valore `name` indicato nel percorso della richiesta dell'URL. ad esempio:

  - Una richiesta a `/api/users/foo` viene instradata all'istanza del servizio `fabric:/app/users/foo`
  - Una richiesta a `/api/users/bar` viene instradata all'istanza del servizio `fabric:/app/users/bar`

Ogni istanza del servizio viene anche partizionata usando lo schema di partizione Int64 con due partizioni e un intervallo di chiavi compreso tra `Int64.MinValue` e `Int64.MaxValue`. I criteri back-end calcolano una chiave di partizione entro l'intervallo specificato convertendo il valore `id` indicato nel percorso della richiesta URL in un intero a 64 bit, sebbene sia possibile usare qualsiasi algoritmo per calcolare la chiave di partizione. 

![Panoramica di Service Fabric con topologia di Gestione API di Azure][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Passaggi successivi

Seguire l'[esercitazione](service-fabric-tutorial-deploy-api-management.md) per impostare il primo cluster Service Fabric con Gestione API e trasferire le richieste ai servizi tramite Gestione API.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png