<properties
   pageTitle="Vincoli di posizionamento dell'orchestrazione del cluster di Service Fabric"
   description="Panoramica concettuale dei vincoli di posizionamento in Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/17/2015"
   ms.author="abhic"/>

# Panoramica dei vincoli di posizionamento

Service Fabric consente agli sviluppatori di vincolare il posizionamento delle repliche dei servizi in nodi che soddisfano condizioni particolari. Tali condizioni vengono indicate mediante un'espressione booleana che viene valutata con valori appropriati specifici del contesto del servizio.


## Funzionalità
Usando i vincoli di posizionamento, è possibile:

- Destinare tipi diversi di servizi a tipi diversi di nodi mediante la definizione di più NodeProperty per i nodi.

- Applicare determinati vincoli alle repliche primarie, ma non a quelle secondarie.


## Concetti principali
NodeProperty. Mapping definito dall'utente o dal sistema tra una stringa e un valore che può variare da un nodo all'altro, ovvero NodeName.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per altre informazioni, vedere l'articolo relativo agli [scenari applicativi](../service-fabric-application-scenarios).
 

<!---HONumber=July15_HO2-->