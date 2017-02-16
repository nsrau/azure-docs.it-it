---
title: Domande frequenti su Microsoft Azure Service Fabric | Microsoft Docs
description: Domande frequenti su Service Fabric e relative risposte
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/19/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 102be620e8812cc551aebafe7c8df4e4eac0ae90
ms.openlocfilehash: 2ad3bd7b846693c637fd843383802651a619b128

---


# <a name="commonly-asked-service-fabric-questions"></a>Domande frequenti su Service Fabric

Esistono molte domande frequenti sulle caratteristiche e sulle modalità di uso di Service Fabric. In questo documento vengono illustrate molte di queste domande comuni e le relative risposte.

## <a name="cluster-setup-and-management"></a>Configurazione e gestione del cluster

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions"></a>È possibile creare un cluster che si estenda a più aree di Azure?

Non ancora, ma si tratta di una richiesta comune che continueremo ad analizzare.

Il nucleo della tecnologia di clustering di Service Fabric non dispone di informazioni sulle aree di Azure e può essere usata per unire macchine in esecuzione in tutto il mondo, purché dispongano di connettività di rete l'una con l'altra. Tuttavia, la risorsa cluster di Service Fabric in Azure è divisa in aree, così come i set di scalabilità di macchine virtuali su cui viene creato il cluster. Inoltre, offrire una replica di dati a coerenza elevata tra macchine lontane geograficamente l'una dall'altra è una sfida di non poco conto. Vogliamo garantire prestazioni pianificabili e accettabili prima di supportare cluster caratterizzati da più aree.

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>I nodi di Service Fabric ricevono automaticamente aggiornamenti del sistema operativo?

Non ancora, ma si tratta di una richiesta comune che abbiamo intenzione di implementare in futuro.

Il problema con gli aggiornamenti del sistema operativo è che solitamente richiedono un riavvio della macchina, causando così una perdita temporanea di disponibilità. Di per sé non sarebbe un problema, poiché Service Fabric reindirizzerà automaticamente verso altri nodi il traffico di tali servizi. Tuttavia, se gli aggiornamenti del sistema operativo non vengono coordinati all'interno del cluster, esiste il rischio di interruzione simultanea per parecchi nodi. Tali riavvii simultanei possono causare la perdita totale di disponibilità per un servizio o, perlomeno, per una partizione specifica (per un servizio con stato).

In futuro Microsoft supporterà un criterio di aggiornamento del sistema operativo completamente automatico e coordinato tra i vari domini di aggiornamento, per garantire la disponibilità nonostante i riavvii e altri guasti o errori imprevisti.

Nel frattempo è stato [messo a disposizione uno script](https://blogs.msdn.microsoft.com/azureservicefabric/2017/01/09/os-patching-for-vms-running-service-fabric/) che un amministratore del cluster può usare per avviare manualmente l'applicazione sicura di patch di ogni nodo.

### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Qual è la dimensione minima di un cluster di Service Fabric? Perché non può essere di dimensioni minori?

La dimensione minima supportata per un cluster di Service Fabric che esegue carichi di lavoro di produzione è di cinque nodi. Per scenari di sviluppo e test, sono supportati cluster a tre nodi.

Queste dimensioni minime esistono poiché il cluster di Service Fabric esegue un set di servizi di sistema con stato, inclusi il servizio di denominazione e la gestione del failover. Questi servizi, che tengono traccia di quali sono stati distribuiti nel cluster e dove sono attualmente ospitati, dipendono da una coerenza elevata. Tale coerenza elevata, a sua volta, dipende dalla possibilità di acquisire un *quorum* per qualsiasi aggiornamento specifico per lo stato di tali servizi, dove per quorum si intende una stretta maggioranza delle repliche (N/2 +&1;) per un determinato servizio.

Detto questo, esaminiamo alcune possibili configurazioni di cluster:

**Un nodo**: questa opzione non offre una disponibilità elevata, in quanto perdere quel singolo nodo per qualsivoglia motivo significa perdere l'intero cluster.

**Due nodi**: un quorum per un servizio distribuito tra due nodi (N = 2) è 2 (2/2 + 1 = 2). Se viene persa una singola replica, è impossibile creare un quorum. Dato che per aggiornare un servizio è necessario portare temporaneamente offline una replica, questa non è una configurazione utile.

**Tre nodi**: con tre nodi (N = 3), è comunque necessario creare un quorum di duo nodi (3/2 + 1 = 2). Ciò significa che è possibile perdere un singolo nodo e mantenere comunque il quorum.

La configurazione del cluster a tre nodi è supportata per gli scenari di sviluppo e test perché è possibile eseguire aggiornamenti in sicurezza e i guasti ai nodi individuali non rappresentano un rischio elevato, purché che non si verifichino contemporaneamente. Per i carichi di lavoro di produzione, è necessario disporre di una maggiore resilienza a tali guasti simultanei, pertanto sono richiesti cinque nodi.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>È possibile disattivare il cluster di notte o nei fine settimana per ridurre i costi?

Generalmente, no. Service Fabric archivia lo stato su dischi locali e temporanei, vale a dire che se la macchina virtuale viene spostata in un host diverso, i dati non vengono spostati con essa. In condizioni normali, ciò non rappresenta un problema, in quanto il nuovo nodo viene aggiornato dagli altri nodi. Tuttavia, se si arrestano tutti i nodi per riavviarli in un secondo momento, è molto probabile che la maggior parte di essi verrà avviata su nuovi host, rendendo impossibile il ripristino dle sistema.

Se si desidera creare cluster per testare l'applicazione prima di distribuirla, è consigliabile crearli in maniera dinamica come parte della [pipeline integrazione costante/distribuzione costante](service-fabric-set-up-continuous-integration.md).

## <a name="application-design"></a>Progettazione di applicazioni

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Qual è il modo migliore per eseguire query sui dati in partizioni di una raccolta Reliable Collections?

Le raccolte Reliable Collections sono in genere [partizionate](service-fabric-concepts-partitioning.md) per abilitare l'aumento delle istanze per maggiori prestazioni e velocità effettiva. Ciò significa che lo stato di un determinato servizio può essere distribuito in decine o centinaia di macchine. Per eseguire operazioni su quel set di dati completo, sono disponibili varie opzioni:

- Creare un servizio che esegua una query di tutte le partizioni di un altro servizio per estrarre i dati richiesti.
- Creare un servizio che possa ricevere dati da tutte le partizioni di un altro servizio.
- Inviare periodicamente dati da ogni servizio in un archivio esterno. Questo approccio è appropriato solamente se si eseguono query che non fanno parte della logica di business principale.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Qual è il modo migliore per eseguire query sui dati nei vari attori?

Gli attori sono progettati per essere unità indipendenti di stato e calcolo, pertanto è consigliabile non eseguire query generali dello stato degli attori in fase di esecuzione. Se è necessario eseguire query nel set completo di stati degli attori, è consigliabile piuttosto:

- Sostituire i servizi di attori con servizi Reliable con stato, come il numero delle richieste di rete per raccogliere tutti i dati, dal numero di attori al numero di partizioni nel servizio.
- Progettare gli attori per l'invio periodico del relativo stato a un'archiviazione esterna per facilitare le query. Come in precedenza, questo approccio è ideale solo se le query in esecuzione non sono richieste per il comportamento di runtime.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Quanti dai è possibile archiviare in una raccolta Reliable Collections?

In genere i servizi Reliable sono partizionati. Pertanto, la quantità di dati archiviabili è limitata solo dal numero di macchine presenti nel cluster e dalla quantità di memoria disponibile su tali macchine.

Ad esempio, si supponga di avere una raccolta Reliable Collections in un servizio con 100 partizioni e 3 replica, con archiviati oggetti di dimensioni pari a 1 KB di media. Si supponga quindi di disporre di un cluster di 10 macchine con 16 GB di memoria per macchina. Per ragioni di semplicità e cautela, si supponga che il sistema operativo, i servizi di sistema, il runtime di Service Fabric e i servizi consumino 6 GB di quella memoria, lasciandone così 10 disponibili per ogni macchina o 100 per il cluster.

Tenendo presente che ciascun oggetto deve essere archiviato tre volte (una primaria e due repliche), si avrà una memoria sufficiente per circa 35 milioni di oggetti nella raccolta, con funzionamento a piena capacità. Tuttavia, si consiglia di essere preparati in caso di perdita simultanea di un dominio di errore e di un dominio di aggiornamento, che rappresentano circa 1/3 di capacità e ne ridurrebbero così il numero a circa 23 milioni.

Si noti che questo calcolo presuppone inoltre:

- Che la distribuzione dei dati in tutte le partizioni sia approssimativamente uniforme o che si indichino le metriche di caricamento a Cluster Resource Manager. Per impostazione predefinita, Service Fabric bilancia il carico in base al numero di repliche. Nell'esempio precedente, vengono inserite 10 repliche primarie e 20 repliche secondarie su ciascun nodo nel cluster. Quanto descritto in precedenza è ideale per carichi distribuiti in modo uniforme tra le partizioni. Se i carichi non sono uniformi, è necessario riportarli in modo che il gestore delle risorse possa raggruppare più repliche di piccole dimensioni e consentire alle repliche di dimensioni maggiori di consumare più memoria su un singolo nodo.

- Che il servizio Reliable in questione sia l'unico ad archiviare stato nel cluster. Poiché è possibile distribuire più servizi in un cluster, è necessario tenere conto delle risorse di esecuzione e gestione dello stato richieste da ciascun servizio.

- Che il cluster stesso non si stia ampliando o riducendo. Se si aggiungono più macchine, Service Fabric bilancerà nuovamente le repliche per sfruttare la capacità aggiuntiva fino a quando il numero di macchine sarà superiore al numero di partizioni nel servizio, poiché una replica singola non può estendersi alle macchine. Al contrario, se si riduce la dimensione del cluster rimuovendo le macchine, le repliche saranno raggruppate più strettamente e avranno una minore capacità totale.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Quanti dati è possibile archiviare in un attore?

Come per i servizi Reliable, la quantità di dati archiviabile in un servizio attore è limitata solo dallo spazio totale su disco e della memoria disponibile tra i nodi del cluster. Tuttavia, i singoli attori sono più efficaci quando vengono usati per racchiudere una piccola quantità di logica di business di stato e associata. Come regola generale, un singolo attore dovrebbe avere uno stato misurabile in kilobyte.

## <a name="other-questions"></a>Altre domande

### <a name="how-does-service-fabric-relate-to-containers"></a>In che modo Service Fabric è correlato ai contenitori?

I contenitori offrono un modo semplice per racchiudere i servizi e le relative dipendenze in modo da risultare eseguibili con coerenza in tutti gli ambiente e usabili in modo isolato su una singola macchina. Service Fabric offre un modo per distribuire e gestire servizi, tra cui [quelli inclusi in un contenitore](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Si prevede di rendere disponibile Service Fabric in open source?

Microsoft prevede di rendere disponibili in open source i framework di Reliable Services e Reliable Actors in GitHub e accetterà i contributi della community per questi progetti. Seguire il [blog di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) per le informazioni più recenti.

Non si prevede attualmente di rendere disponibile in open source il runtime di Service Fabric.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui concetti chiave e sulle procedure consigliate di Service Fabric](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)



<!--HONumber=Jan17_HO3-->


