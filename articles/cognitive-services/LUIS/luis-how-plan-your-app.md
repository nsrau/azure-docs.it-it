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
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 6a155f4c43da03ccdc40d289742918973aa6da7b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326784"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Pianificare lo schema dell'app LUIS con dominio soggetto ed estrazione dei dati

Uno schema di app LUIS contiene [Intent](luis-glossary.md#intent) ed [entità](luis-glossary.md#entity) rilevanti per il [dominio](luis-glossary.md#domain)dell'oggetto. Gli Intent classificano le [espressioni](luis-glossary.md#utterance)utente e le entità estraggono i dati dalle espressioni utente.

## <a name="identify-your-domain"></a>Identificare il dominio

Un'app LUIS è incentrata su un dominio soggetto. Ad esempio, è possibile avere un'app di viaggio che gestisce la prenotazione di biglietti, voli, Alberghi e automobili di noleggio. Un'altra app potrebbe fornire contenuti relativi ad allenamento fisico, monitoraggio degli sforzi e impostazione degli obiettivi. L'identificazione del dominio consente di trovare parole o frasi rilevanti per il dominio.

> [!TIP]
> LUIS offre [domini predefiniti](luis-how-to-use-prebuilt-domains.md) per molti scenari comuni. Verificare se è possibile utilizzare un dominio predefinito come punto di partenza per l'app.

## <a name="identify-your-intents"></a>Identificare le finalità

Considerare gli [Intent](luis-concept-intent.md) che sono importanti per le attività dell'applicazione.

Si consideri l'esempio di un'app di viaggi con funzioni per la prenotazione di un volo e il controllo delle previsioni meteo relative alla destinazione dell'utente. È possibile definire il `BookFlight` e `GetWeather` Intent per queste azioni.

In un'app più complessa con più funzioni, sono presenti più Intent ed è necessario definirli con cautela, in modo che gli Intent non siano troppo specifici. È ad esempio possibile che `BookFlight` e `BookHotel` debbano essere ritenute separate, ma `BookInternationalFlight` e `BookDomesticFlight` potrebbero essere troppo simili.

> [!NOTE]
> È consigliabile limitare il numero delle finalità solo a quelle necessarie per eseguire le funzioni dell'app. Se si definiscono troppe finalità, per LUIS sarà più difficile classificare correttamente le espressioni. Se si definisce un numero troppo basso di elementi, potrebbe essere così generale che si sovrappongono.

Se non è necessario identificare l'intenzione complessiva dell'utente, aggiungere tutte le espressioni utente di esempio allo scopo del `None`. Se l'app aumenta in modo da richiedere più Intent, è possibile crearli in un secondo momento.

## <a name="create-example-utterances-for-each-intent"></a>Creare espressioni di esempio per ogni finalità

Per iniziare, evitare di creare un numero eccessivo di espressioni per ogni finalità. Una volta stabiliti gli Intent, creare da 15 a 30 espressioni di esempio per finalità. Ogni espressione deve essere diversa dalle espressioni fornite in precedenza. Una grande varietà di espressioni include il conteggio generale delle parole, la scelta di parole, il verbo teso e la punteggiatura.

Per altre informazioni, vedere informazioni sulle [espressioni valide per le app Luis](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identificare le entità

Nelle espressioni di esempio individuare le entità che si intende estrarre. Per prenotare un volo, sono necessarie informazioni come la destinazione, la data, la compagnia aerea, la categoria di ticket e la classe di viaggio. Creare entità per questi tipi di dati, quindi contrassegnare le [entità](luis-concept-entity-types.md) negli enunciati di esempio. Le entità sono importanti per l'esecuzione di un preventivo.

Quando si determinano le entità da usare nell'app, tenere presente che esistono tipi diversi di entità per l'acquisizione delle relazioni tra i tipi di oggetto. L'articolo [Entità in LUIS](luis-concept-entity-types.md) fornisce maggiori dettagli su questi diversi tipi.

> [!TIP]
> LUIS offre [entità predefinite](luis-prebuilt-entities.md) per scenari utente comuni e di conversazione. Prendere in considerazione l'uso di entità predefinite come punto di partenza per lo sviluppo di applicazioni.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Apprendimento della lifecylce di sviluppo LUIS](luis-concept-app-iteration.md)

