---
title: "Uso delle funzionalità cognitive U-SQL in Azure Data Lake Analytics | Documentazione Microsoft"
description: "Informazioni su come usare l'intelligence delle funzionalità cognitive in U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: a651fe045d7eb1265f698ebb89843fd4c2b1c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Esercitazione: Introduzione alle funzionalità cognitive di U-SQL

## <a name="overview"></a>Panoramica
Le funzionalità cognitive per U-SQL consentono agli sviluppatori di usare l'intelligence nei programmi di Big Data che creano. 

Sono disponibili le funzionalità cognitive seguenti:
* Immagini: rilevamento visi
* Immagini: rilevamento emozioni
* Immagini: rilevamento oggetti (aggiunta di tag)
* Immagini: OCR (riconoscimento ottico dei caratteri)
* Testo: estrazione frasi chiave
* Testo: analisi del sentiment

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>Come usare le funzionalità cognitive negli script U-SQL

Il processo complessivo è semplice:

* Usare l'istruzione REFERENCE ASSEMBLY per abilitare le funzionalità cognitive per lo script U-SQL
* Usare PROCESS in un set di righe di input usando un oggetto UDO delle funzionalità cognitive per generare un set di righe di output

### <a name="detecting-objects-in-images"></a>Rilevamento di oggetti nelle immagini

L'esempio seguente illustra come usare le funzionalità cognitive per rilevare gli oggetti nelle immagini.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputers.Csv();
```
Per altri esempi, vedere gli **esempi di funzionalità cognitive U-SQL** nella sezione **Passaggi successivi**.

## <a name="next-steps"></a>Passaggi successivi
* [Esempi di funzionalità cognitive U-SQL](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Sviluppare script U-SQL con Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso delle funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-use-window-functions.md)
