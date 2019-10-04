---
title: Pianificare l'app-LUIS
titleSuffix: Azure Cognitive Services
description: Definire le finalità e le entità dell'app e quindi creare i relativi piani in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dc648b30dc1236080be06044f510557ae0ce9476
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638303"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Pianificare l'app LUIS con dominio soggetto, finalità ed entità

Per pianificare l'app, identificare il dominio dell'area del soggetto. Sono inclusi possibili Intent ed entità rilevanti per l'applicazione.  

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

Una volta determinati gli Intent, creare da 15 a 30 espressioni di esempio per ogni finalità. Per iniziare, non avere meno di questo numero o creare troppe espressioni per ogni finalità. Ogni espressione deve essere diversa dall'espressione precedente. Una buona varietà di espressioni comprende il conteggio complessivo delle parole, la scelta delle parole, i tempi verbali e la punteggiatura. 

Per ulteriori informazioni, esaminare le [espressioni](luis-concept-utterance.md) .

## <a name="identify-your-entities"></a>Identificare le entità

Nelle espressioni di esempio individuare le entità che si intende estrarre. Per prenotare un volo, sono necessarie informazioni come la destinazione, la data, la compagnia aerea, la categoria di ticket e la classe di viaggio. Creare entità per questi tipi di dati e quindi contrassegnare le [entità](luis-concept-entity-types.md) negli enunciati di esempio perché sono importanti per l'esecuzione di una finalità. 

Quando si determinano le entità da usare nell'app, tenere presente che esistono diversi tipi di entità per l'acquisizione di relazioni tra i tipi di oggetti. L'articolo [Entità in LUIS](luis-concept-entity-types.md) fornisce maggiori dettagli su questi diversi tipi.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver effettuato il training e la pubblicazione dell'app e aver ottenuto le espressioni di endpoint per l'app, occorre pianificare l'implementazione di miglioramenti della previsione mediante [apprendimento attivo](luis-how-to-review-endpoint-utterances.md), [elenchi di frasi](luis-concept-feature.md) e [criteri](luis-concept-patterns.md). 


* Vedere [Creare la prima app Language Understanding Intelligent Service (LUIS)](luis-get-started-create-app.md) per la procedura dettagliata per creare un'app LUIS.
