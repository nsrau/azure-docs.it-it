---
title: Pianificare l'app
titleSuffix: Language Understanding - Azure Cognitive Services
description: Definire le finalità e le entità dell'app e quindi creare i relativi piani in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 9d54cff81f39f41b60800e9b33f3b4da1a735d85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196236"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Pianificare l'app LUIS con dominio soggetto, finalità ed entità

È importante pianificare l'app. Identificare il dominio, tra cui possibili finalità ed entità rilevanti per l'applicazione.  

## <a name="identify-your-domain"></a>Identificare il dominio

Un'app LUIS è incentrata su un argomento specifico del dominio.  Ad esempio, si potrebbe avere un'app di viaggi su cui si eseguono prenotazioni di biglietti, voli, hotel e auto a noleggio. Un'altra app potrebbe fornire contenuti relativi ad allenamento fisico, monitoraggio degli sforzi e impostazione degli obiettivi. Identificare il dominio consente di trovare parole o frasi importanti per il dominio.

> [!TIP]
> LUIS offre [domini predefiniti](luis-how-to-use-prebuilt-domains.md) per molti scenari comuni.
> Verificare se è possibile utilizzare un dominio predefinito come punto di partenza per l'app.

## <a name="identify-your-intents"></a>Identificare le finalità

Pensare alle [finalità](luis-concept-intent.md) che sono importanti per l'attività dell'applicazione. Si consideri l'esempio di un'app di viaggi con funzioni per la prenotazione di un volo e il controllo delle previsioni meteo relative alla destinazione dell'utente. Per queste azioni è possibile definire le finalità "BookFlight" e "GetWeather". In un'app più complessa con più funzioni, si avranno più finalità e sarà necessario definirle con attenzione in modo che non siano troppo specifiche. Ad esempio, "BookFlight" e "BookHotel" potrebbero essere finalità separate, ma "BookInternationalFlight" e "BookDomesticFlight" potrebbero essere troppo simili.

> [!NOTE]
> È consigliabile limitare il numero delle finalità solo a quelle necessarie per eseguire le funzioni dell'app. Se si definiscono troppe finalità, per LUIS sarà più difficile classificare correttamente le espressioni. Se invece se ne definiscono troppo poche, potrebbero risultare così generali da sovrapporsi.

## <a name="create-example-utterances-for-each-intent"></a>Creare espressioni di esempio per ogni finalità

Dopo aver determinato le finalità, creare 10 o 15 espressioni di esempio per ciascuna. Per cominciare, il numero non deve essere inferiore a questo e non si devono creare molte espressioni per ogni finalità. Ogni espressione deve essere diversa dall'espressione precedente. Una buona varietà di espressioni comprende il conteggio complessivo delle parole, la scelta delle parole, i tempi verbali e la punteggiatura. 

## <a name="identify-your-entities"></a>Identificare le entità

Nelle espressioni di esempio individuare le entità che si intende estrarre. Per prenotare un volo sono necessarie alcune informazioni come destinazione, data, compagnia aerea, categoria del biglietto e classe di viaggio. Creare entità per questi tipi di dati e quindi contrassegnare le [entità](luis-concept-entity-types.md) nelle espressioni di esempio perché sono importanti per conseguire una finalità. 

Quando si determinano le entità da usare nell'app, tenere presente che esistono diversi tipi di entità per l'acquisizione di relazioni tra i tipi di oggetti. L'articolo [Entità in LUIS](luis-concept-entity-types.md) fornisce maggiori dettagli su questi diversi tipi.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver effettuato il training e la pubblicazione dell'app e aver ottenuto le espressioni di endpoint per l'app, occorre pianificare l'implementazione di miglioramenti della previsione mediante [apprendimento attivo](luis-how-to-review-endpoint-utterances.md), [elenchi di frasi](luis-concept-feature.md) e [criteri](luis-concept-patterns.md). 


* Vedere [Creare la prima app Language Understanding Intelligent Service (LUIS)](luis-get-started-create-app.md) per la procedura dettagliata per creare un'app LUIS.
