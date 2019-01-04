---
title: Riepilogo delle guide introduttive di Visione artificiale
titleSuffix: Azure Cognitive Services
description: In queste guide introduttive si analizza un'immagine, si crea un'anteprima e si estrae testo stampato e scritto a mano usando l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 645c57c9bfeaefcfd2fc50b2dd431713fffaa42b
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53578785"
---
# <a name="quickstart-summary"></a>Avvio rapido: Summary

Queste guide introduttive forniscono informazioni ed esempi di codice per iniziare rapidamente a usare il servizio Visione artificiale.

Gli esempi eseguono chiamate HTTP dirette all'API Visione artificiale. Vedere la sezione *Guide introduttive per SDK* per visualizzare esempi in cui vengono usate librerie client di Visione artificiale che forniscono metodi pratici per il wrapping delle chiamate HTTP.

Per sperimentare rapidamente i metodi dell'API Visione artificiale, provare la [console di test dell'API aperta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

È possibile usare il servizio Visione artificiale per eseguire le attività seguenti:

* Analizzare un'immagine remota
* Analizzare un'immagine locale
* Rilevare celebrità e luoghi di interesse (modelli di dominio)
* Generare un'anteprima intelligente
* Rilevare ed estrarre testo stampato (OCR) da un'immagine
* Rilevare ed estrarre testo scritto a mano da un'immagine

Pur essendo simile in ogni esempio, il codice evidenzia funzionalità di Visione artificiale diverse con diverse tecniche per lo scambio dei dati con il servizio, ad esempio:

* La _generazione di un'anteprima_ restituisce un'immagine come matrice di byte nel corpo della risposta.
* Per l'_analisi di un'immagine locale_ è necessario che tale immagine venga inclusa nella richiesta come matrice di byte.
* Per l'_estrazione di testo scritto a mano_ sono necessarie due chiamate per recuperare il testo.

## <a name="summary"></a>Summary

| Guida introduttiva               | Parametri della richiesta                          | Risposta          |
| ------------------------ | ------------------------------------------- | ----------------  |
| Analizzare un'immagine remota   | visualFeatures=Categories,Description,Color | stringa JSON       |
| Analizzare un'immagine locale    | data=image_data (matrice di byte)                | stringa JSON       |
| Rilevare celebrità       | model=celebrities                           | stringa JSON       |
| Generare un'anteprima     | width=200&height=150&smartCropping=true     | matrice di byte        |
| Estrarre testo stampato     | language=unk&detectOrientation=true         | stringa JSON       |
| Estrarre testo scritto a mano | handwriting=true                            | URL, stringa JSON  |

## <a name="next-steps"></a>Passaggi successivi

Esaminare le API Visione artificiale usate per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato.

> [!div class="nextstepaction"]
> [Esaminare le API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
