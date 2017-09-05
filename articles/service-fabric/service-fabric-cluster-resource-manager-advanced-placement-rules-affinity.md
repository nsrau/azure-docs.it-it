---
title: "Cluster Resource Manager di Service Fabric - Affinità | Documentazione Microsoft"
description: "Informazioni generali sulla configurazione dell'affinità per i servizi di Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 8122f1644da25a9a2ab05291dafc33c77a91147f
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurazione e utilizzo dell'affinità del servizio in Service Fabric
Il controllo di affinità è disponibile principalmente per facilitare la transizione di grandi applicazioni monolitiche verso ambienti cloud e di microservizi. Viene anche usato come ottimizzazione per migliorare le prestazioni dei servizi, sebbene questa operazione possa avere effetti collaterali.

Si supponga di voler importare in Service Fabric un'applicazione di grandi dimensioni o che non era stata progettata nell'ottica dei microservizi (o qualsiasi ambiente distribuito). Questo tipo di transizione è comune. È necessario innanzitutto sollevare l'intera applicazione nell'ambiente, creare il pacchetto e accertarsi che venga eseguita senza problemi. Quindi, la si suddivide in vari servizi più piccoli che comunicano tra loro.

Infine è possibile che nell'applicazione si siano verificati alcuni problemi. I problemi rientrano, in genere, in una di queste categorie:

1. Una parte del componente X dell'app monolitica aveva una dipendenza non documentata dal componente Y che abbiamo appena convertito in servizi separati. Poiché questi servizi sono in esecuzione su nodi diversi del cluster, vengono interrotti.
2. Queste componenti comunicano tramite (named pipe locali | memoria condivisa | file su disco), e devono essere subito in grado di scrivere su una risorsa locale condivisa per motivi di prestazioni. Tale dipendenza rigida viene rimossa, forse, in un secondo momento.
3. La procedura è corretta, ma si scopre che questi due componenti comunicano tra loro o sono sensibili alle prestazioni. Quando sono stati spostati in servizi separati, le prestazioni globali hanno subito dell'applicazione ne hanno risentito pesantemente o hanno aumentato la latenza. Di conseguenza, l'applicazione globale non soddisfa le aspettative.

In questi casi non si vuole perdere il lavoro di refactoring e non si vuole tornare all'app monolitica. L'ultima condizione può anche essere utile come una normale ottimizzazione. Tuttavia fino a quando non sarà possibile riprogettare i componenti perché funzionino in modo naturale come servizi (o fino a quando non si sarà in grado di risolvere le aspettative delle prestazioni in un altro modo), è necessario un senso di località.

Cosa fare? Si può provare ad attivare il servizio di affinità.

## <a name="how-to-configure-affinity"></a>Come configurare l'affinità
Per impostare l'affinità, è necessario definire una relazione di affinità tra due servizi. Si tratta di fare in modo che un servizio "punti" a un altro servizio affinché il primo possa essere eseguito solo se anche il secondo è in esecuzione. A volte, si fa riferimento all'affinità come a una relazioni padre-figlio, in cui l'elemento figlio punta all'elemento padre. L'affinità garantisce che le repliche o le istanze di un servizio vengano inserite negli stessi nodi in cui risiedono quelle di un altro servizio.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Un servizio figlio può partecipare solo a una relazione di affinità singola. Se si desidera creare un'affinità fra un servizio figlio e due servizi padre in una volta sola, sono disponibili due opzioni:
> - Invertire le relazioni (includere i punti parentService1 e parentService2 nel servizio figlio corrente), oppure
> - Designare uno dei padri come hub per convenzione e disporre di tutti i punti del servizio in quel servizio. 
>
> Il comportamento di selezione risultante del cluster deve essere lo stesso.
>

## <a name="different-affinity-options"></a>Diverse opzioni di affinità
L'affinità è rappresentata tramite vari possibili schemi di correlazione e ha due modalità diverse. La modalità di affinità più comune è la cosiddetta NonAlignedAffinity. Nella modalità NonAlignedAffinity le repliche o le istanze dei diversi servizi vengono inserite negli stessi nodi. L'altra modalità è AlignedAffinity. La modalità AlignedAffinity viene usata solo con i servizi con stato. La configurazione di due servizi con stati per l'allineamento dell'affinità garantisce che i primari di tali servizi vengano inseriti negli stessi nodi. La configurazione consente anche di inserire ogni coppia di secondari dei servizi negli stessi nodi. È possibile anche configurare una relazione NonAlignedAffinity per i servizi con stato, sebbene questa pratica sia meno comune. Per la relazione NonAlignedAffinity, le diverse repliche dei due servizi con stato saranno in esecuzione sugli stessi nodi, ma le relative primarie potrebbero finire in nodi diversi.

<center>
![Modalità di affinità e loro effetti][Image1]
</center>

### <a name="best-effort-desired-state"></a>Stato desiderato del massimo sforzo
Una relazione di affinità è migliore. Non fornisce le stesse garanzie di collocazione o affidabilità di quelle eseguite nello stesso processo eseguibile. I servizi in una relazione di affinità sono entità profondamente diverse che possono avere esito negativo ed essere spostate in modo indipendente. Una relazione di affinità può anche subire un'interruzione, anche se tali interruzioni sono temporanee. Ad esempio, i limiti di capacità possono significare che solo alcuni degli oggetti del servizio nella relazione di affinità possono contenere un determinato nodo. In questi casi, anche se è disponibile una relazione di affinità, non verrà applicata a causa di altri vincoli. Se è possibile eseguire questa operazione, la violazione viene corretta automaticamente in un secondo momento.

### <a name="chains-vs-stars"></a>Modelli a catena o a stella
Oggi Cluster Resource Manager non è in grado modellare le catene di relazioni di affinità di. Ciò significa che un servizio che è un elemento figlio in una relazione di affinità non potrà essere un elemento padre in un'altra relazione di affinità. Se si desidera modellare questo tipo di relazione, è necessario modellarla in modo efficace a forma di stella, invece di una catena. Per spostarsi da un modello a catena a uno a stella, l'elemento figlio più basso verrebbe imparentato con il padre del primo elemento figlio. A seconda della disposizione dei servizi, è possibile eseguire l'operazione più volte. Se non esiste alcun servizio padre naturale, è necessario crearne uno che funga da segnaposto. A seconda dei requisiti, è inoltre consigliabile esaminare i [gruppi di applicazioni](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Modelli a catena o a stella nel contesto delle relazioni di affinità][Image2]
</center>

Un altro aspetto da notare circa le relazioni di affinità attuali è che sono direzionali. Ciò significa che la regola di affinità impone solo che l'elemento figlio sia collocato nella stessa posizione dell'elemento padre. Non garantisce che l'elemento padre sia posizionato con l'elemento figlio. È anche importante notare che la relazione di affinità non può essere perfetta o immediatamente imposta poiché diversi servizi dispongono di diversi cicli di vita e possono avere esito negativo e spostarsi in modo indipendente. Supponiamo, ad esempio, che per l'elemento padre improvvisamente si verifichi un errore su un altro nodo perché si è arrestato in modo anomalo. Cluster Resource Manager e Gestione failover gestiscono prima di tutto il failover, poiché mantenere i servizi attivi, coerenti e disponibili è la priorità. Una volta completato il failover, la relazione di affinità è interrotta, ma per Cluster Resource Manager l'operazione si svolge in modo corretto fino a quando non rileva che l'elemento figlio non si trova con l'elemento padre. Questi tipi di controlli vengono eseguiti periodicamente. Altre informazioni sulla modalità in cui Cluster Resource Manager valuta i vincoli sono disponibili in [questo articolo](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), mentre [questo](service-fabric-cluster-resource-manager-balancing.md) fornisce altre indicazioni su come configurare la frequenza con cui questi vincoli vengono valutati.   


### <a name="partitioning-support"></a>Supporto del partizionamento
L'ultimo aspetto da notare è che le relazioni di affinità non sono supportate nelle situazioni in cui l'elemento padre è partizionato. I servizi padre partizionati potrebbero essere supportati alla fine, ma attualmente non è consentito.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulla configurazione dei servizi, [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
- Per limitare i servizi per un set ridotto di computer o per l'aggregazione del carico dei servizi, usare [Gruppi di applicazioni](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
