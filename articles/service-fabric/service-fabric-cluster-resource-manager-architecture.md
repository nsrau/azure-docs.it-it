<properties
   pageTitle="Resource Manager Architecture | Microsoft Azure"
   description="An architectural overview of Service Fabric Cluster Resource Manager."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Panoramica dell'architettura di Cluster Resource Manager
Per gestire le risorse del cluster, Cluster Resource Manager deve disporre di varie informazioni. Deve conoscere i servizi esistenti e la relativa quantità attuale (o predefinita) di risorse utilizzate. Deve conoscere la capacità effettiva dei nodi nel cluster e, di conseguenza, la quantità di risorse disponibili sia nel cluster intero sia in un nodo specifico. Si noti che l'utilizzo di risorse di un determinato servizio può cambiare nel tempo e che i servizi, in realtà, sono in genere interessati da più di una risorsa. In molti servizi diversi potrebbero essere presenti sia risorse "reali", come memoria e disco comunemente utilizzati tra numerosi tipi di servizi, sia risorse rilevanti solo per un determinato servizio.

## Altre considerazioni
A complicare il quadro si aggiunge il fatto che i proprietari e gli operatori del cluster talvolta non sono anche autori del servizio, o almeno sono le stesse persone ma con ruoli diversi. Quando si sviluppa il servizio, ad esempio, si conoscono solo alcune informazioni su cosa è necessario in termini di risorse e sulla modalità di distribuzione ideale dei diversi componenti, ma nel ruolo della persona che gestisce un evento imprevisto del sito live per tale servizio in produzione è richiesto un lavoro diverso, con strumenti diversi. Inoltre, né il cluster né i servizi stessi sono configurati staticamente: il numero di nodi nel cluster può aumentare e ridursi, i nodi di dimensioni diverse lasciano e rientrano nel cluster e i servizi possono modificare la loro allocazione delle risorse ed essere creati e rimossi. Gli aggiornamenti o altre operazioni di gestione possono passare tramite il cluster, con il rischio costante di errore.

## Componenti di Cluster Resource Manager e flusso dei dati
Resource manager dovrà conoscere molti aspetti generali sul cluster stesso, così come i requisiti di determinati servizi. A tale scopo, in Service Fabric, entrambi gli agenti del Resource Manager sono eseguiti su nodi individuali per aggregare informazioni sull'utilizzo di risorse locali ed è previsto un servizio Resource Manager centralizzato, a tolleranza di errore, che aggrega tutte le informazioni sui servizi e il cluster e risponde ai cambiamenti sulla base della configurazione dello stato desiderato del cluster e del servizio. La tolleranza di errore viene ottenuta attraverso lo stesso meccanismo adottato per i servizi, vale a dire la replica dello stato del servizio per un certo numero di repliche (in genere 7).

![Architettura di Resource Balancer][Image1]

Si consideri il diagramma di esempio. In fase di runtime potrebbe verificarsi un'intera serie di modifiche. Si supponga ad esempio che vi siano alcune modifiche della quantità di risorse utilizzate dai servizi, alcuni errori del servizio, che alcuni nodi si uniscano e lascino il cluster e così via. Tutte le modifiche in un nodo specifico vengono aggregate e inviate periodicamente al servizio Resource Manager centrale (1,2) in cui vengono nuovamente aggregate, analizzate e archiviate. Il servizio centrale esamina tutte le modifiche a intervalli di pochi secondi e determina eventuali azioni necessarie (3). Ad esempio potrebbe notare che sono stati aggiunti dei nodi vuoti al cluster e decidere di spostare alcuni servizi su questi nodi. Potrebbe inoltre notare che un determinato nodo è sovraccarico o che alcuni servizi presentano anomalie (o sono stati eliminati), liberando risorse in altri nodi.

Vedere il diagramma successivo e ciò che avviene nell'esempio. Si supponga che Resource Manager stabilisca che sono necessarie modifiche. Interagisce con altri servizi di sistema (in particolare Gestione failover) per apportare le modifiche necessarie. Le richieste di modifica vengono quindi inviate ai nodi appropriati (4). In questo caso, si presume che Resource Manager abbia notato che il nodo 5 è sovraccarico e che abbia pertanto deciso di spostare il servizio B da N5 a N4. Alla fine della riconfigurazione (5), il cluster avrà l'aspetto seguente:

![Architettura di Resource Balancer][Image2]

## Passaggi successivi
- Cluster Resource Manager dispone di molte opzioni per la descrizione del cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=AcomDC_0316_2016-->