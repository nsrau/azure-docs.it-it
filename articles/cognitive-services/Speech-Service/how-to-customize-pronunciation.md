---
title: Personalizzare la pronuncia - Servizi Voce
titlesuffix: Azure Cognitive Services
description: Informazioni su come personalizzare la pronuncia con il servizio Voce. Con la pronuncia personalizzata è possibile definire la forma fonetica e visualizzare una parola o un termine. È utile per gestire i termini personalizzati, come i nomi di prodotto o gli acronimi. Tutto ciò che serve è un file di pronunce, un semplice file con estensione TXT.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: f825cf8f381a7a2974b150a74a091412b24b09bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863760"
---
# <a name="enable-custom-pronunciation"></a>Abilitare la pronuncia personalizzata

Usando la pronuncia personalizzata, è possibile definire i form fonetiche e la visualizzazione di una parola o un termine (acronimo). È utile per gestire i termini personalizzati, come i nomi di prodotto o gli acronimi. Tutto ciò che serve è un file di pronunce, un semplice file con estensione TXT.

e funziona nel modo seguente: In un singolo file TXT è possibile immettere più voci di pronuncia personalizzate. La struttura è analoga alla seguente:

```
Display form <Tab> Spoken form <Newline>
```

La tabella seguente include diversi esempi:

| Forma visualizzata | Forma parlata |
|----------|-------|
| 3CPO | ci tre pi o |
| L8R | elle e erre |
| CNTK | k c n t|

## <a name="requirements-for-the-spoken-form"></a>Requisiti per la forma parlata

La forma parlata deve essere in minuscolo, cosa che può essere imposta durante l'importazione. Occorre inoltre fornire i controlli nell'utilità di importazione dati. I caratteri di tabulazione non sono consentiti né nella forma parlata né in quella visualizzata. Tuttavia possono essere presenti anche altri caratteri non consentiti (ad esempio ~ e ^).

Ogni file con estensione .txt può avere diverse voci, come illustrato nell'immagine seguente:

![Esempi di pronuncia degli acronimi](media/stt/custom-speech-pronunciation-file.png)

La forma parlata è la sequenza fonetica della forma visualizzata. È composta da lettere, parole o sillabe. Attualmente non sono disponibili ulteriori indicazioni né un set di standard da usare come riferimento per formulare la forma parlata.

## <a name="supported-pronunciation-characters"></a>Caratteri di pronuncia supportati

La pronuncia personalizzata è al momento supportata solo per l'inglese (en-US) e il tedesco (de-DE). La tabella seguente mostra il set di caratteri che può essere usato per esprimere la forma parlata di un termine nel file di pronunce personalizzato:

| Linguaggio | Caratteri |
|---------- |----------|
| Inglese (en-Us) | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Tedesco (de-de) | ä, ö, ü, ?, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> La forma visualizzata di un termine (in un file di pronunce) deve essere scritta allo stesso modo in un set di dati di adattamento linguistico.

## <a name="requirements-for-the-display-form"></a>Requisiti per la forma visualizzata

Un modulo di visualizzazione può essere solo una parola personalizzata, l'acronimo o parole composte che combinano parole esistente.

>[!NOTE]
>Non è consigliabile usare questa funzionalità per riformulare parole comuni o per modificare la forma parlata. È un controllo migliore se alcune parole insolite (ad esempio le abbreviazioni, termini tecnici o parole straniere) vengono erroneamente transribed prima di questa funzionalità viene usata. Se lo sono state, aggiungerle al file di pronunce personalizzato. Nel modello linguistico è necessario usare sempre e solo la forma visualizzata di una parola.

## <a name="requirements-for-the-file-size"></a>Requisiti per le dimensioni del file
Le dimensioni del file con estensione TXT che contiene le voci di pronuncia sono limitate a 1 megabyte (1 KB per le chiavi di livello gratuito). In genere non è necessario caricare grandi quantità di dati tramite questo file. Le dimensioni della maggior parte dei file di pronunce sono di pochi kilobyte (KB). La codifica del file TXT per tutte le impostazioni locali deve essere UTF-8 BOM. Per le impostazioni locali della lingua inglese è accettabile anche ANSI.

## <a name="next-steps"></a>Passaggi successivi
* Migliorare la precisione del riconoscimento creando un [modello acustico personalizzato](how-to-customize-acoustic-models.md).
* Migliorare la precisione del riconoscimento creando un [modello linguistico personalizzato](how-to-customize-language-model.md).
