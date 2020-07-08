---
title: Mitigazione dell'effetto Z-fighting
description: Descrive le tecniche per mitigare gli artefatti z-fighting
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: f4c49be5f5a0f2c89831891dc2640b64fee9fc44
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022419"
---
# <a name="z-fighting-mitigation"></a>Mitigazione dell'effetto Z-fighting

Quando due superfici si sovrappongono, non è chiaro di quale deve essere eseguito il rendering sull'altra. Il risultato varia anche per ogni pixel, ottenendo gli artefatti dipendenti dalla visualizzazione della fotocamera. Di conseguenza, quando la camera o la mesh si sposta, questi modelli emettono uno sfarfallio evidente. Questo artefatto è detto *z-fighting*. Per le applicazioni di realtà aumentata e realtà virtuale il problema è più accentuato, perché, com'è naturale, i caschi si muovono sempre. Per evitare disagio a chi guarda, è disponibile una funzionalità di mitigazione dell'effetto z-fighting in Rendering remoto di Azure.

## <a name="z-fighting-mitigation-modes"></a>Modalità di mitigazione dell'effetto Z-fighting

|Situazione                        | Risultato                               |
|---------------------------------|:-------------------------------------|
|Effetto z-fighting normale               |![Z-fighting](./media/zfighting-0.png)|
|Mitigazione dell'effetto Z-fighting abilitata    |![Z-fighting](./media/zfighting-1.png)|
|Evidenziazione dell'effetto scacchiera abilitata|![Z-fighting](./media/zfighting-2.png)|

Il codice seguente abilita la funzionalità di mitigazione dell'effetto z-fighting:

```cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = *session->Actions()->ZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->Enabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->Highlighting(highlight);
}
```


> [!NOTE]
> La funzionalità di mitigazione dell'effetto z-fighting è un'impostazione globale che influisce su tutte le mesh sottoposte a rendering.

## <a name="reasons-for-z-fighting"></a>Motivi dell'effetto z-fighting

L'effetto z-fighting si verifica principalmente in due casi:

1. Quando le superfici sono molto distanti dalla camera, la precisione dei valori di profondità diminuisce e i valori diventano indistinguibili
1. Quando le superfici in una mesh si sovrappongono fisicamente

Il primo problema può sempre verificarsi ed è difficile da eliminare. Se si verifica nell'applicazione, assicurarsi che il rapporto tra la distanza del *piano vicino* e la distanza del *piano distante* sia il più basso possibile. Ad esempio, un piano vicino con distanza 0,01 e un piano distante con distanza 1000 creeranno questo problema molto prima rispetto a un piano vicino con distanza 0,1 e un piano distante con distanza 20.

Il secondo problema indica che il contenuto non è stato creato correttamente. Nel mondo reale, due oggetti non possono trovarsi nello stesso luogo nello stesso momento. A seconda dell'applicazione, è possibile che gli utenti vogliano sapere se esistono superfici sovrapposte e dove si trovano. Una scena CAD di un edificio che costituisce la base per una costruzione reale, ad esempio, non deve contenere intersezioni di superfici fisicamente impossibili. Per consentire l'ispezione visiva, è disponibile la modalità di evidenziazione, che visualizza i casi di effetto z-fighting potenziali sotto forma di motivo a scacchi animato.

## <a name="limitations"></a>Limitazioni

La funzionalità di mitigazione dell'effetto z-fighting è il risultato del principio del massimo sforzo. Non garantisce l'eliminazione di tutti i casi di effetto z-fighting e preferisce automaticamente una superficie rispetto a un'altra. Quando sono presenti superfici troppo vicine, pertanto, è possibile che la superficie posizionata sopra non sia quella corretta. Un caso comune in cui il problema si verifica è quando su una superficie sono applicati testo e altre decalcomanie. Con la funzionalità di mitigazione dell'effetto z-fighting abilitata questi dettagli potrebbero facilmente scomparire.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

* L'abilitazione della funzionalità di mitigazione dell'effetto z-fighting comporta un sovraccarico delle prestazioni minimo o nullo.
* L'abilitazione aggiuntiva della sovraimpressione z-fighting comporta un sovraccarico delle prestazioni non indifferente, anche se variabile a seconda della scena.

## <a name="next-steps"></a>Passaggi successivi

* [Modalità di rendering](../../concepts/rendering-modes.md)
* [Riproiezione con ritardo della fase](late-stage-reprojection.md)
