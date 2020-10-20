---
title: Override dello stato gerarchico
description: Descrizione del concetto di componenti di override dello stato gerarchico.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: bb120a533e4d11b34bb9712bf0164cec5a7728ce
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207734"
---
# <a name="hierarchical-state-override"></a>Override dello stato gerarchico

In molti casi, è necessario modificare dinamicamente l'aspetto delle parti di un [modello](../../concepts/models.md), ad esempio nascondendo sottografici o cambiando parti nel rendering trasparente. La modifica dei materiali di ogni parte coinvolta non è consigliabile perché richiede di eseguire l'iterazione sull’intero grafo della scena e gestire la clonazione e l’assegnazione dei materiali su ogni nodo.

Per completare questo caso d'uso con il minor sovraccarico possibile, utilizzare `HierarchicalStateOverrideComponent`. Questo componente implementa gli aggiornamenti dello stato gerarchico in rami arbitrari del grafo della scena. Ciò significa che uno stato può essere definito a qualsiasi livello nel grafo della scena e scende a cascata nella gerarchia fino a quando non viene sostituito da un nuovo stato o applicato a un oggetto foglia.

Nel caso del modello di un'automobile, ad esempio, è possibile rendere l’intera automobile trasparente, ad eccezione della parte interna del motore. Questo caso d'uso coinvolge solo due istanze del componente:

* Il primo componente viene assegnato al nodo radice del modello e attiva il rendering trasparente per l'intera automobile.
* Il secondo componente viene assegnato al nodo radice del motore e sostituisce di nuovo lo stato disattivando in modo esplicito la modalità trasparente.

## <a name="features"></a>Funzionalità

Il set fisso di stati di cui è possibile eseguire l'override sono:

* **`Hidden`**: Le maglie corrispondenti nel grafico della scena sono nascoste o visualizzate.
* **`Tint color`**: Un oggetto di cui è stato eseguito il rendering può essere colorato con il colore della tinta e il peso della tinta singoli. L'immagine seguente mostra la colorazione del cerchione di una ruota.
  
  ![Colore tinta utilizzato per trasformare un oggetto in verde](./media/color-tint.png)

* **`See-through`**: La geometria viene sottoposta a rendering in modo semi trasparente, ad esempio per rivelare le parti interne di un oggetto. Nell'immagine seguente viene mostrata l'intera automobile sottoposta a rendering in modalità trasparente, ad eccezione della pinza del freno rossa:

  ![Modalità See-through utilizzata per rendere trasparente gli oggetti selezionati](./media/see-through.png)

  > [!IMPORTANT]
  > L'effetto trasparente funziona solo quando viene usata la [modalità di rendering](../../concepts/rendering-modes.md) *TileBasedComposition*.

* **`Selected`**: La geometria viene sottoposta a rendering con una [struttura di selezione](outlines.md).

  ![Opzione di struttura utilizzata per evidenziare una parte selezionata](./media/selection-outline.png)

* **`DisableCollision`**: La geometria è esentata dalle [query spaziali](spatial-queries.md). Il **`Hidden`** flag non influisce sul flag di stato di collisione, quindi questi due flag vengono spesso impostati insieme.

* **`UseCutPlaneFilterMask`**: Usare una maschera di bit filtro singola per controllare la selezione del piano di taglio. Questo flag determina se la singola maschera di filtro deve essere utilizzata o ereditata dal relativo elemento padre. La maschera di bit del filtro viene impostata tramite la `CutPlaneFilterMask` Proprietà. Per informazioni dettagliate sul funzionamento del filtro, vedere il [paragrafo selettivo dei piani di taglio](cut-planes.md#selective-cut-planes).
![Piani tagliati selettivi](./media/selective-cut-planes.png)


> [!TIP]
> In alternativa alla disattivazione delle query spaziali e di visibilità per un sottografico completo, `enabled` è possibile attivare/disattivare lo stato di un oggetto gioco. Se una gerarchia è disabilitata, avrà la preferenza su Any `HierarchicalStateOverrideComponent` .

## <a name="hierarchical-overrides"></a>Override gerarchici

`HierarchicalStateOverrideComponent` può essere collegato a più livelli di una gerarchia di oggetti. Poiché può essere presente solo un componente di ciascun tipo in un'entità, ogni `HierarchicalStateOverrideComponent` gestisce gli stati per hidden, see-through, selected, color tint e collision.

Pertanto, ogni stato può essere impostato su uno dei seguenti:

* `ForceOn` - lo stato è abilitato per tutte le mesh su questo nodo e sotto di esso
* `ForceOff` - lo stato è disabilitato per tutte le mesh su questo nodo e sotto di esso
* `InheritFromParent` - lo stato non è interessato da questo componente di override

È possibile modificare gli stati direttamente o tramite la funzione `SetState`:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Colore tinta

L' `tint color` override è leggermente speciale in quanto è disponibile uno stato on/off/inherit e una proprietà Color tinta. La parte alfa del colore tinta definisce lo spessore dell'effetto di colorazione: se è impostato su 0,0, non è visibile nessun colore tinta, mentre se è impostato su 1,0 verrà eseguito il rendering dell'oggetto con il colore tinta puro. Per i valori intermedi, il colore finale verrà combinato con il colore tinta. Il colore tinta può essere modificato per i singoli frame per ottenere un'animazione di colori.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Un'istanza di `HierarchicalStateOverrideComponent` non aggiunge molto sovraccarico in fase di esecuzione. Tuttavia, è sempre consigliabile fare in modo che il numero di componenti attivi sia basso. Ad esempio, quando si implementa un sistema di selezione che evidenzia l'oggetto selezionato, è consigliabile eliminare il componente quando l'evidenziazione viene rimossa. Il mantenimento di componenti con funzionalità neutre può aggiungere rapidamente sovraccarico.

Il rendering trasparente aggiunge un carico di lavoro maggiore sulle GPU del server rispetto al rendering standard. Se parti di grandi dimensioni del grafo della scena sono impostate come *see-through*, con molti livelli di geometria visibili, si potrebbe verificare un collo di bottiglia nelle prestazioni. Lo stesso vale per gli oggetti con [strutture di selezione](../../overview/features/outlines.md#performance).

## <a name="api-documentation"></a>Documentazione dell'API

* [C# HierarchicalStateOverrideComponent (classe)](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [Classe C++ HierarchicalStateOverrideComponent](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>Passaggi successivi

* [Strutture](../../overview/features/outlines.md)
* [Modalità di rendering](../../concepts/rendering-modes.md)
* [Query spaziali](../../overview/features/spatial-queries.md)