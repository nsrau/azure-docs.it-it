---
title: Che cosa sono i documenti paralleli? - Custom Translator
titleSuffix: Azure Cognitive Services
description: I documenti paralleli sono coppie di documenti in cui uno è la traduzione dell'altro. Un documento della coppia contiene frasi nella lingua di origine, mentre l'altro contiene le stesse frasi tradotte nella lingua di destinazione.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: e644a4df99669e7ad69e08090418c2a3cffc7e9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389832"
---
# <a name="what-are-parallel-documents"></a>Che cosa sono i documenti paralleli?

I documenti paralleli sono coppie di documenti in cui uno è la traduzione dell'altro. Un documento della coppia contiene frasi nella lingua di origine, mentre l'altro contiene le stesse frasi tradotte nella lingua di destinazione.
Non ha importanza quale lingua è contrassegnata come "di origine" e quale come "di destinazione", in quanto un documento parallelo può essere usato per eseguire il training di un sistema in entrambe le direzioni.

## <a name="requirements"></a>Requisiti

È necessario un minimo di 10.000 frasi parallele univoche per il training di un sistema. Come procedura consigliata, è possibile aggiungere continuamente contenuti paralleli e ripetere il training per migliorare la qualità del sistema di traduzione.

Microsoft richiede che i documenti caricati in Custom Translator non violino i diritti di copyright o di proprietà intellettuale di terze parti. Per altre informazioni, vedere le [Condizioni per l'utilizzo](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
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
