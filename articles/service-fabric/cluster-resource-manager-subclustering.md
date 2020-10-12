---
title: Bilanciamento delle metriche sottocluster
description: Effetti dei vincoli di posizionamento sul bilanciamento del carico e su come gestirli
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82183118"
---
# <a name="balancing-of-subclustered-metrics"></a>Bilanciamento delle metriche sottocluster

## <a name="what-is-subclustering"></a>Informazioni sul sottoclustering

Il sottoclustering si verifica quando i servizi con vincoli di posizionamento diversi hanno una metrica comune ed entrambi ne segnalano il carico. Se il carico segnalato dai servizi differisce in modo significativo, il carico totale sui nodi avrà una deviazione standard di grandi dimensioni e sembrerebbe come il cluster è sbilanciato, anche quando è disponibile il migliore equilibrio possibile.

## <a name="how-subclustering-affects-load-balancing"></a>Impatto del sottoclustering sul bilanciamento del carico

Se il carico segnalato dai servizi in diversi nodi differisce significativamente, è possibile che si verifichi un notevole squilibrio in cui non è presente alcun tipo. Inoltre, se il falso squilibrio causato dal sottoclustering è maggiore dello squilibrio effettivo, è possibile confondere il Gestione risorse algoritmo di bilanciamento del carico e produrre un saldo non ottimale nel cluster.

Si immagini, ad esempio, di disporre di quattro servizi che segnalano un carico per la metrica Metric1:

* Il servizio A – ha un vincolo di posizionamento "NodeType = = frontend", segnala un carico di 10
* Il servizio B: ha un vincolo di posizionamento "NodeType = = frontend", segnala un carico di 10
* Il servizio C – ha un vincolo di posizionamento "NodeType = = backend", segnala un carico di 100
* Il servizio D: ha un vincolo di posizionamento "NodeType = = backend", segnala un carico di 100
* E sono disponibili quattro nodi. Due di esse hanno NodeType impostato come "frontend" e le altre due sono "backend"

E abbiamo la posizione seguente:

<center>

![Esempio di selezione host sottocluster][Image1]
</center>

Il cluster può sembrare sbilanciato, il carico dei nodi 3 e 4 è elevato, ma questo posizionamento crea il migliore equilibrio possibile in questa situazione.

Gestione risorse è in grado di riconoscere le situazioni del sottoclustering e in quasi tutti i casi può produrre il giusto equilibrio per la situazione specificata.

Per alcune situazioni eccezionali in cui Gestione risorse non è in grado di bilanciare in modo ottimale una metrica del sottocluster, verrà comunque rilevato il sottoclustering e verrà generato un report sull'integrità per consigliare la risoluzione del problema.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Tipi di sottoclustering e come vengono gestiti

Le situazioni del sottoclustering possono essere classificate in tre categorie diverse. La categoria di una specifica situazione del sottoclustering determina il modo in cui verrà gestita da Gestione risorse.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Prima categoria: sottoclustering flat con gruppi di nodi non contigui

Questa categoria presenta la forma più semplice di sottoclustering, in cui i nodi possono essere separati in gruppi diversi e ogni servizio può essere inserito solo nei nodi di uno di questi gruppi. Ogni nodo appartiene a un solo gruppo e a un solo gruppo. La situazione descritta in precedenza appartiene a questa categoria come la maggior parte delle situazioni del sottoclustering. 

Per le situazioni in questa categoria, il Gestione risorse può produrre il saldo ottimale e non è necessario ulteriore intervento.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Seconda categoria: sottoclustering con gruppi di nodi gerarchici

Questa situazione si verifica quando un gruppo di nodi consentiti per un servizio è un subset del gruppo di nodi consentito per un altro servizio. L'esempio più comune di questa situazione si verifica quando per un servizio è stato definito un vincolo di posizionamento e un altro servizio non ha vincoli di posizionamento e può essere inserito in qualsiasi nodo.

Esempio:

* Servizio A: nessun vincolo di posizionamento
* Servizio B: vincolo di posizionamento "NodeType = = front-end"
* Servizio C: vincolo di posizionamento "NodeType = = backend"

Questa configurazione crea una relazione subset-superset tra i gruppi di nodi per i diversi servizi.

<center>

![Sottocluster superset subset][Image2]
</center>

In questa situazione, è possibile che venga creato un saldo non ottimale.

Gestione risorse riconoscerà questa situazione e produrrà un report sull'integrità che consiglia di dividere il servizio A in due servizi: il servizio a1 che può essere inserito nei nodi front-end e nel servizio a2 che può essere inserito nei nodi back-end. Questa operazione riporta alla prima situazione di categoria che può essere bilanciata in modo ottimale.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Terza categoria: sottoclustering con sovrapposizione parziale tra i set di nodi

Questa situazione si verifica in presenza di una sovrapposizione parziale tra i set di nodi su cui è possibile collocare alcuni servizi.

Se, ad esempio, è presente una proprietà Node denominata NodeColor e sono presenti tre nodi:

* Nodo 1: NodeColor = rosso
* Nodo 2: NodeColor = blu
* Nodo 2: NodeColor = verde

Sono disponibili due servizi:

* Servizio A: con vincolo di posizionamento "colore = = rosso | | Colore = = blu "
* Servizio B: con vincolo di posizionamento "color = = Blue | | Color = = verde "

Per questo motivo, il servizio A può essere inserito nei nodi 1 e 2 e il servizio B può essere inserito nei nodi 2 e 3.

In questa situazione, è possibile che venga creato un saldo non ottimale.

Gestione risorse riconoscerà questa situazione e produrrà un report sull'integrità che consiglia di suddividere alcuni dei servizi.

Per questa situazione, il Gestione risorse non è in grado di fornire una proposta come suddividere i servizi, dal momento che è possibile eseguire più divisioni e non esiste alcun modo per stimare quale sia il modo ottimale per suddividere i servizi.

## <a name="configuring-subclustering"></a>Configurazione del sottoclustering

Il comportamento di Gestione risorse sul sottoclustering può essere modificato modificando i parametri di configurazione seguenti:
* SubclusteringEnabled-Parameter determina se Gestione risorse prenderà in considerazione il sottoclustering durante il bilanciamento del carico. Se questo parametro è disattivato, Gestione risorse ignorerà il sottoclustering e tenterà di ottenere un equilibrio ottimale a livello globale. Il valore predefinito di questo parametro è false.
* SubclusteringReportingPolicy: determina il modo in cui Gestione risorse emetterà report sull'integrità per il sottoclustering gerarchico e parzialmente sovrapposto. Un valore pari a zero indica che i report sull'integrità sul sottoclustering sono spenti, "1" significa che i report sull'integrità degli avvisi verranno generati per le situazioni di sottoclustering non ottimali e il valore "2" produrrà report sull'integrità "OK". Il valore predefinito per questo parametro è "1".

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
* Per informazioni sul modo in cui i servizi possono essere vincolati solo in determinati nodi, vedere Proprietà dei [nodi e vincoli di posizionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
