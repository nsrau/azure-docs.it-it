---
title: Informazioni sul servizio di riconoscimento vocale di Servizi cognitivi di Azure
description: Informazioni su come personalizzare la pronuncia con il servizio di riconoscimento vocale di Servizi cognitivi.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: 93fec1ea78263798588a43b2314ffdea736cdbbc
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745352"
---
# <a name="enable-custom-pronunciation"></a>Abilitare la pronuncia personalizzata
L'uso della pronuncia personalizzata consente di definire la forma fonetica e la visualizzazione di una parola o un termine. È utile per gestire i termini personalizzati, come i nomi di prodotto o gli acronimi. Tutto ciò che serve è un file di pronunce, un semplice file con estensione txt.

e funziona nel modo seguente: In un singolo file TXT è possibile immettere più voci di pronuncia personalizzate. La struttura è analoga alla seguente:

```
Display form <Tab> Spoken form <Newline>
```

La tabella seguente include diversi esempi:

| Forma visualizzata | Forma parlata |
|----------|-------|
| C3PO | ci tre pi o |
| L8R | elle e erre |
| CNTK | ci enne ti kappa|

## <a name="requirements-for-the-spoken-form"></a>Requisiti per la forma parlata
La forma parlata deve essere in minuscolo, cosa che può essere imposta durante l'importazione. Occorre inoltre fornire i controlli nell'utilità di importazione dati. I caratteri di tabulazione non sono consentiti né nella forma parlata né in quella visualizzata. Tuttavia possono essere presenti anche altri caratteri non consentiti (ad esempio ~ e ^).

Ogni file con estensione .txt può avere diverse voci, come illustrato nell'immagine seguente:

![Esempi di pronuncia degli acronimi](media/stt/custom-speech-pronunciation-file.png)

La forma parlata è la sequenza fonetica della forma visualizzata. È composta da lettere, parole o sillabe. Attualmente non sono disponibili ulteriori indicazioni né un set di standard da usare come riferimento per formulare la forma parlata. 

## <a name="supported-pronunciation-characters"></a>Caratteri di pronuncia supportati
La pronuncia personalizzata è al momento supportata solo per l'inglese (en-US) e il tedesco (de-DE). La tabella seguente mostra il set di caratteri che può essere usato per esprimere la forma parlata di un termine nel file di pronunce personalizzato: 

| Linguaggio | Caratteri |
|---------- |----------|
| Inglese (en-Us) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Tedesco (de-de) | ä, ö, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> La forma visualizzata di un termine (in un file di pronunce) deve essere scritta allo stesso modo in un set di dati di adattamento linguistico.

## <a name="requirements-for-the-display-form"></a>Requisiti per la forma visualizzata
Una forma visualizzata può essere costituita solo da una parola, un termine o un acronimo personalizzato oppure da parole composte che combinano parole esistenti. È anche possibile immettere pronunce alternative per parole comuni. 

>[!NOTE]
>Non è consigliabile usare questa funzionalità per riformulare parole comuni o per modificare la forma parlata. È preferibile eseguire il decoder per verificare se alcune parole insolite (ad esempio abbreviazioni, parole tecniche o parole straniere) non sono state decodificate correttamente. Se lo sono state, aggiungerle al file di pronunce personalizzato. Nel modello linguistico è necessario usare sempre e solo la forma visualizzata di una parola. 

## <a name="requirements-for-the-file-size"></a>Requisiti per le dimensioni del file
Le dimensioni del file TXT che contiene le voci di pronuncia sono limitate a 1 megabyte (MB). In genere non è necessario caricare grandi quantità di dati tramite questo file. Le dimensioni della maggior parte dei file di pronunce sono di pochi kilobyte (KB). La codifica del file TXT per tutte le impostazioni locali deve essere UTF-8 BOM. Per le impostazioni locali della lingua inglese è accettabile anche ANSI.

## <a name="next-steps"></a>Passaggi successivi
* Migliorare la precisione del riconoscimento creando un [modello acustico personalizzato](how-to-customize-acoustic-models.md).
* Migliorare la precisione del riconoscimento creando un [modello linguistico personalizzato](how-to-customize-language-model.md).
 