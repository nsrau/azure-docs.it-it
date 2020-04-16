---
title: Bilanciamento delle metriche del sottocluster
description: L'effetto dei vincoli di posizionamento sul bilanciamento e su come gestirlo
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430643"
---
# <a name="balancing-of-subclustered-metrics"></a>Bilanciamento delle metriche del sottocluster

## <a name="what-is-subclustering"></a>Che cos'è il sottocluster

Il sottocluster si verifica quando i servizi con vincoli di posizionamento diversi hanno una metrica comune ed entrambi segnalano il carico per esso. Se il carico segnalato dai servizi differisce in modo significativo, il carico totale sui nodi avrà una grande deviazione standard e sarebbe simile a come il cluster è sbilanciato, anche quando ha il miglior equilibrio possibile.

## <a name="how-subclustering-affects-load-balancing"></a>Influenza del sottoclustering sul bilanciamento del caricoHow subclustering affects load balancing

Se il carico segnalato dai servizi su nodi diversi differisce in modo significativo, potrebbe sembrare che ci sia un grande squilibrio in cui non è presente nessuno. Inoltre, se il falso squilibrio causato dal sottocluster è maggiore dello squilibrio effettivo, è possibile confondere l'algoritmo di bilanciamento di Resource Manager e produrre un equilibrio non ottimale nel cluster.

Ad esempio, supponiamo di avere quattro servizi e che tutti segnalino un carico per la metrica Metrica1:

* Servizio A – ha un vincolo di posizionamento "NodeType, Type1", segnala un carico di 10
* Servizio B – ha un vincolo di posizionamento "NodeType, Type1", segnala un carico di 10
* Servizio C – ha un vincolo di collocamento "NodeType " Type2", segnala un carico di 100
* Servizio D – ha un vincolo di posizionamento "NodeType - Type2", segnala un carico di 100
* E abbiamo quattro nodi. Due di essi hanno NodeType impostato come "Type1" e gli altri due sono "Type2"

E abbiamo il seguente posizionamento:

<center>

![Esempio di posizionamento del sottoclusterSubclustered placement example][Image1]
</center>

Il cluster potrebbe sembrare sbilanciato, abbiamo un grande carico sui nodi 3 e 4, ma questo posizionamento crea il miglior equilibrio possibile in questa situazione.

Resource Manager è in grado di riconoscere le situazioni di sottocluster e in quasi tutti i casi può produrre il saldo ottimale per la determinata situazione.

Per alcune situazioni eccezionali in cui Resource Manager non è in grado di bilanciare in modo ottimale una metrica sottocluster rileverà comunque il sottocluster e genererà un rapporto di integrità per consigliare di risolvere il problema.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Tipi di sottocluster e relative modalità di gestione

Le situazioni di sottocluster possono essere classificate in tre categorie diverse. La categoria di una situazione di sottocluster specifica determina la modalità di gestione da parte di Gestione risorse.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Prima categoria – cluster di sottocluster semplice con gruppi di nodi disgiunti

Questa categoria ha la forma più semplice di sottoclustering in cui i nodi possono essere separati in gruppi diversi e ogni servizio può essere posizionato solo su nodi in uno di questi gruppi. Ogni nodo appartiene a un solo gruppo e a un solo gruppo. La situazione descritta in precedenza appartiene a questa categoria, così come la maggior parte delle situazioni di sottoclustering. 

Per le situazioni in questa categoria, Il Resource Manager può produrre l'equilibrio ottimale e non sono necessari ulteriori interventi.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Seconda categoria – sottoclustering con gruppi di nodi gerarchici

Questa situazione si verifica quando un gruppo di nodi consentiti per un servizio è un sottoinsieme del gruppo di nodi consentiti per un altro servizio. L'esempio più comune di questa situazione è quando un servizio ha un vincolo di posizionamento definito e un altro servizio non ha alcun vincolo di posizionamento e può essere posizionato su qualsiasi nodo.

Esempio:

* Servizio A: nessun vincolo di posizionamentoService A: no placement constraint
* Servizio B: vincolo di collocamento "NodeType-Type1"
* Servizio C: vincolo di collocamento "NodeType/Type2"

Questa configurazione crea una relazione sottoinsieme-superset tra gruppi di nodi per servizi diversi.

<center>

![Sottocluster superset di sottoinsiemi][Image2]
</center>

In questa situazione, c'è la possibilità che venga fatto un equilibrio non ottimale.

Resource Manager riconoscerà questa situazione e produrrà un rapporto di integrità che consiglia di suddividere il servizio A in due servizi: il servizio A1 che può essere inserito nei nodi Type1 e Service A2 che possono essere posizionati nei nodi Type2. Questo ci riporterà alla situazione della prima categoria che può essere bilanciata in modo ottimale.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Terza categoria – sottocluster con sovrapposizione parziale tra set di nodi

Questa situazione si verifica quando esiste una sovrapposizione parziale tra set di nodi in cui è possibile posizionare alcuni servizi.

Ad esempio, se abbiamo una proprietà node denominata NodeColor e abbiamo tre nodi:

* Nodo 1: NodeColor-RedNode 1: NodeColor-Red
* Nodo 2: NodeColor-Blu
* Nodo 2: NodeColor-Verde

E abbiamo due servizi:

* Servizio A: con vincolo di posizionamento "Colore Colore: blu"
* Servizio B: con vincolo di posizionamento "Colore Colore: verde"

Per questo motivo, il servizio A può essere posizionato sui nodi 1 e 2 e il servizio B nei nodi 2 e 3.

In questa situazione, c'è la possibilità che venga fatto un equilibrio non ottimale.

Resource Manager riconoscerà questa situazione e produrrà un rapporto sull'integrità che ti consiglierà di dividere alcuni dei servizi.

Per questa situazione, Resource Manager non è in grado di fornire una proposta su come dividere i servizi, poiché è possibile eseguire più suddivisioni e non è possibile stimare quale modo sarebbe quello ottimale per dividere i servizi.

## <a name="configuring-subclustering"></a>Configurazione del sottocluster

Il comportamento di Gestione risorse in merito al sottocluster può essere modificato modificando i seguenti parametri di configurazione:
* SubclusteringEnabled - parametro determina se Gestione risorse prenderà in considerazione il sottoclustering quando esegue il bilanciamento del carico. Se questo parametro è disattivato, Gestione risorse ignorerà il sottocluster e tenterà di ottenere un equilibrio ottimale a livello globale. Il valore predefinito di questo parametro è false.
* SubclusteringReportingPolicy: determina il modo in cui Gestione risorse genera report di integrità per il sottocluster gerarchico e parziale sovrapposto. Un valore pari a zero indica che i rapporti di integrità relativi al sottocluster sono disattivati, "1" indica che verranno generati report di avviso sull'integrità per situazioni di sottocluster non ottimali e il valore "2" produrrà rapporti di integrità "OK". Il valore predefinito per questo parametro è "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sul modo in cui Cluster Resource Manager gestisce e bilancia il carico nel cluster, vedere l'articolo relativo al [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
* Per informazioni su come i servizi possono essere vincolati per essere posizionati solo su determinati nodi, vedere [Proprietà dei nodi e vincoli](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) di posizionamento

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
