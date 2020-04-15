---
title: Pianificare l'app - LUIS
description: Definire le finalità e le entità dell'app e quindi creare i relativi piani in Language Understanding Intelligent Service (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382300"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Pianificare lo schema dell'app LUIS con il dominio dell'oggetto e l'estrazione dei dati

Uno schema di app LUIS contiene finalità ed [entità](luis-glossary.md#entity) [rilevanti](luis-glossary.md#intent) per il [dominio](luis-glossary.md#domain)dell'oggetto. Le finalità classificano [le espressioni](luis-glossary.md#utterance)utente e le entità estraggono i dati dalle espressioni utente.

## <a name="identify-your-domain"></a>Identificare il dominio

Un'app LUIS è incentrata su un dominio soggetto. Ad esempio, potresti avere un'app di viaggio che gestisce la prenotazione di biglietti, voli, hotel e auto a noleggio. Un'altra app potrebbe fornire contenuti relativi ad allenamento fisico, monitoraggio degli sforzi e impostazione degli obiettivi. L'identificazione del dominio consente di trovare parole o frasi rilevanti per il dominio.

> [!TIP]
> LUIS offre [domini predefiniti](luis-how-to-use-prebuilt-domains.md) per molti scenari comuni. Verificare se è possibile utilizzare un dominio predefinito come punto di partenza per l'app.

## <a name="identify-your-intents"></a>Identificare le finalità

Considerare gli [scopi](luis-concept-intent.md) importanti per l'attività dell'applicazione.

Si consideri l'esempio di un'app di viaggi con funzioni per la prenotazione di un volo e il controllo delle previsioni meteo relative alla destinazione dell'utente. È possibile `BookFlight` definire `GetWeather` le finalità e per queste azioni.

In un'app più complessa con più funzioni, hai più finalità e devi definirle con attenzione in modo che le finalità non siano troppo specifiche. Ad `BookFlight` esempio, `BookHotel` e potrebbe essere necessario `BookInternationalFlight` essere `BookDomesticFlight` finalità separate, ma e potrebbe essere troppo simile.

> [!NOTE]
> È consigliabile limitare il numero delle finalità solo a quelle necessarie per eseguire le funzioni dell'app. Se si definiscono troppe finalità, per LUIS sarà più difficile classificare correttamente le espressioni. Se si definiscono troppo pochi, potrebbero essere così generali da sovrapporsi.

Se non è necessario identificare le intenzioni generali dell'utente, `None` aggiungere tutte le espressioni utente di esempio alla finalità. Se l'app ha bisogno di più finalità, puoi crearle in un secondo momento.

## <a name="create-example-utterances-for-each-intent"></a>Creare espressioni di esempio per ogni finalità

Per iniziare, evitare di creare troppe espressioni per ogni finalità. Dopo aver determinato le finalità, creare da 15 a 30 espressioni di esempio per finalità. Ogni espressione deve essere diversa dalle espressioni fornite in precedenza. Una buona varietà nelle espressioni include il conteggio complessivo delle parole, la scelta delle parole, il tempo verbale e la [punteggiatura](luis-reference-application-settings.md#punctuation-normalization).

Per ulteriori informazioni, vedere [Understanding good utterances for LUIS apps](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identificare le entità

Nelle espressioni di esempio individuare le entità che si intende estrarre. Per prenotare un volo, sono necessarie informazioni come la destinazione, la data, la compagnia aerea, la categoria di biglietti e la classe di viaggio. Creare entità per questi tipi di dati e contrassegnare le [entità](luis-concept-entity-types.md) nelle espressioni di esempio. Le entità sono importanti per l'esecuzione di un intento.

Quando determini le entità da usare nella tua app, tieni presente che esistono diversi tipi di entità per l'acquisizione di relazioni tra tipi di oggetti. L'articolo [Entità in LUIS](luis-concept-entity-types.md) fornisce maggiori dettagli su questi diversi tipi.

> [!TIP]
> LUIS offre [entità predefinite](luis-prebuilt-entities.md) per scenari utente comuni e conversazionali. Prendere in considerazione l'utilizzo di entità predefinite come punto di partenza per lo sviluppo di applicazioni.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Apprendimento del ciclo di vita dello sviluppo LUIS](luis-concept-app-iteration.md)

