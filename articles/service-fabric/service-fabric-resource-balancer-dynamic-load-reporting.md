<properties
   pageTitle="Creazione di report sul carico dinamico | Microsoft Azure"
   description="Panoramica della creazione di report sul carico dinamico per Resource Balancer"
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
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Panoramica della creazione di report sul carico dinamico

In fase di runtime, gli oggetti servizio con stato e senza stato possono creare report sul carico tramite il metodo ReportLoad (membro delle interfacce IStatefulServicePartition e IStatelessServicePartition). È importante segnalare i valori di carico in fase di runtime. Essa consente di raggruppare correttamente i servizi nei nodi e garantisce il rilevamento accurato dell'utilizzo delle risorse da parte del componente centrale Resource Balancer in Service Fabric di Azure, in base a quanto rilevato dinamicamente dai servizi sui nodi.

Si noti che, quando una replica crea un report sul carico, questo viene prima riunito in batch locali con altri report sul carico e solo dopo un singolo report viene inviato a Resource Balancer. Di conseguenza, se un servizio crea ripetutamente report sul carico in modo molto rapido, solo l'ultimo report viene effettivamente inviato a Resource Balancer.

Quando viene eseguito, Resource Balancer di Service Fabric esamina tutte le informazioni relative al carico raccolte da tutti i nodi ed esegue alcuni controlli. Ad esempio, verifica la soglia di bilanciamento e le soglie di attività per le metriche, come definite nel manifesto del cluster. Questi controlli determinano se il cluster presenta problemi di bilanciamento tali da richiedere l'esecuzione di Resource Balancer e se un nodo risulta al di sopra della capacità per una delle metriche per cui è definita una capacità specifica. In caso di superamento della capacità, Resource Balancer considera innanzitutto solo i servizi sui nodi che condividono la metrica per cui la capacità risulta superata. In caso di sbilanciamento del cluster, Resource Balancer considera tutti i servizi correlati, in base alla metrica, a qualsiasi servizio che segnala la metrica sbilanciata. Se Resource Balancer determina che si è verificata una di queste situazioni, esso esegue una simulazione per individuare una disposizione più efficace dei servizi.

I servizi devono creare report di carico ogni volta che il carico cambia. Non devono eseguire qualsiasi tipo di aggregazione o livellamento dei report sul proprio carico.

Si noti che quando un servizio segnala il carico, i report sul carico sostituiscono i valori predefiniti di carico primari e secondari che sono stati definiti quando il servizio è stato creato. Diventano i nuovi valori di carico che vengono utilizzati quando Resource Balancer di Service Fabric deve prendere decisioni sul bilanciamento del carico o sulla sua posizione da questo momento.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Per altre informazioni: [Architettura di Resource Balancer](service-fabric-resource-balancer-architecture.md)

<!---HONumber=AcomDC_1223_2015-->