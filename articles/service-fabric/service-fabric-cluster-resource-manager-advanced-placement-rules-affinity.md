---
title: "Cluster Resource Manager di Service Fabric - Affinità | Documentazione Microsoft"
description: "Informazioni generali sulla configurazione dell&quot;affinità per i servizi di Service Fabric"
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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 16863217eddf0a4bbd85c52f8481c03e50dd9108


---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurazione e utilizzo dell'affinità del servizio in Service Fabric
Il controllo di affinità è disponibile principalmente per facilitare la transizione di grandi applicazioni monolitiche verso ambienti cloud e di microservizi. Ciò premesso, può anche essere usato in alcuni casi come ottimizzazione legittima per migliorare le prestazioni dei servizi, sebbene questa operazione possa avere effetti collaterali.

Si supponga di voler importare in Service Fabric un'applicazione di grandi dimensioni o che non era stata progettata nell'ottica dei microservizi. Questo tipo di transizione è comune. È necessario innanzitutto sollevare l'intera applicazione nell'ambiente, creare il pacchetto e accertarsi che venga eseguita senza problemi. Quindi, la si suddivide in vari servizi più piccoli che comunicano tra loro.

Poi si verifica un inconveniente. L'inconveniente rientra, in genere, in una di queste categorie:

1. Una parte del componente X dell'app monolitica aveva una dipendenza non documentata dal componente Y che abbiamo appena convertito in servizi separati. Poiché questi servizi sono in esecuzione su nodi diversi del cluster, vengono interrotti.
2. Questi comunicano tramite (named pipe locali | memoria condivisa | file su disco), ma devono essere subito in grado di scrivere su una risorsa condivisa per motivi di prestazioni. Tale dipendenza rigida viene rimossa in un secondo momento.
3. La procedura è corretta, ma si scopre che questi due componenti comunicano tra loro o sono sensibili alle prestazioni. Quando sono stati spostati in servizi separati, le prestazioni globali hanno subito dell'applicazione ne hanno risentito pesantemente o hanno aumentato la latenza. Di conseguenza, l'applicazione globale non soddisfa le aspettative.

In questi casi si non vogliamo perdere il lavoro di ricostruzione e non vogliamo tornare all'app monolitica. Tuttavia fino a quando non sarà possibile riprogettare i componenti perché funzionino in modo naturale come servizi (o fino a quando non si sarà in grado di risolvere le aspettative delle prestazioni in un altro modo), è necessario un senso di località.

Cosa fare? Si può provare ad attivare il servizio di affinità.

## <a name="how-to-configure-affinity"></a>Come configurare l'affinità
Per impostare l'affinità, è necessario definire una relazione di affinità tra due servizi. Si tratta di fare in modo che un servizio "punti" a un altro servizio affinché il primo possa essere eseguito solo se anche il secondo è in esecuzione. A volte, si fa riferimento all'affinità come a una relazioni padre-figlio, in cui l'elemento figlio punta all'elemento padre. L'affinità garantisce che le repliche o le istanze di un servizio vengano inserite negli stessi nodi in cui risiedono le repliche o le istanze di un altro servizio.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Diverse opzioni di affinità
L'affinità è rappresentata tramite vari possibili schemi di correlazione e ha due modalità diverse. La modalità di affinità più comune è la cosiddetta NonAlignedAffinity. Nella modalità NonAlignedAffinity le repliche o le istanze dei diversi servizi vengono inserite negli stessi nodi. L'altra modalità è AlignedAffinity. La modalità AlignedAffinity viene usata solo con i servizi con stato. La configurazione di due servizi con stati per l'allineamento dell'affinità garantisce che i primari di tali servizi vengano inseriti negli stessi nodi. La configurazione consente anche di inserire ogni coppia di secondari dei servizi negli stessi nodi. È possibile anche configurare una relazione NonAlignedAffinity per i servizi con stato, sebbene questa pratica sia meno comune. Per la relazione NonAlignedAffinity, le diverse repliche dei due servizi con stato saranno collocate sugli stessi nodi, ma non verrà eseguito alcun tentativo di allineamento di primarie o secondarie.

<center>
![Modalità di affinità e loro effetti][Image1]
</center>

### <a name="best-effort-desired-state"></a>Stato desiderato del massimo sforzo
Esistono alcune differenze tra il servizio di affinità e le architetture monolitiche. Molte di queste dipendono dal fatto che una relazione di affinità è migliore. I servizi in una relazione di affinità sono entità profondamente diverse che possono avere esito negativo ed essere spostate in modo indipendente. Sono disponibili anche le cause per cui una relazione di affinità potrebbe interrompersi. Ad esempio, i limiti di capacità per cui solo alcuni degli oggetti del servizio nella relazione di affinità possono contenere un determinato nodo. In questi casi, anche se è disponibile una relazione di affinità, non verrà applicata a causa di altri vincoli. Se è possibile effettuare questa operazione, la violazione viene corretta automaticamente in un secondo momento.

### <a name="chains-vs-stars"></a>Modelli a catena o a stella
Oggi Cluster Resource Manager non è in grado modellare le catene di relazioni di affinità di. Ciò significa che un servizio che è un elemento figlio in una relazione di affinità non potrà essere un elemento padre in un'altra relazione di affinità. Se si desidera modellare questo tipo di relazione, è necessario modellarla in modo efficace a forma di stella, invece di una catena. Per spostarsi da un modello a catena a uno a stella, l'elemento figlio più basso verrebbe imparentato con il padre del primo elemento figlio. A seconda della disposizione dei servizi, è possibile eseguire l'operazione più volte. Se non esiste alcun servizio padre naturale, è necessario crearne uno che funga da segnaposto. A seconda dei requisiti, è inoltre consigliabile esaminare i [gruppi di applicazioni](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Modelli a catena o a stella nel contesto delle relazioni di affinità][Image2]
</center>

Un altro aspetto da notare circa le relazioni di affinità attuali è che sono direzionali. Ciò significa che la regola "affinità" impone solo che l'elemento figlio si trovi nella stessa posizione dell'elemento padre, non che l'elemento padre si trovi con l'elemento figlio. Supponiamo, ad esempio, che per l'elemento padre improvvisamente si verifichi un errore su un altro nodo. In questo caso per Cluster Resource Manager l'operazione si svolge in modo corretto fino a quando non rileva che l'elemento figlio non si trova con l'elemento padre. La relazione di affinità non può essere perfetta o imposta subito poiché si tratta di servizi diversi con diversi cicli di vita.

### <a name="partitioning-support"></a>Supporto del partizionamento
L'ultimo aspetto da notare è che le relazioni di affinità non sono supportate nelle situazioni in cui l'elemento padre è partizionato. È possibile che in futuro venga attivato il supporto per questo aspetto, ma attualmente non è previsto.

## <a name="next-steps"></a>Passaggi successivi
* Per maggiori informazioni sulle altre opzioni disponibili per la configurazione dei servizi, consultare l'articolo relativo alle altre configurazioni disponibili per Cluster Resource Manager [Informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
* Per i casi in cui gli utenti usano l'affinità per limitare un piccolo set di computer e provare ad aggregare il carico di una raccolta di servizi, usare i [gruppi di applicazioni](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png



<!--HONumber=Jan17_HO1-->


