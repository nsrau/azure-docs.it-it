---
title: Pianificare l'app-LUIS
description: Definire le finalità e le entità dell'app e quindi creare i relativi piani in Language Understanding Intelligent Service (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018957"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Pianificare lo schema dell'app LUIS con dominio soggetto ed estrazione dei dati

Uno schema di app LUIS contiene [Intent](luis-glossary.md#intent) ed [entità](luis-glossary.md#entity) rilevanti per il [dominio](luis-glossary.md#domain)dell'oggetto. Gli Intent classificano le [espressioni](luis-glossary.md#utterance)utente e le entità estraggono i dati dalle espressioni utente.

## <a name="identify-your-domain"></a>Identificare il dominio

Un'app LUIS è incentrata su un dominio soggetto. Ad esempio, è possibile avere un'app di viaggio che gestisce la prenotazione di biglietti, voli, Alberghi e automobili di noleggio. Un'altra app potrebbe fornire contenuti relativi ad allenamento fisico, monitoraggio degli sforzi e impostazione degli obiettivi. L'identificazione del dominio consente di trovare parole o frasi rilevanti per il dominio.

> [!TIP]
> LUIS offre [domini predefiniti](./howto-add-prebuilt-models.md) per molti scenari comuni. Verificare se è possibile utilizzare un dominio predefinito come punto di partenza per l'app.

## <a name="identify-your-intents"></a>Identificare le finalità

Considerare gli [Intent](luis-concept-intent.md) che sono importanti per le attività dell'applicazione.

Si consideri l'esempio di un'app di viaggi con funzioni per la prenotazione di un volo e il controllo delle previsioni meteo relative alla destinazione dell'utente. È possibile definire gli `BookFlight` `GetWeather` Intent e per queste azioni.

In un'app più complessa con più funzioni, sono presenti più Intent ed è necessario definirli con cautela, in modo che gli Intent non siano troppo specifici. Ad esempio, `BookFlight` e `BookHotel` potrebbe essere necessario che sia un Intent separato, ma `BookInternationalFlight` e `BookDomesticFlight` potrebbe essere troppo simile.

> [!NOTE]
> È consigliabile limitare il numero delle finalità solo a quelle necessarie per eseguire le funzioni dell'app. Se si definiscono troppe finalità, per LUIS sarà più difficile classificare correttamente le espressioni. Se si definisce un numero troppo basso di elementi, potrebbe essere così generale che si sovrappongono.

Se non è necessario identificare l'intenzione complessiva dell'utente, aggiungere tutte le espressioni utente di esempio allo `None` scopo. Se l'app aumenta in modo da richiedere più Intent, è possibile crearli in un secondo momento.

## <a name="create-example-utterances-for-each-intent"></a>Creare espressioni di esempio per ogni finalità

Per iniziare, evitare di creare un numero eccessivo di espressioni per ogni finalità. Una volta stabiliti gli Intent, creare da 15 a 30 espressioni di esempio per finalità. Ogni espressione deve essere diversa dalle espressioni fornite in precedenza. Una grande varietà di espressioni include il conteggio generale delle parole, la scelta di parole, il verbo teso e la [punteggiatura](luis-reference-application-settings.md#punctuation-normalization).

Per altre informazioni, vedere informazioni sulle [espressioni valide per le app Luis](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identificare le entità

Nelle espressioni di esempio individuare le entità che si intende estrarre. Per prenotare un volo, sono necessarie informazioni come la destinazione, la data, la compagnia aerea, la categoria di ticket e la classe di viaggio. Creare entità per questi tipi di dati, quindi contrassegnare le [entità](luis-concept-entity-types.md) negli enunciati di esempio. Le entità sono importanti per l'esecuzione di un preventivo.

Quando si determinano le entità da usare nell'app, tenere presente che esistono tipi diversi di entità per l'acquisizione delle relazioni tra i tipi di oggetto. L'articolo [Entità in LUIS](luis-concept-entity-types.md) fornisce maggiori dettagli su questi diversi tipi.

> [!TIP]
> LUIS offre [entità predefinite](./howto-add-prebuilt-models.md) per scenari utente comuni e di conversazione. Prendere in considerazione l'uso di entità predefinite come punto di partenza per lo sviluppo di applicazioni.

## <a name="resolution-with-intent-or-entity"></a>Risoluzione con finalità o entità?

In molti casi, soprattutto quando si lavora con la conversazione naturale, gli utenti forniscono un enunciato che può contenere più di una funzione o finalità. Per risolvere questo problema, una regola empirica generale consiste nel comprendere che la rappresentazione dell'output può essere eseguita sia negli Intent che nelle entità. Questa rappresentazione deve essere mappabili per le azioni dell'applicazione client e non deve essere limitata agli Intent.

**Int-ENT-Ties** è il concetto che le azioni, generalmente comprese come Intent, possono essere acquisite come entità e si basano su in questo formato nel codice JSON di output in cui è possibile eseguire il mapping a un'azione specifica. La _negazione_ è un utilizzo comune per sfruttare questa dipendenza sia per finalità che per entità per l'estrazione completa.

Si considerino i due enunciati seguenti, che sono molto vicini considerando la scelta di parole, ma hanno risultati diversi:

|Espressione|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

Invece di avere due finalità separate, creare una singola finalità con un'entità di `FlightAction` Machine Learning. L'entità Machine Learning deve estrarre i dettagli dell'azione sia per una pianificazione che per una richiesta di annullamento, nonché per una posizione di origine o di destinazione.

L' `FlightAction` entità è strutturata nello pseudo-schema seguente dell'entità e delle sottoentità di Machine Learning:

* FlightAction
    * Action
    * Origine
    * Destination

Per consentire all'estrazione di aggiungere funzionalità alle sottoentità. Si scelgono le funzionalità in base al vocabolario che si prevede di visualizzare negli enunciati utente e i valori che si desidera vengano restituiti nella risposta alla stima.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Apprendimento della lifecylce di sviluppo LUIS](luis-concept-app-iteration.md)