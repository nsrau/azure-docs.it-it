---
title: Mitigazione per la lotta alla z
description: Descrive le tecniche per mitigare gli artefatti di combattimento zDescribes techniques to mitigate z-fighting artifacts
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680453"
---
# <a name="z-fighting-mitigation"></a>Mitigazione per la lotta alla z

Quando due superfici si sovrappongono, non è chiaro quale deve essere eseguito il rendering sopra l'altra. Il risultato varia anche per pixel, generando artefatti dipendenti dalla visualizzazione. Di conseguenza, quando la fotocamera o la mesh si muove, questi modelli sfarfallano notevolmente. Questo artefatto è chiamato *z-fighting*. Per le applicazioni AR e VR, il problema si intensifica perché i dispositivi montati sulla testa si muovono sempre. Per evitare che la funzionalità di mitigazione z per la protezione da z, si impedisca il disagio dello z-fighting mitigation functionality is disponibile in Azure Remote Rendering.

## <a name="z-fighting-mitigation-modes"></a>Modalità di mitigazione per la lotta a z

|Situazione                        | Risultato                               |
|---------------------------------|:-------------------------------------|
|Combattimento z regolare               |![Lotta a z](./media/zfighting-0.png)|
|Per la mitigazione della lotta alla z è abilitata    |![Lotta a z](./media/zfighting-1.png)|
|Evidenziazione della scheda checker board abilitata|![Lotta a z](./media/zfighting-2.png)|

Il codice seguente consente l'attenuazione z-fighting:The following code enables z-fighting mitigation:

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
> L'attenuazione della lotta alla z è un'impostazione globale che influisce su tutte le maglie sottoposte a rendering.

## <a name="reasons-for-z-fighting"></a>Motivi per la lotta z

La lotta a z avviene principalmente per due motivi:

1. quando le superfici sono molto lontane dalla fotocamera, la precisione dei loro valori di profondità si degrada e i valori diventano indistinguibili
1. quando le superfici in una mesh si sovrappongono fisicamente

Il primo problema può sempre accadere ed è difficile da eliminare. Se ciò accade nell'applicazione, assicurarsi che il rapporto tra la distanza del *piano vicino* alla distanza del *piano lontano* sia il più basso possibile. Ad esempio, un piano vicino alla distanza 0,01 e un piano lontano alla distanza 1000 creerà questo problema molto prima, rispetto all'avere il piano vicino a 0,1 e il piano lontano alla distanza 20.

Il secondo problema è un indicatore per il contenuto scritto male. Nel mondo reale, due oggetti non possono trovarsi nello stesso posto contemporaneamente. A seconda dell'applicazione, gli utenti potrebbero voler sapere se esistono superfici sovrapposte e dove si trovano. Ad esempio, una scena CAD di un edificio che è la base per una costruzione del mondo reale, non dovrebbe contenere intersezioni di superficie fisicamente impossibili. Per consentire l'ispezione visiva, è disponibile la modalità di evidenziazione, che visualizza potenziali lotte z come pattern a scacchiera animata.

## <a name="limitations"></a>Limitazioni

La mitigazione z-fighting fornita è uno sforzo migliore. Non vi è alcuna garanzia che rimuova tutti i combattimenti z. Inoltre preferirà automaticamente una superficie rispetto a un'altra. Così, quando si dispone di superfici che sono troppo vicine l'una all'altra, potrebbe accadere che la superficie "sbagliata" finisca in cima. Un caso problematico comune si verifica quando il testo e altre decalcomanie vengono applicate a una superficie. Con la mitigazione z-fighting abilitato questi dettagli potrebbero facilmente svanire.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

* L'abilitazione della mitigazione z riduce un sovraccarico delle prestazioni minimo o nessuno.
* Inoltre, l'abilitazione dell'overlay di z-fighting comporta un sovraccarico delle prestazioni non banale, anche se può variare a seconda della scena.

## <a name="next-steps"></a>Passaggi successivi

* [Modalità di rendering](../../concepts/rendering-modes.md)
* [Riproiezione in fase avanzata](late-stage-reprojection.md)
