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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467707"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Pianificare lo schema dell'app LUIS con dominio soggetto ed estrazione dei dati

Uno schema di app LUIS contiene Intent ed entità rilevanti per il dominio dell'oggetto. Gli Intent classificano le espressioni utente e le entità estraggono i dati dalle espressioni utente. 

## <a name="identify-your-domain"></a>Identificare il dominio

Un'app LUIS è incentrata su un argomento specifico del dominio.  Ad esempio, si potrebbe avere un'app di viaggi su cui si eseguono prenotazioni di biglietti, voli, hotel e auto a noleggio. Un'altra app potrebbe fornire contenuti relativi ad allenamento fisico, monitoraggio degli sforzi e impostazione degli obiettivi. Identificare il dominio consente di trovare parole o frasi importanti per il dominio.

> [!TIP]
> LUIS offre [domini predefiniti](luis-how-to-use-prebuilt-domains.md) per molti scenari comuni.
> Verificare se è possibile utilizzare un dominio predefinito come punto di partenza per l'app.

## <a name="identify-your-intents"></a>Identificare le finalità

Pensare alle [finalità](luis-concept-intent.md) che sono importanti per l'attività dell'applicazione. 

Si consideri l'esempio di un'app di viaggi con funzioni per la prenotazione di un volo e il controllo delle previsioni meteo relative alla destinazione dell'utente. È possibile definire il `BookFlight` e `GetWeather` Intent per queste azioni. 

In un'app più complessa con più funzioni, sono presenti più Intent ed è necessario definirli con cautela, in modo che gli Intent non siano troppo specifici. È ad esempio possibile che `BookFlight` e `BookHotel` debbano essere ritenute separate, ma `BookInternationalFlight` e `BookDomesticFlight` potrebbero essere troppo simili.

> [!NOTE]
> È consigliabile limitare il numero delle finalità solo a quelle necessarie per eseguire le funzioni dell'app. Se si definiscono troppe finalità, per LUIS sarà più difficile classificare correttamente le espressioni. Se si definisce un numero troppo basso di elementi, potrebbe essere così generale che si sovrappongono.

Se non è necessario identificare l'intenzione complessiva dell'utente, aggiungere tutte le espressioni utente di esempio alla finalità None. Se l'app aumenta in modo da richiedere più Intent, è possibile crearli in un secondo momento. 

## <a name="create-example-utterances-for-each-intent"></a>Creare espressioni di esempio per ogni finalità

Una volta determinati gli Intent, creare da 15 a 30 espressioni di esempio per ogni finalità. Per iniziare, non avere meno di questo numero o creare troppe espressioni per ogni finalità. Ogni espressione deve essere diversa dall'espressione precedente. Una buona varietà di espressioni comprende il conteggio complessivo delle parole, la scelta delle parole, i tempi verbali e la punteggiatura. 

Per ulteriori informazioni, esaminare le [espressioni](luis-concept-utterance.md) .

## <a name="identify-your-entities"></a>Identificare le entità

Nelle espressioni di esempio individuare le entità che si intende estrarre. Per prenotare un volo, sono necessarie informazioni come la destinazione, la data, la compagnia aerea, la categoria di ticket e la classe di viaggio. Creare entità per questi tipi di dati e quindi contrassegnare le [entità](luis-concept-entity-types.md) negli enunciati di esempio perché sono importanti per l'esecuzione di una finalità. 

Quando si determinano le entità da usare nell'app, tenere presente che esistono diversi tipi di entità per l'acquisizione di relazioni tra i tipi di oggetti. L'articolo [Entità in LUIS](luis-concept-entity-types.md) fornisce maggiori dettagli su questi diversi tipi.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul ciclo di [sviluppo](luis-concept-app-iteration.md)tipico.  