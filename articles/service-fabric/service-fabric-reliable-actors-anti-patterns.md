<properties
   pageTitle="Anti-modelli di Reliable Actors"
   description="Alcuni potenziali errori per i clienti che stanno imparando a usare Service Fabric Reliable Actors."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="claudioc"/>

# Modello di progettazione di Reliable Actors: alcuni anti-modelli

Per i clienti che stanno imparando a usare Service Fabric Reliable Actors, abbiamo identificato i potenziali errori seguenti:

* Considerare Reliable Actors come un sistema transazionale. Service Fabric Reliable Actors non è un sistema basato su commit in due fasi in grado di fornire ACID. Se non viene implementata la persistenza facoltativa e il computer su cui è in esecuzione l'attore smette di funzionare, anche il relativo stato corrente scompare. L'attore si riattiverà su un altro nodo molto rapidamente, ma senza l'implementazione della persistenza a supporto, lo stato andrà perduto. Tra i diversi tentativi, filtri duplicati e/o progettazione idempotente, è tuttavia possibile ottenere un elevato livello di affidabilità e coerenza.

* Disporre di blocchi. Tutte le operazioni eseguite in Reliable Actors dovrebbero essere asincrone. Questo è generalmente semplice perché le API asincrone ormai sono molte nella piattaforma Microsoft. Ma se per un motivo qualsiasi è necessario interagire con un sistema che fornisce solo un'API bloccante, sarà necessario inserirla in un wrapper che usa esplicitamente il pool di thread .NET.

* Ricorrere troppo all'architettura. Lasciare l'ambiente libero di funzionare. Per gli sviluppatori abituati a preoccuparsi di raccolte e blocchi simultanei o a usare strumenti per compilare oggetti dall'XML, può essere difficile scrivere solo il codice di una classe che esegue operazioni semplici come assegnare un valore a una variabile o pianificare il lavoro Le attività pianificate sono predefinite. I blocchi non sono necessari. Lo stato non è un elemento da evitare in tutti i casi. Chi in genere ha svolto molto lavoro sul lato server in ambienti su vasta scala avrà solo necessità di un po' di tempo per adattarsi.

* Ritenere che il collo di bottiglia dipenda da un unico attore. Spesso è molto facile cadere in questa trappola, avendo milioni di attori che si convogliano in una singola istanza di un altro attore. Usare l'approccio di aggregazione illustrato nel [modello di progettazione con calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md).

* Mappare alla cieca i modelli di entità. Questa situazione riguarda gli sviluppatori che provengono da un universo relazionale in cui i problemi vengono modellati usando le entità e le rispettive relazioni. Sebbene questo approccio sia ancora utile per comprendere l'argomento, dovrebbe accompagnarsi a un ragionamento orientato ai servizi e al comportamento.

<!---HONumber=August15_HO6-->