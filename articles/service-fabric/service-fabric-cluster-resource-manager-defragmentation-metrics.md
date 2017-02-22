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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 5ef6381f7d182c818171eca3e3d32a00bc30268e


---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Deframmentazione delle metriche e del carico in Service Fabric
Cluster Resource Manager di Service Fabric si occupa principalmente del bilanciamento del carico in termini di distribuzione del carico stesso, assicurandosi che i nodi del cluster vengano usati allo stesso modo. Si tratta in genere del layout più intelligente e sicuro per la sopravvivenza agli errori perché garantisce che qualsiasi anomalia non interessi gran parte di un carico di lavoro. Cluster Resource Manager di Service Fabric supporta anche una strategia diversa, ovvero la deframmentazione. La deframmentazione in genere implica che invece di tentare di distribuire l'utilizzo di una metrica all'interno del cluster, si cerca di favorirne un consolidamento effettivo. Il consolidamento rappresenta una fortunata inversione della nostra strategia normale: invece di ridurre al minimo la deviazione media standard della metrica del carico Cluster Resource Manager punta agli incrementi della deviazione. Ma perché si dovrebbe desiderare questa strategia?

Se si ripartisce il carico uniformemente tra i nodi nel cluster, verranno usate alcune delle risorse offerte dai nodi. Tuttavia, alcuni carichi di lavoro creano servizi di dimensioni eccezionalmente elevate e consumano la maggior parte di un nodo. In questi casi è possibile che il 75-95% delle risorse di un nodo venga dedicato a un singolo oggetto di servizio. I carichi di lavoro di dimensioni elevate non sono un problema. Cluster Resource Manager determina già al momento della creazione del servizio la necessità di riorganizzare il cluster per far spazio al carico di lavoro di dimensioni elevate. Tuttavia, nel frattempo il carico di lavoro deve rimanere in attesa di essere pianificato nel cluster.

Se sono presenti molti servizi e stati da spostare, potrebbe occorrere molto tempo per inserire nel cluster il carico di lavoro di dimensioni elevate. Ciò è probabile se anche altri carichi di lavoro già nel cluster sono di dimensioni elevate e, pertanto, richiedono più tempo per gli spostamenti. Il team di Service Fabric ha misurato i tempi di creazione nelle simulazioni di questo scenario. È stato rilevato che se i servizi erano sufficientemente grandi e il cluster era molto utilizzato, la creazione di tali servizi impiegava parecchio tempo. Per gestire questo scenario, è stata introdotta la deframmentazione come strategia di bilanciamento. È stato riscontrato che per i carichi di lavoro di grandi dimensioni, soprattutto quelli per cui il tempo di creazione era elevato, la deframmentazione ha davvero aiutato nella pianificazione di tali carichi nel cluster.

È possibile configurare le metriche di deframmentazione in modo che Cluster Resource Manager tenti in maniera proattiva di condensare in meno nodi il carico dei servizi. Questo garantisce che ci sia (quasi) sempre spazio per servizi di dimensioni ancora più elevate e ne consente la creazione rapida quando necessario.

La maggior parte degli utenti non avrà bisogno della deframmentazione. I servizi dovrebbero in genere essere di piccole dimensioni e non dovrebbe quindi essere difficile trovare spazio nel cluster. Tuttavia, se si dispone di servizi di grandi dimensioni e da creare con rapidità (con tutti i compromessi del caso), allora la deframmentazione è la strategia più indicata.

Quali sono tali compromessi? Principalmente, la deframmentazione può aumentare l'impatto degli errori, poiché vengono eseguiti più servizi sul nodo che presenta errori. Inoltre, la deframmentazione assicura che alcune risorse nel cluster non vengano usate, poiché attendono la pianificazione dei carichi di lavoro.

Il diagramma di seguito offre una rappresentazione visiva di due cluster diversi, uno deframmentato e uno non deframmentato. Nel caso bilanciato, considerare il numero di spostamenti necessari per posizionare uno degli oggetti servizio di dimensioni più grandi. Confrontarli quindi con il cluster deframmentato, in cui il carico di lavoro di grandi dimensioni può essere immediatamente posizionato nei nodi quattro o cinque.

<center>
![Confronto tra cluster bilanciati e deframmentati][Image1]
</center>

## <a name="defragmentation-pros-and-cons"></a>Vantaggi e svantaggi della deframmentazione
Quali sono quindi questi altri compromessi concettuali? Si consiglia di misurare accuratamente i carichi di lavoro prima di attivare le metriche di deframmentazione. Ecco una tabella riepilogativa degli aspetti da considerare:

| Vantaggi della deframmentazione | Svantaggi della deframmentazione |
| --- | --- |
| Consente di creare servizi di grandi dimensioni più rapidamente |Concentra i carichi in meno nodi, aumentando il conflitto |
| Consente di ridurre lo spostamento dei dati durante la creazione |Eventuali errori possono impattare più servizi e causare una maggiore varianza |
| Consente una descrizione completa dei requisiti e il recupero di spazio |Configurazione di gestione delle risorse complessiva più complessa |

È possibile combinare le metriche normali e deframmentate nello stesso cluster. Cluster Resource Manager tenta di consolidare il più possibile le metriche di deframmentazione, diffondendo le altre. Se non sono presenti servizi che condividono tali metriche, i risultati possono essere positivi. I risultati esatti dipenderanno dal numero di metriche di bilanciamento rispetto al numero di metriche di deframmentazione, l'eventuale sovrapposizione, i relativi pesi, i carichi correnti e altri fattori. È necessario fare delle prove per determinare la configurazione esatta necessaria.

## <a name="configuring-defragmentation-metrics"></a>Configurazione delle metriche di deframmentazione
La configurazione delle metriche di deframmentazione è una decisione globale nel cluster ed è possibile selezionare metriche singole per la deframmentazione:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Disk",
          "value": "true"
      },
      {
          "name": "CPU",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Passaggi successivi
* Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png



<!--HONumber=Jan17_HO1-->


