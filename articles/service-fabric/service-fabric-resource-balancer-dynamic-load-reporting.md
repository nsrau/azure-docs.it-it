<properties
   pageTitle="Creazione di report sul carico dinamico"
   description="Panoramica della creazione di report sul carico dinamico per Resource Balancer"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# Panoramica della creazione di report sul carico dinamico

In fase di runtime, gli oggetti servizio con stato e senza stato possono creare report sul carico tramite il metodo ReportLoad (membro delle interfacce IStatefulServicePartition e IStatelessServicePartition). La creazione di report sui valori di carico in fase di runtime è importante perché consente di raggruppare correttamente i servizi nei nodi e garantisce il rilevamento accurato dell'utilizzo delle risorse da parte del componente centrale Service Fabric Resource Balancer, in base a quanto rilevato dinamicamente dai servizi sui nodi.

Si noti che, quando una replica crea un report sul carico, prima questo viene riunito in batch con altri report sul carico in locale e dopo un singolo report viene inviato a Resource Balancer. Di conseguenza, se un servizio crea ripetutamente report sul carico in modo molto rapido, solo l'ultimo report viene effettivamente inviato a Resource Balancer.

Quando viene eseguito, Service Fabric Resource Balancer esamina tutte le informazioni relative al carico raccolte da tutti i nodi ed esegue alcuni controlli. Ad esempio, verifica la soglia di bilanciamento e le soglie di attività per le metriche, come definite nel manifesto del cluster. Questi controlli determinano se il cluster presenta problemi di bilanciamento tali da richiedere l'esecuzione di Resource Balancer e se un nodo risulta al di sopra della capacità per una delle metriche per cui è definita una capacità specifica. In caso di superamento della capacità, Resource Balancer considera innanzitutto solo i servizi sui nodi che condividono la metrica per cui la capacità risulta superata. In caso di sbilanciamento del cluster, Resource Balancer considera tutti i servizi correlati, in base alla metrica, a qualsiasi servizio che segnala la metrica sbilanciata. Se Service Fabric Resource Balancer determina che si è verificata una di queste situazioni, esegue una simulazione per individuare una disposizione più efficace dei servizi.

I servizi devono creare report ogni volta che si verifica una variazione del carico e non eseguire autonomamente interventi di aggregazione o livellamento dei report sul carico.

Si noti che, quando un servizio crea report sul carico, tali report sostituiscono i valori predefiniti di carico primario e secondario impostati al momento della creazione del servizio e, da quel momento in poi, diventano i nuovi valori di carico usati da Service Fabric Resource Balancer per prendere decisioni di bilanciamento o posizionamento.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per altre informazioni: [Architettura di Resource Balancer](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=August15_HO6-->