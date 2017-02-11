---
title: Deframmentazione delle metriche in Azure Service Fabric | Documentazione Microsoft
description: Informazioni generali sull&quot;uso della deframmentazione o della compressione come strategia per le metriche in Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 68183597e19347f10a0ffe24a418dbcac409cb14


---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Deframmentazione delle metriche e del carico in Service Fabric
Cluster Resource Manager di Service Fabric si occupa principalmente del bilanciamento del carico in termini di distribuzione del carico stesso, assicurandosi che tutti i nodi del cluster vengano usati allo stesso modo. Si tratta in genere del layout più intelligente e sicuro per la sopravvivenza agli errori perché garantisce che qualsiasi anomalia non interessi gran parte di un carico di lavoro. Cluster Resource Manager di Service Fabric supporta anche una strategia diversa, ovvero la deframmentazione. La deframmentazione in genere implica che invece di tentare di distribuire l'utilizzo di una metrica all'interno del cluster, si cerca di favorirne un consolidamento effettivo. Si tratta di una fortunata inversione della nostra strategia normale: invece di ottimizzare il cluster riducendo al minimo la deviazione media standard della metrica del carico per una determinata metrica, si inizia l'ottimizzazione dagli incrementi della deviazione. Ma perché si dovrebbe desiderare questa strategia?

Se si ripartisce il carico uniformemente tra i nodi nel cluster, verranno usate alcune delle risorse offerte dai nodi. In genere ciò non rappresenta un problema, ma a volte alcuni carichi di lavoro creano servizi eccezionalmente grandi, destinando ad esempio il 75% - 95% delle risorse di un nodo a una singola istanza o replica del servizio. Ciò non rappresenta un problema dal momento che Cluster Resource Manager rileva, in fase di creazione del servizio, la necessità di riorganizzare il cluster per far posto a questo carico di lavoro di grandi dimensioni e consentirne l'implementazione. Tuttavia, nel frattempo il carico di lavoro deve rimanere in attesa di essere pianificato nel cluster.

Dato che la pianificazione di nuovi carichi di lavoro è in genere almeno in parte sensibile alla latenza, se non si fa qualcosa di diverso si rischia di non riuscire a rispettare i contratti di servizio in caso di una gran quantità di servizi e il relativo stato da spostare, in modo particolare se i carichi di lavoro nel cluster sono generalmente di grandi dimensioni e quindi richiedono più tempo per lo spostamento all'interno del cluster. In effetti, misurando i tempi di creazione nelle simulazioni basate su dati cluster reali, si è notato che se i servizi fossero abbastanza grandi e il cluster fosse equamente usato, la creazione di quei servizi di grandi dimensioni risulterebbe rallentata, problema che potrebbe essere risolto con l'introduzione del criterio di metriche di deframmentazione.

Proprio come il processo di creazione o accesso a un file potrebbe essere rallentato in caso di frammentazione del disco rigido del computer e velocizzato tramite la deframmentazione dell'unità in modo che siano disponibili blocchi continui più grandi, è possibile configurare le metriche di deframmentazione per permettere a Cluster Resource Manager di concentrare il carico dei servizi in pochi nodi per lasciare (quasi) sempre spazio per servizi di dimensioni ancora maggiori, consentendone la rapida creazione. La maggior parte delle persone non ne avrà bisogno, poiché in genere i servizi devono essere piccoli e pertanto non è difficile trovare spazio, ma se si dispone di servizi di grandi dimensioni ed è necessario crearli in modo rapido, e si è disposti ad accettare compromessi quali l'impatto aumentato degli errori e di alcune risorse lasciate inutilizzate durante l'attesa di carichi di lavoro per la pianificazione, la strategia giusta è la deframmentazione.

Il diagramma di seguito offre una rappresentazione visiva di due cluster diversi, uno deframmentato e uno non deframmentato. Nel caso bilanciato, considerare i movimenti che sarebbero necessari per posizionare uno degli oggetti di servizio più grandi, se ne venisse creato uno nuovo, rispetto al cluster deframmentato, in cui potrebbe essere posizionato immediatamente nei nodi 4 o 5.

![Confronto tra cluster bilanciati e deframmentati][Image1]

## <a name="defragmentation-pros-and-cons"></a>Vantaggi e svantaggi della deframmentazione
Quali sono quindi questi altri compromessi concettuali? Si consiglia di misurare accuratamente i carichi di lavoro prima di attivare le metriche di deframmentazione. Ecco una tabella riepilogativa degli aspetti da considerare:

| Vantaggi della deframmentazione | Svantaggi della deframmentazione |
| --- | --- |
| Consente di creare servizi di grandi dimensioni più rapidamente |Concentra i carichi in meno nodi, aumentando il conflitto |
| Consente di ridurre lo spostamento dei dati durante la creazione |Eventuali errori possono impattare più servizi e causare una maggiore varianza |
| Consente una descrizione completa dei requisiti e il recupero di spazio |Configurazione di gestione delle risorse complessiva più complessa |

È possibile combinare metriche normali e deframmentate nello stesso cluster. Resource Manager farà il possibile per offrire all'utente un layout che consolidi quante più metriche di deframmentazione possibili mentre tenta di distribuire il resto. I risultati esatti ottenuti dipenderanno dal numero di metriche di bilanciamento rispetto al numero di metriche di deframmentazione, i relativi pesi, i carichi correnti e così via.

## <a name="configuring-defragmentation-metrics"></a>Configurazione delle metriche di deframmentazione
La configurazione delle metriche di deframmentazione è una decisione globale nel cluster ed è possibile selezionare metriche singole per la deframmentazione:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>Passaggi successivi
* Cluster Resource Manager dispone di molte opzioni per la descrizione del cluster. Per altre informazioni a riguardo vedere l'articolo [Descrizione di un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png



<!--HONumber=Nov16_HO3-->


