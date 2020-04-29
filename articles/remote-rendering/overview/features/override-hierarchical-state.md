---
title: Override dello stato gerarchico
description: Viene illustrato il concetto di componenti di override dello stato gerarchico.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680791"
---
# <a name="hierarchical-state-override"></a>Override dello stato gerarchico

In molti casi, è necessario modificare dinamicamente l'aspetto delle parti di un [modello](../../concepts/models.md), ad esempio nascondendo sottografici o cambiando parti nel rendering trasparente. Non è possibile modificare i materiali di ogni parte, perché è necessario scorrere l'intero grafico della scena e gestire la clonazione e l'assegnazione di materiali in ogni nodo.

Per eseguire questo caso di utilizzo con il minor sovraccarico possibile, utilizzare `HierarchicalStateOverrideComponent`. Questo componente implementa gli aggiornamenti di stato gerarchico in rami arbitrari del grafico della scena. Ciò significa che uno stato può essere definito a qualsiasi livello nel grafico della scena e che esegue il gocciolamento della gerarchia fino a quando non viene sottoposto a override da un nuovo stato o applicato a un oggetto foglia.

Si consideri, ad esempio, il modello di un'automobile e si desidera passare l'intera automobile a trasparente, ad eccezione della parte del motore interno. Questo caso d'uso riguarda solo due istanze del componente:

* Il primo componente viene assegnato al nodo radice del modello e attiva il rendering trasparente per l'intera automobile.
* Il secondo componente viene assegnato al nodo radice del motore e sostituisce di nuovo lo stato disattivando in modo esplicito la modalità di visualizzazione.

## <a name="features"></a>Caratteristiche

Il set fisso di Stati di cui è possibile eseguire l'override sono:

* **Nascosto**: le mesh corrispondenti nel grafico della scena sono nascoste o visualizzate.
* **Colore tinta**: un oggetto di cui è stato eseguito il rendering può essere colorato con il colore della tinta singola e il peso della tinta. L'immagine seguente mostra la colorazione del bordo di una rotellina.
  
  ![Tinta del colore](./media/color-tint.png)

* **See-through**: la geometria viene sottoposta a rendering semi-trasparente, ad esempio per rivelare le parti interne di un oggetto. Nell'immagine seguente viene mostrata l'intera automobile sottoposta a rendering in modalità di visualizzazione, ad eccezione della pinza del freno rossa:

  ![Vedere](./media/see-through.png)

  > [!IMPORTANT]
  > L'effetto See-through funziona solo quando viene usata la [modalità di rendering](../../concepts/rendering-modes.md) *TileBasedComposition* .

* **Selezionato**: la geometria viene sottoposta a rendering con una [struttura di selezione](outlines.md).

  ![Struttura di selezione](./media/selection-outline.png)

* **DisableCollision**: la geometria è esentata dalle [query spaziali](spatial-queries.md). Il flag **Hidden** non disattiva i conflitti, quindi questi due flag vengono spesso impostati insieme.

## <a name="hierarchical-overrides"></a>Sostituzioni gerarchiche

Il `HierarchicalStateOverrideComponent` può essere collegato a più livelli di una gerarchia di oggetti. Poiché può essere presente solo un componente di ogni tipo in un'entità, ognuno `HierarchicalStateOverrideComponent` gestisce gli Stati per le proprietà nascoste, see-through, Selected, color e Collision.

Pertanto, ogni stato può essere impostato su uno dei seguenti:

* `ForceOn`-lo stato è abilitato per tutti i mesh al di sotto di questo nodo
* `ForceOff`-lo stato è disabilitato per tutte le mesh al di sotto di questo nodo
* `InheritFromParent`-lo stato non è interessato da questo componente di sostituzione

È possibile modificare gli stati direttamente o tramite `SetState` la funzione:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>Colore tinta

L'override del colore di tinta è leggermente speciale in quanto è disponibile uno stato on/off/inherit e una proprietà Color tinta. La parte alfa del colore tinta definisce il peso dell'effetto colorazione: se è impostato su 0,0, non è visibile alcun colore tinta e se è impostato su 1,0 verrà eseguito il rendering dell'oggetto con il colore tinta pura. Per i valori in-between, il colore finale verrà combinato con il colore della tinta. Il colore della tinta può essere modificato in base ai singoli fotogrammi per ottenere un'animazione colori.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Un'istanza di `HierarchicalStateOverrideComponent` se stessa non aggiunge molto sovraccarico in fase di esecuzione. Tuttavia, è sempre consigliabile evitare che il numero di componenti attivi sia basso. Ad esempio, quando si implementa un sistema di selezione che evidenzia l'oggetto selezionato, è consigliabile eliminare il componente quando l'evidenziazione viene rimossa. Il mantenimento dei componenti con funzionalità neutre può essere aggiunto rapidamente.

Il rendering trasparente mette un carico di lavoro maggiore sulle GPU del server rispetto al rendering standard. Se le parti grandi del grafico della scena vengono passate a *See-through*, con molti livelli di geometria visibili, potrebbe diventare un collo di bottiglia delle prestazioni. Lo stesso valore è valido per gli oggetti con [strutture di selezione](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Passaggi successivi

* [Contorni](../../overview/features/outlines.md)
* [Modalità di rendering](../../concepts/rendering-modes.md)
* [Query spaziali](../../overview/features/spatial-queries.md)
