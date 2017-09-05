---
title: Deframmentazione delle metriche in Azure Service Fabric | Documentazione Microsoft
description: Informazioni generali sull'uso della deframmentazione o della compressione come strategia per le metriche in Service Fabric
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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 0b8b2bad967532bb0040407dc6a3a7b9599576d2
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Deframmentazione delle metriche e del carico in Service Fabric
La strategia predefinita di Cluster Resource Manager di Service Fabric per la gestione delle metriche di carico del cluster è di distribuire il carico. Assicurare che i nodi vengano usati in modo uniforme evita aree sensibili e non sensibili che possono causare conflitti e spreco di risorse. La distribuzione dei carichi di lavoro nel cluster è il modo più sicuro per la sopravvivenza agli errori perché garantisce che qualsiasi anomalia non interessi gran parte di un carico di lavoro. 

Cluster Resource Manager di Service Fabric supporta anche una strategia diversa per la gestione del carico, ovvero la deframmentazione. La deframmentazione implica che invece di tentare di distribuire l'uso di una metrica all'interno del cluster, viene consolidata. Il consolidamento rappresenta una inversione della strategia di bilanciamento predefinita: invece di ridurre al minimo la deviazione media standard del carico della metrica Cluster Resource Manager cerca di aumentarla.

## <a name="when-to-use-defragmentation"></a>Quando usare la deframmentazione
La distribuzione del carico nel cluster usa alcune delle risorse in ogni nodo. Alcuni carichi di lavoro creano servizi di dimensioni eccezionalmente elevate e consumano la maggior parte o tutto un nodo. In questi casi, è possibile che durante la creazione di grandi carichi di lavoro non vi sia spazio sufficiente su qualsiasi nodo per eseguirli. I carichi di lavoro di grandi dimensioni non sono un problema in Service Fabric. Cluster Resource Manager determina come è necessario per riorganizzare il cluster per far spazio al carico di lavoro di dimensioni elevate. Tuttavia, nel frattempo il carico di lavoro deve rimanere in attesa di essere pianificato nel cluster.

Se sono presenti molti servizi e stati da spostare, potrebbe occorrere molto tempo per inserire nel cluster il carico di lavoro di dimensioni elevate. Ciò è più probabile se anche altri carichi di lavoro già nel cluster sono di dimensioni elevate e pertanto richiedono più tempo per la riorganizzazione. Il team di Service Fabric ha misurato i tempi di creazione nelle simulazioni di questo scenario. È stato rilevato che la creazione di servizi di grandi dimensioni ha richiesto molto più tempo quando l'uso del cluster ha superato il valore tra il 30 e il 50%. Per gestire questo scenario, è stata introdotta la deframmentazione come strategia di bilanciamento. È stato riscontrato che per i carichi di lavoro di grandi dimensioni, soprattutto quelli per cui il tempo di creazione era elevato, la deframmentazione ha davvero aiutato nella pianificazione di tali carichi nel cluster.

È possibile configurare le metriche di deframmentazione in modo che Cluster Resource Manager tenti in maniera proattiva di condensare in meno nodi il carico dei servizi. Questo garantisce che ci sia quasi sempre spazio per servizi di dimensioni ancora più elevate senza necessità di riorganizzare il cluster. Non dover riorganizzare il cluster consente di creare rapidamente grandi carichi di lavoro.

La maggior parte degli utenti non avrà bisogno della deframmentazione. I servizi sono in genere di piccole dimensioni e non è difficile trovare spazio nel cluster. Quando la riorganizzazione è possibile avviene rapidamente poiché la maggior parte dei servizi sono di dimensioni ridotte e possono essere spostati rapidamente e in parallelo. Se tuttavia si dispone di servizi di grandi dimensioni da creare rapidamente allora la deframmentazione è la strategia più indicata. Si illustreranno gli svantaggi dell'uso della deframmentazione qui di seguito. 

## <a name="defragmentation-tradeoffs"></a>Svantaggi della deframmentazione
La deframmentazione può aumentare l'impatto degli errori, poiché vengono eseguiti più servizi sui nodi che presentano errori. La deframmentazione può anche aumentare i costi poiché le risorse del cluster devono essere tenute di riserva in attesa della creazione di carichi di lavoro di grandi dimensioni.

Il diagramma di seguito offre una rappresentazione visiva di due cluster, uno deframmentato e uno non deframmentato. 

<center>
![Confronto tra cluster bilanciati e deframmentati][Image1]
</center>

Nel caso bilanciato, considerare il numero di spostamenti necessari per posizionare uno degli oggetti servizio di dimensioni più grandi. Nel cluster deframmentato il carico di lavoro di grandi dimensioni potrebbe trovarsi nel nodo quattro o cinque senza dover attendere lo spostamento di altri servizi.

## <a name="defragmentation-pros-and-cons"></a>Vantaggi e svantaggi della deframmentazione
Quali sono quindi questi altri compromessi concettuali? Ecco una tabella riepilogativa degli aspetti da considerare:

| Vantaggi della deframmentazione | Svantaggi della deframmentazione |
| --- | --- |
| Consente di creare servizi di grandi dimensioni più rapidamente |Concentra i carichi in meno nodi, aumentando il conflitto |
| Consente di ridurre lo spostamento dei dati durante la creazione |Eventuali errori possono impattare più servizi e causare una maggiore varianza |
| Consente una descrizione completa dei requisiti e il recupero di spazio |Configurazione di gestione delle risorse complessiva più complessa |

È possibile combinare le metriche normali e deframmentate nello stesso cluster. Cluster Resource Manager tenta di consolidare il più possibile le metriche di deframmentazione, diffondendo le altre. I risultati della combinazione delle strategie di deframmentazione e bilanciamento del carico dipendono da diversi fattori, tra cui:
  - il numero delle metriche di bilanciamento e il numero delle metriche di deframmentazione
  - se un servizio usa entrambi i tipi di metriche 
  - i pesi delle metriche
  - i carichi correnti delle metriche
  
È necessario fare delle prove per determinare la configurazione esatta necessaria. Si consiglia di misurare accuratamente i carichi di lavoro prima di abilitare le metriche di deframmentazione nella produzione. Ciò vale soprattutto quando si combinano metriche di deframmentazione e metriche bilanciate all'interno dello stesso servizio. 

## <a name="configuring-defragmentation-metrics"></a>Configurazione delle metriche di deframmentazione
La configurazione delle metriche di deframmentazione è una decisione globale nel cluster ed è possibile selezionare metriche singole per la deframmentazione. I frammenti di codice di configurazione seguenti illustrano come configurare le metriche per la deframmentazione. In questo caso, "Metric1" è configurato come una metrica di deframmentazione, mentre "Metric2" continuerà a essere bilanciata normalmente. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Passaggi successivi
- Cluster Resource Manager dispone di varie opzioni per descrivere il cluster. Per altre informazioni a riguardo vedere l'articolo [Descrivere un cluster di Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Le metriche determinano il modo in cui Cluster Resource Manger di Service Fabric gestisce il consumo e la capacità del cluster. Per altre informazioni sulle metriche e su come configurarle, vedere [questo articolo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png

