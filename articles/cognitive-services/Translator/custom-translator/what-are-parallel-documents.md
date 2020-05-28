---
title: Che cosa sono i documenti paralleli? - Custom Translator
titleSuffix: Azure Cognitive Services
description: I documenti paralleli sono coppie di documenti in cui uno è la traduzione dell'altro. Un documento della coppia contiene frasi nella lingua di origine, mentre l'altro contiene le stesse frasi tradotte nella lingua di destinazione.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d397e481fcd11e534e4bb5cf98f8c402435e890e
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997078"
---
# <a name="what-are-parallel-documents"></a>Che cosa sono i documenti paralleli?

I documenti paralleli sono coppie di documenti in cui uno è la traduzione dell'altro. Un documento della coppia contiene frasi nella lingua di origine, mentre l'altro contiene le stesse frasi tradotte nella lingua di destinazione.
Non importa quale lingua è contrassegnata come "origine" e quale lingua è contrassegnata come "destinazione", un documento parallelo può essere usato per eseguire il training di un sistema di conversione in entrambe le direzioni.

## <a name="requirements"></a>Requisiti

Per eseguire il training di un sistema sono necessarie almeno 10.000 frasi parallele allineate univoche. Questa limitazione è una rete di sicurezza per garantire che le frasi parallele contengano un vocabolario univoco sufficiente per eseguire correttamente il training di un modello di traduzione. Come procedura consigliata, aggiungere continuamente altri contenuti paralleli e ripetere il training per migliorare la qualità del sistema di traduzione. Vedere allineamento delle [frasi](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment).

Microsoft richiede che i documenti caricati nel traduttore personalizzato non violino le proprietà intellettuale o di copyright di terze parti. Per altre informazioni, vedere le [Condizioni per l'utilizzo](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Il caricamento di un documento tramite il portale non altera in alcun modo la proprietà intellettuale nel documento stesso.

## <a name="use-of-parallel-documents"></a>Uso dei documenti paralleli

I documenti paralleli vengono usati dal sistema per:

1.  Apprendere le più comuni modalità di mapping di parole, espressioni e frasi tra le due lingue.

2.  Imparare ad elaborare il contesto appropriato in base alle espressioni circostanti. Non è detto che una parola venga sempre tradotta nella stessa esatta parola nell'altra lingua.

Come procedura consigliata, assicurarsi che esista una corrispondenza di frasi 1:1 tra le versioni dei documenti nella lingua di origine e in quella di destinazione.

Se il progetto è specifico del dominio (categoria), la terminologia dei documenti dovrebbe essere coerente all'interno di tale categoria. La qualità del sistema di traduzione risultante dipende dal numero di frasi nel set di documenti e dalla qualità delle frasi. Maggiore è il numero di esempi nei documenti con utilizzi diversi di una parola specifica della categoria, migliori saranno le prestazioni del sistema durante la traduzione.

I documenti caricati sono privati in ogni area di lavoro e possono essere usati in un numero illimitato di progetti o training. Le frasi estratte dai documenti vengono archiviate separatamente nel repository come file di testo normale Unicode e possono essere eliminate. Non usare Custom Translator come repository di documenti, in quanto non sarà possibile scaricare i documenti nello stesso formato con cui sono stati caricati.



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare un [dizionario](what-is-dictionary.md) in Custom Translator.
