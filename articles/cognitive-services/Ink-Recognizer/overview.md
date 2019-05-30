---
title: Informazioni su Riconoscimento input penna - API Riconoscimento input penna
titlesuffix: Azure Cognitive Services
description: Integrare Riconoscimento input penna in applicazioni, siti Web, strumenti e altre soluzioni per fornire...
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ed1a72a5dc61458200b72c768ad722656b820d8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65025606"
---
# <a name="what-is-the-ink-recognizer-api"></a>Informazioni sull'API Riconoscimento input penna


Il servizio cognitivo Riconoscimento input penna fornisce un'API REST basata sul cloud per analizzare e riconoscere il contenuto dell'input penna. A differenza di servizi come il riconoscimento ottico dei caratteri (OCR), l'API richiede l'immissione di dati sotto forma di tratto input penna. I tratti input penna sono set temporizzati di punti 2D (coordinate X,Y) che rappresentano il movimento di strumenti di input come penne digitali o dita. Il servizio riconosce quindi la forma del contenuto scritto a mano dall'input e restituisce una risposta JSON contenente tutte le entità riconosciute.

![Diagramma di flusso che descrive l'invio di un tratto input penna all'API](media/ink-recognizer-pen-graph.png)

## <a name="features"></a>Funzionalità

Con l'API Riconoscimento input penna, è possibile riconoscere con facilità il contenuto scritto a mano nelle applicazioni. 

|Funzionalità  |DESCRIZIONE  |
|---------|---------|
| Riconoscimento della grafia | Riconosce il contenuto scritto a mano in 63 [lingue e impostazioni locali](language-support.md) principali. | 
| Riconoscimento del layout | Fornisce informazioni strutturali sul contenuto dell'input penna. Il contenuto viene diviso in aree di scrittura, paragrafi, righe, parole, elenchi puntati. L'applicazione può quindi usare le informazioni sul layout per sviluppare altre funzionalità, come la formattazione automatica degli elenchi e l'allineamento delle forme. |
| Riconoscimento delle forme | Riconosce le [forme geometriche](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) di uso comune quando si scrivono note. |
| Riconoscimento combinato di forme e testo | Riconosce quali tratti input penna appartengono a forme o a contenuto scritto a mano e li classifica separatamente.|

## <a name="workflow"></a>Flusso di lavoro

L'API Riconoscimento input penna è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Dopo aver eseguito l'iscrizione:

1. [Formattare](concepts/send-ink-data.md#sending-ink-data) i dati del tratto input penna in codice JSON valido.
1. Inviare una richiesta all'API Riconoscimento input penna con i dati.
1. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

Provare a seguire una guida di avvio rapido nei linguaggi seguenti per iniziare a effettuare chiamate all'API Riconoscimento input penna.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/csharp.md)

Per vedere come funziona l'API Riconoscimento input penna in un'app di input penna, esaminare le applicazioni di esempio seguenti in GitHub:
* [C# e piattaforma UWP (Universal Windows Platform)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [App Javascript per Web browser](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [App Java e per dispositivi mobili Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [App Swift e per dispositivi mobili iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
