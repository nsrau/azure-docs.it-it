---
title: "Testabilità: comunicazione tra servizi | Documentazione Microsoft"
description: Le comunicazioni da servizio a servizio sono un punto di integrazione critico di un'applicazione Infrastruttura di servizi. Questo articolo illustra alcune considerazioni di progettazione e tecniche di test.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4c6b53673dd4c79ce435c1593e7d08c7dd4a1cb0
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Scenari di Testabilità di Service Fabric: comunicazione tra servizi
Gli stili dell'architettura orientata ai microservizi e ai servizi vengono visualizzati naturalmente in Azure Service Fabric. In questi tipi di architetture distribuite, le applicazioni di microservizi con componenti sono in genere costituite da più servizi che devono comunicare tra loro. Anche nei casi più semplici, in genere si ha almeno un servizio Web senza stato e un servizio di archiviazione dei dati con stato che devono comunicare.

La comunicazione da servizio a servizio è un punto di integrazione critico di un'applicazione, in quanto ogni servizio espone un'API remota ad altri servizi. L'uso di un set di limiti di API che coinvolgono l'I/O richiede in genere particolare attenzione e diverse operazioni di test e di convalida.

Quando questi limiti di servizio sono raggruppati in un sistema distribuito, è necessario fare numerose considerazioni:

* *Protocollo di trasporto*. Si userà HTTP per una maggiore interoperabilità o un protocollo binario personalizzato per la velocità effettiva massima?
* *Gestione degli errori*. Come vengono gestiti gli errori permanenti e temporanei? Cosa succede quando un servizio viene spostato in un nodo diverso?
* *Timeout e latenza*. Nelle applicazioni a più livelli in che modo ogni livello di servizio gestirà la latenza nello stack e all'utente?

Sia che venga usato uno dei componenti integrati di comunicazione tra servizi forniti da Service Fabric, sia che ne venga creato uno personalizzato, testare le interazioni tra i servizi è essenziale per garantire resilienza nell'applicazione.

## <a name="prepare-for-services-to-move"></a>Preparare lo spostamento dei servizi
Le istanze del servizio possono spostarsi nel tempo. Ciò vale soprattutto se vengono configurate con la metrica di caricamento per il bilanciamento delle risorse ottimizzato personalizzato. Service Fabric sposta le istanze del servizio per ottimizzare la disponibilità anche durante aggiornamenti, failover, scalabilità e altre situazioni che si verificano nel corso della durata di un sistema distribuito.

Dal momento che i servizi si spostano all'interno del cluster, i client e gli altri servizi devono essere preparati a gestire due scenari durante la comunicazione con un servizio:

* L’istanza del servizio o la replica della partizione si è spostata dall’ultima volta in cui si è stabilita una comunicazione. Si tratta della parte normale del ciclo di vita di un servizio e deve essere prevista nel corso della durata dell'applicazione.
* L’istanza del servizio o la replica della partizione è in fase di spostamento. Sebbene in Infrastruttura di servizi il failover di un servizio da un nodo a un altro si verifichi molto rapidamente, potrebbe esserci un ritardo nella disponibilità se il componente di comunicazione del servizio è lento ad avviarsi.

La gestione normale di questi scenari è importante per garantire che il sistema funzioni senza problemi. A tale scopo, tenere presente quanto segue:

* Ogni servizio a cui è possibile connettersi ha un *indirizzo* sul quale è in ascolto, ad esempio HTTP o WebSocket. Quando un'istanza o una partizione del servizio si sposta, il relativo endpoint dell'indirizzo cambia. Si sposta in un altro nodo con un indirizzo IP diverso. Se si utilizzano componenti di comunicazione integrati, questi gestiranno automaticamente la nuova risoluzione degli indirizzi del servizio.
* Potrebbe esserci un aumento temporaneo della latenza del servizio quando l'istanza del servizio avvia nuovamente il listener. Ciò dipende dalla rapidità con cui il servizio apre il listener dopo lo spostamento dell'istanza del servizio.
* Tutte le connessioni esistenti devono essere chiuse e riaperte dopo l'apertura del servizio in un nuovo nodo. Un arresto o un riavvio normale del nodo consente di attendere che le connessioni esistenti siano arrestate normalmente.

### <a name="test-it-move-service-instances"></a>Test: spostare istanze del servizio
Grazie agli strumenti di testabilità di Service Fabric, è possibile creare uno scenario di test per testare queste situazioni in modi diversi:

1. Spostare la replica primaria di un servizio con stato.
   
    La replica primaria di una partizione del servizio con stato può essere spostata per diversi motivi. Utilizzare questa procedura per la replica primaria di una partizione specifica, per osservare in che modo reagiscono i servizi allo spostamento in modo molto controllato.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Arrestare un nodo.
   
    Quando un nodo viene arrestato, Service Fabric sposta tutte le istanze o le partizioni del servizio che erano presenti in quel nodo in uno degli altri nodi disponibili nel cluster. Usare questa procedura per testare una situazione in cui un nodo viene perso dal cluster e tutte le repliche e le istanze del servizio in tale nodo devono essere spostate.
   
    È possibile arrestare un nodo tramite il cmdlet di PowerShell **Stop-ServiceFabricNode** :
   
    ```powershell
   
    PS > Restart-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Mantenere la disponibilità del servizio
Service Fabric è una piattaforma progettata per garantire elevata disponibilità dei servizi. In casi estremi, tuttavia, i problemi dell'infrastruttura sottostante possono comunque causare indisponibilità. È importante testare anche questi scenari.

I servizi con stato usano un sistema basato su quorum per la replica dello stato per l'elevata disponibilità. Ciò significa che per eseguire le operazioni di scrittura deve essere disponibile un quorum di repliche. In rari casi, ad esempio nel caso di errore hardware diffuso, potrebbe non essere disponibile un quorum di repliche. In questi casi non sarà possibile eseguire operazioni di scrittura, ma sarà possibile eseguire operazioni di lettura.

### <a name="test-it-write-operation-unavailability"></a>Test: mancata disponibilità delle operazioni di scrittura
Grazie agli strumenti di testabilità di Service Fabric, è possibile inserire come test un errore che provoca la perdita di quorum. Sebbene si tratti di uno scenario raro, è importante che i client e i servizi che dipendono da un servizio con stato siano preparati a gestire le situazioni in cui non possono eseguire richieste di scrittura al servizio. È importante anche che il servizio con stato sia a conoscenza di questa possibilità e sia in grado di comunicarla normalmente ai chiamanti.

La perdita di quorum può essere indotta mediante il cmdlet di PowerShell **Invoke-ServiceFabricPartitionQuorumLoss** :

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

In questo esempio `QuorumLossMode` viene impostato su `QuorumReplicas`, per indicare che si vuole causare la perdita di quorum senza interrompere tutte le repliche. In questo modo è comunque possibile eseguire le operazioni di lettura. Per testare uno scenario in cui un'intera partizione non è disponibile, è possibile impostare questa opzione su `AllReplicas`.

## <a name="next-steps"></a>Passaggi successivi
[Azioni di Testabilità](service-fabric-testability-actions.md)

[Scenari di Testabilità](service-fabric-testability-scenarios.md)

