---
title: Mitigazione dell'effetto Z-fighting
description: Descrive le tecniche per mitigare gli artefatti di combattimento z
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680453"
---
# <a name="z-fighting-mitigation"></a>Mitigazione dell'effetto Z-fighting

Quando due superfici si sovrappongono, non è chiaro quale eseguire il rendering sopra l'altra. Il risultato varia anche per ogni pixel, ottenendo gli artefatti dipendenti dalla visualizzazione. Di conseguenza, quando la fotocamera o la mesh viene spostata, questi modelli si sfarfallano in modi evidenti. Questo artefatto viene chiamato *z-Fighting*. Per le applicazioni AR e VR, il problema si intensifica perché i dispositivi montati in modo naturale si muovono sempre. Per evitare il disagio del visualizzatore, la funzionalità di mitigazione per il combattimento z è disponibile nel rendering remoto di Azure.

## <a name="z-fighting-mitigation-modes"></a>Modalità di mitigazione del combattimento Z

|Situazione                        | Risultato                               |
|---------------------------------|:-------------------------------------|
|Normale combattimento z               |![Combattimento Z](./media/zfighting-0.png)|
|La mitigazione dei riscontri Z abilitata    |![Combattimento Z](./media/zfighting-1.png)|
|Evidenziazione della scacchiera abilitata|![Combattimento Z](./media/zfighting-2.png)|

Il codice seguente consente la mitigazione del combattimento z:

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> La mitigazione del combattimento Z è un'impostazione globale che influiscono su tutte le mesh sottoposte a rendering.

## <a name="reasons-for-z-fighting"></a>Motivi per il combattimento z

Il combattimento Z si verifica principalmente per due motivi:

1. Quando le superfici sono molto distanti dalla fotocamera, la precisione dei valori di profondità diminuisce e i valori diventano indistinguibili
1. Quando le superfici in una mesh si sovrappongono fisicamente

Il primo problema può sempre verificarsi ed è difficile da eliminare. Se questa situazione si verifica nell'applicazione, assicurarsi che il rapporto tra la distanza del *piano vicino* e la distanza del *piano* più basso sia il più basso possibile. Ad esempio, un piano vicino a distanza 0,01 e un piano lontano a distanza 1000 creeranno questo problema molto prima, rispetto al piano vicino 0,1 e al piano lontano a distanza 20.

Il secondo problema è un indicatore di contenuto non corretto. Nel mondo reale, due oggetti non possono trovarsi nello stesso punto nello stesso momento. A seconda dell'applicazione, è possibile che gli utenti desiderino sapere se esistono superfici sovrapposte e dove si trovano. Una scena CAD di un edificio che costituisce la base per una costruzione reale, ad esempio, non deve contenere le intersezioni di superficie fisicamente impossibili. Per consentire l'ispezione visiva, è disponibile la modalità di evidenziazione, che mostra il potenziale combattimento z come motivo a scacchi animato.

## <a name="limitations"></a>Limitazioni

La mitigazione del combattimento z è uno dei migliori sforzi. Non vi è alcuna garanzia che elimini tutti i riscontri z. Inoltre, si preferisce automaticamente una superficie rispetto a un'altra. Pertanto, quando sono presenti superfici troppo vicine, è possibile che la superficie "errata" si trovi nella parte superiore. Un caso di problema comune è quando il testo e le altre decalcomanie vengono applicati a una superficie. Con la mitigazione della lotta z abilitata, questi dettagli potrebbero semplicemente scomparire.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

* L'abilitazione della mitigazione del combattimento z non comporta un sovraccarico delle prestazioni.
* Inoltre, l'abilitazione della sovrimpressione z-Fighting comporta un sovraccarico delle prestazioni non semplice, sebbene possa variare a seconda della scena.

## <a name="next-steps"></a>Passaggi successivi

* [Modalità di rendering](../../concepts/rendering-modes.md)
* [Riproiezione con ritardo della fase](late-stage-reprojection.md)
