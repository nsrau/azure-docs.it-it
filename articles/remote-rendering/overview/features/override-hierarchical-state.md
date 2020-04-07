---
title: Sostituzione dello stato gerarchico
description: Viene illustrato il concetto di componenti di sostituzione dello stato gerarchico.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680791"
---
# <a name="hierarchical-state-override"></a>Sostituzione dello stato gerarchico

In molti casi, è necessario modificare dinamicamente l'aspetto di parti di un [modello,](../../concepts/models.md)ad esempio nascondere sottografi e passare da una parte a un rendering trasparente. La modifica dei materiali di ogni parte coinvolta non è pratica in quanto richiede l'iterazione sull'intero grafico della scena e la gestione della clonazione e dell'assegnazione dei materiali su ogni nodo.

A tale scopo, utilizzare il caso `HierarchicalStateOverrideComponent`con il minor sovraccarico possibile, utilizzare il metodo . Questo componente implementa aggiornamenti dello stato gerarchico su rami arbitrari del grafico della scena. Ciò significa che uno stato può essere definito a qualsiasi livello nel grafico della scena e scorre verso il basso nella gerarchia fino a quando non viene sottoposto a override da un nuovo stato o applicato a un oggetto foglia.

Ad esempio, si consideri il modello di un'auto e si desidera cambiare l'intera vettura in modo che sia trasparente, ad eccezione della parte interna del motore. Questo caso d'uso coinvolge solo due istanze del componente:

* Il primo componente viene assegnato al nodo radice del modello e attiva il rendering trasparente per l'intera vettura.
* Il secondo componente viene assegnato al nodo radice del motore ed esegue nuovamente l'override dello stato disattivando in modo esplicito la modalità di visualizzazione.

## <a name="features"></a>Funzionalità

Il set fisso di stati che possono essere sostituiti sono:

* **Nascosto**: Le maglie rispettive nel grafico della scena sono nascoste o mostrate.
* **Tint color (Colore tinta):** un oggetto sottoposto a rendering può essere colorato con il colore e lo spessore della tinta individuale. L'immagine seguente mostra il colore che si sciolse il bordo di una ruota.
  
  ![Tinta colore](./media/color-tint.png)

* **See-through**: La geometria viene renderizzata in modo semitrasparente, ad esempio per rivelare le parti interne di un oggetto. L'immagine seguente mostra l'intera vettura sottoposta a rendering in modalità di intratpareti, ad eccezione della pinza rossa del freno:

  ![Trasparente](./media/see-through.png)

  > [!IMPORTANT]
  > L'effetto di selezione funziona solo quando viene utilizzata la [modalità](../../concepts/rendering-modes.md) di rendering *TileBasedComposition.*

* **Selezionato**: La geometria viene sottoposta a rendering con un [contorno](outlines.md)di selezione .

  ![Contorno di selezione](./media/selection-outline.png)

* **DisableCollision**: La geometria è esente da [query spaziali.](spatial-queries.md) Il flag **Nascosto** non disattiva le collisioni, pertanto questi due flag sono spesso impostati insieme.

## <a name="hierarchical-overrides"></a>Sostituzioni gerarchiche

L'oggetto `HierarchicalStateOverrideComponent` può essere collegato a più livelli di una gerarchia di oggetti. Poiché può essere presente un solo componente `HierarchicalStateOverrideComponent` di ogni tipo su un'entità, ognuno gestisce gli stati per nascosto, see-through, selezionato, tinta di colore e collisione.

Pertanto ogni stato può essere impostato su uno dei:

* `ForceOn`- lo stato è abilitato per tutte le mesh al di sotto e al di sotto di questo nodo
* `ForceOff`- lo stato è disabilitato per tutte le maglie al di sotto e al di sotto di questo nodo
* `InheritFromParent`- lo stato non è influenzato da questo componente di sostituzione- the state is unaffected by this override component

È possibile modificare gli `SetState` stati direttamente o tramite la funzione:

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

La sostituzione del colore della tinta è leggermente speciale in quanto è presente sia uno stato on/off/inherit che una proprietà di colore tinta. La parte alfa del colore della tinta definisce lo spessore dell'effetto di colorazione: se impostato su 0,0, non è visibile alcun colore di tinta e se impostato su 1,0 l'oggetto verrà sottoposto a rendering con colore di tinta puro. Per i valori intermedi, il colore finale verrà mescolato con il colore della tinta. Il colore della tinta può essere modificato in base al fotogramma per ottenere un'animazione del colore.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Un'istanza `HierarchicalStateOverrideComponent` di se stessa non aggiunge molto sovraccarico di runtime. Tuttavia, è sempre buona norma mantenere basso il numero di componenti attivi. Ad esempio, quando si implementa un sistema di selezione che evidenzia l'oggetto selezionato, si consiglia di eliminare il componente quando l'evidenziazione viene rimossa. Mantenere i componenti con caratteristiche neutre può rapidamente sommarsi.

Il rendering trasparente attribuisce più carico di lavoro alle GPU del server rispetto al rendering standard. Se grandi parti del grafico della scena vengono commutate in *riquadri*visibili, con molti layer di geometria visibili, potrebbe diventare un collo di bottiglia delle prestazioni. Lo stesso vale per gli oggetti con [contorni di selezione.](../../overview/features/outlines.md#performance)

## <a name="next-steps"></a>Passaggi successivi

* [Contorni](../../overview/features/outlines.md)
* [Modalità di rendering](../../concepts/rendering-modes.md)
* [Query spaziali](../../overview/features/spatial-queries.md)
