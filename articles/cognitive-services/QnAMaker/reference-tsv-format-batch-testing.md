---
title: Formato TSV di test in batch - QnA Maker
titleSuffix: Azure Cognitive Services
description: Comprendere il formato TSV per il test in batch
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507825"
---
# <a name="batch-testing-tsv-format"></a>Formato TSV di test in batch

Il test batch è disponibile dal [codice sorgente](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) o come [eseguibile scaricabile.](https://aka.ms/qna_btzip) Il formato del comando per eseguire il test batch è:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Valore previsto|
|--|--|
|1|nome del file tsv formattato con campi di [input TSV](#tsv-input-fields)|
|2|URI per endpoint, con YOUR-HOST dalla pagina Pubblica del portale QnA Maker.|
|3|ENDPOINT-KEY, disponibile nella pagina Pubblica del portale QnA Maker.|
|4|nome del file tsv creato dal test batch per i risultati.|

Utilizzare le informazioni seguenti per comprendere e implementare il formato TSV per il test in batch. 

## <a name="tsv-input-fields"></a>Campi di immissione TSV

|Campi del file di input TSV|Note|
|--|--|
|KBID|L'ID KB disponibile nella pagina Pubblica.|
|Domanda|La domanda che un utente inserirebilmente.|
|Tag dei metadati|facoltativo|
|Parametro Top|facoltativo| 
|ID della risposta prevista|facoltativo|

![Formato di input per il file TSV per il test in batch.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Campi di output TSV 

|Parametri del file di output TSV|Note|
|--|--|
|KBID|L'ID KB disponibile nella pagina Pubblica.|
|Domanda|La domanda immessa dal file di input.|
|Risposta|Top risposta dalla vostra base di conoscenze.|
|ID risposta|ID risposta|
|Punteggio|Punteggio di stima per la risposta. |
|Tag dei metadati|associato con risposta restituita|
|ID della risposta prevista|facoltativo (solo quando viene fornito l'ID di risposta previsto)|
|Etichetta di giudizio|opzionale, i valori potrebbero essere: corretti o non corretti (solo quando viene data la risposta prevista)|
