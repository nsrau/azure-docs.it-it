---
title: Moderazione immagine - Content Moderator
titleSuffix: Azure Cognitive Services
description: Usa la moderazione delle immagini assistita da computer di Content Moderator e lo strumento di revisione umana in loop per moderare le immagini per contenuti per adulti e per gli utenti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: fe76e32bfd9b1734f3c84a400f897b7af7e3168b
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85800996"
---
# <a name="learn-image-moderation-concepts"></a>Informazioni sui concetti di moderazione delle immagini

USA [lo strumento](Review-Tool-User-Guide/human-in-the-loop.md) per la moderazione e la revisione delle immagini assistite da un content moderator per moderare le immagini per contenuti per adulti e in modo audace. Analizzare le immagini per trovare i contenuti di testo, estrarre il testo e rilevare i caratteri tipografici. È possibile abbinare le immagini agli elenchi personalizzati e intraprendere altre azioni.

## <a name="evaluating-for-adult-and-racy-content"></a>Valutazione dei contenuti spinti e per adulti

L'operazione **Valuta** restituisce un punteggio di attendibilità compreso tra 0 e 1. Restituisce inoltre dati booleani uguali a true o false. Questi valori stimano se l'immagine contiene un potenziale contenuto spinto o per adulti. Quando si chiama l'API con la propria immagine (file o URL), la risposta restituita include le informazioni seguenti:

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - `isImageAdultClassified` rappresenta la potenziale presenza di immagini che potrebbero essere considerate sessualmente esplicite o per adulti in determinate situazioni.
> - `isImageRacyClassified` rappresenta la potenziale presenza di immagini che potrebbero essere considerate sessualmente suggestive o per maggiorenni in determinate situazioni.
> - I punteggi sono compresi tra 0 e 1. Maggiore è il punteggio, più alto è il modello che prevede che la categoria possa essere applicabile. Questa anteprima si basa su un modello statistico piuttosto che su risultati codificati manualmente. Si consiglia di eseguire i test con i propri contenuti per determinare come ogni categoria si allinei alle proprie esigenze.
> - I valori booleani sono true o false a seconda delle soglie interne del punteggio. I clienti devono valutare se usare questo valore o optare per le soglie personalizzate in base ai propri criteri relativi ai contenuti.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Rilevamento del testo con il riconoscimento ottico dei caratteri (OCR)

L'operazione **Riconoscimento ottico dei caratteri OCR** consente di stimare la presenza di contenuti di testo in un'immagine e li estrae anche per moderarne il testo. È possibile specificare la lingua. Se non si specifica una lingua, la lingua predefinita per il rilevamento è l'inglese.

La risposta include le informazioni seguenti:
- Il testo originale.
- Gli elementi di testo rilevati con i punteggi di attendibilità.

Esempio di estratto:

```json
"TextDetection": {
    "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
    },
    .........
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
},
```

## <a name="detecting-faces"></a>Rilevamento dei visi

Il rilevamento dei visi consente di rilevare i dati personali, ad esempio i visi nelle immagini. Vengono rilevati i potenziali visi e il numero dei possibili visi presenti in ogni immagine.

Una risposta contiene queste informazioni:

- Conteggio dei visi
- Elenco delle posizioni dei visi rilevati

Esempio di estratto:

```json
"FaceDetection": {
    ......
    "result": true,
    "count": 2,
    "advancedInfo": [
        .....
    ],
    "faces": [
        {
            "bottom": 598,
            "left": 44,
            "right": 268,
            "top": 374
        },
        {
            "bottom": 620,
            "left": 308,
            "right": 532,
            "top": 396
        }
    ]
}
```

## <a name="creating-and-managing-custom-lists"></a>Creazione e gestione di elenchi personalizzati

In molte community online, una volta che gli utenti hanno caricato immagini o altri tipi di contenuti, gli elementi offensivi possono essere condivisi più volte nei giorni, nelle settimane e nei mesi successivi. I costi per l'analisi e il filtraggio ripetuti della stessa immagine o anche di versioni leggermente modificate dell'immagine da più punti possono risultare costosi e soggetti a errori.

Anziché moderare più volte la stessa immagine, aggiungere le immagini offensive all'elenco personalizzato dei contenuti bloccati. In questo modo, il sistema di moderazione del contenuto confronta le immagini in ingresso rispetto agli elenchi personalizzati e arresta altre elaborazioni.

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di immagini** e ogni elenco **non può includere più di 10.000 immagini**.
>

Content Moderator fornisce un'[API di gestione degli elenchi di immagini](try-image-list-api.md) completa con le operazioni per la gestione degli elenchi di immagini personalizzate. Iniziare con la [Console dell'API per elenchi di immagini](try-image-list-api.md) e usare gli esempi di codice API REST. Estrarre anche l'[avvio repido degli elenchi di immagini in .NET](image-lists-quickstart-dotnet.md) se si ha familiarità con Visual Studio e C#.

## <a name="matching-against-your-custom-lists"></a>Confronto con gli elenchi personalizzati

L'operazione Match (Corrispondenza) consente di effettuare la corrispondenza fuzzy delle immagini in ingresso con uno degli elenchi personalizzati, creati e gestiti con le operazioni elenco.

Se viene trovata una corrispondenza, l'operazione restituisce l'identificatore e i tag di moderazione dell'immagine corrispondente. La risposta contiene queste informazioni:

- Punteggio di corrispondenza (compreso tra 0 e 1)
- Immagine corrispondente
- Tag di immagine (assegnati durante la moderazione precedente)
- Etichette di immagine

Esempio di estratto:

```json
{
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
}
```

## <a name="review-tool"></a>Strumento di revisione

Per i casi più sfumati, usare lo [strumento di revisione](Review-Tool-User-Guide/human-in-the-loop.md) content moderator e la relativa API per visualizzare i risultati della moderazione e il contenuto nella revisione per i moderatori umani. Revisionano i tag assegnati automaticamente e confermano le loro decisioni finali.

![Revisione di immagini per moderatori umani](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Passaggi successivi

Eseguire il test drive della [Console dell'API Moderazione immagini](try-image-api.md) e usare gli esempi di codice API REST. Vedere anche [recensioni, flussi di lavoro e processi](./review-api.md) per apprendere come configurare le recensioni umane.
