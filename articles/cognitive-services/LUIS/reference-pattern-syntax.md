---
title: Informazioni di riferimento sulla sintassi del modello - LUISPattern syntax reference - LUIS
titleSuffix: Azure Cognitive Services
description: Creare entità per estrarre i dati chiave dalle espressioni utente nelle app LUIS (Language Understanding). I dati estratti vengono utilizzati dall'applicazione client.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 696f4bdc22bed01a4b5be8bff63ade482a8dbe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219731"
---
# <a name="pattern-syntax"></a>Sintassi dei criteri

La sintassi dei criteri è un modello per un'espressione. Il modello deve contenere parole ed entità che si vuole far corrispondere, nonché le parole e la punteggiatura che si vuole ignorare. **Non** si tratta di un'espressione regolare.

> [!CAUTION]
> I modelli includono solo gli elementi padre di entità appresi dal computer, non i sottocomponenti.

Le entità nei criteri sono racchiuse tra parentesi graffe, `{}`. I criteri possono includere entità ed entità con ruoli. [Pattern.any](luis-concept-entity-types.md#patternany-entity) è un'entità utilizzata solo nei modelli.

La sintassi del modello supporta la sintassi seguente:Pattern syntax supports the following syntax:

|Funzione|Sintassi|Livello di nidificazione|Esempio|
|--|--|--|--|
|Entità| {}- parentesi graffe|2|Dov'è il modulo "nome-entità"?|
|facoltativo|[] - parentesi quadre<BR><BR>Esiste un limite di 3 sui livelli di nidificazione di qualsiasi combinazione di |2|Il punto interrogativo è facoltativo [?]|
|raggruppamento|() - parentesi|2|è (a \| b)|
|o| \|- barra verticale (tubo)<br><br>C'è un limite di 2 sulle barre verticali (O) in un gruppo |-|Dove è (nome-modulo-breve &#x7c; "nome-modulo-lungo" &#x7c; "numero-modulo")|
|inizio e/o fine dell'espressione|- accento circonflesso|-|: iniziare l'espressione<br>l'espressione è fatta.<br>: corrispondenza letterale rigorosa dell'intera espressione con l'entità numero.|

## <a name="nesting-syntax-in-patterns"></a>Nidificazione della sintassi nei modelli

La sintassi **facoltativa,** con parentesi quadre, può essere nidificata su due livelli. Ad esempio `[[this]is] a new form`. Questo esempio consente le espressioni seguenti:This example allows for the following utterances:

|Esempio di espressione facoltativa annidataNested optional utterance example|Spiegazione|
|--|--|
|questa è una nuova forma|corrisponde a tutte le parole nel modello|
|è una nuova forma|corrisponde a una parola facoltativa esterna e a parole non facoltative nel modello|
|un nuovo modulo|corrisponde solo alle parole richieste|

La sintassi di **raggruppamento,** racchiusa tra parentesi, può essere nidificata su due livelli. Ad esempio `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Questa funzione consente di trovare una corrispondenza tra le tre entità.

Se Entity1 è una posizione con ruoli quali origine (Seattle) e destinazione (Cairo) e Entità 2 è un nome di edificio noto da un'entità di elenco (RedWest-C), le espressioni seguenti verrebbero mappate a questo modello:If Entity1 is a Location with roles such as origin (Seattle) and destination (Cairo) and Entity 2 is a known building name from a list entity (RedWest-C), the following utterances would map to this pattern:

|Esempio di espressione di raggruppamento annidatoNested grouping utterance example|Spiegazione|
|--|--|
|RedWest-C|corrisponde all'entità di raggruppamento esterno|
|Seattle|corrisponde a una delle entità di raggruppamento interno|
|Cairo|corrisponde a una delle entità di raggruppamento interno|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limiti di nidificazione per i gruppi con sintassi facoltativa

Una combinazione di **raggruppamento** con sintassi **facoltativa** ha un limite di 3 livelli di annidamento.

|Consentito|Esempio|
|--|--|
|Sì|( ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|No|( [ [ [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limiti di nidificazione per i gruppi con sintassi o-ing

Una combinazione di **raggruppamento** con sintassi o di **raggruppamento** ha un limite di 2 barre verticali.

|Consentito|Esempio|
|--|--|
|Sì|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 )|
|No|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Sintassi per aggiungere un'entità a un modello di criteri
Per aggiungere un'entità a un modello di criteri, racchiudere il nome dell'entità tra parentesi graffe, come ad esempio in `Who does {Employee} manage?`.

|Criteri con entità|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Sintassi per aggiungere un'entità e un ruolo a un modello di criteri
Un ruolo di entità viene indicato come `{entity:role}` con il nome dell'entità seguito da due punti e dal nome del ruolo. Per aggiungere un'entità con un ruolo a un modello di criteri, racchiudere il nome dell'entità e il nome del ruolo tra parentesi graffe, come ad esempio in `Book a ticket from {Location:Origin} to {Location:Destination}`.

|Criteri con ruoli di entità|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Sintassi per aggiungere un'entità pattern.any a un modello di criteri
L'entità Pattern.any consente di aggiungere al criterio un'entità di lunghezza variabile. L'entità Pattern.any può avere qualsiasi lunghezza, purché il modello di criteri sia rispettato.

Per aggiungere un'entità **Pattern.any** al modello di criteri, racchiuderla tra parentesi graffe, come ad esempio in `How much does {Booktitle} cost and what format is it available in?`.

|Criteri con entità pattern.any|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Titoli di libri nel criterio|
|--|
|How much does **steal this book** cost and what format is it available in?|
|How much does **ask** cost and what format is it available in?|
|How much does **The Curious Incident of the Dog in the Night-Time** cost and what format is it available in?|

Le parole del titolo del libro non confondono LUIS perché LUIS sa dove finisce il titolo del libro, in base all'entità Pattern.any.

## <a name="explicit-lists"></a>Elenchi espliciti

creare un [elenco esplicito](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) tramite l'API di creazione per consentire l'eccezione quando:

* Il modello contiene un [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* E tale sintassi di modello consente la possibilità di un'estrazione di entità non corretta in base all'espressione.

Ad esempio, si supponga che un criterio contenga sia la sintassi facoltativa, `[]`, che la sintassi dell'entità, `{}`, combinate in modo da estrarre i dati in modo non corretto.

Si consideri il criterio "[find] email about {subject} [from {person}]".

Nelle espressioni seguenti le entità **subject** e **person** vengono estratte in modo corretto e non corretto:

|Espressione|Entità|Estrazione corretta|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

Nella tabella precedente, l'oggetto `the man from La Mancha` deve essere (titolo di libro), `from`ma poiché l'oggetto include la parola facoltativa, il titolo viene stimato in modo non corretto.

Per risolvere questa eccezione nel criterio, aggiungere `the man from la mancha` come corrispondenza dell'elenco esplicito per l'entità {subject} usando l'[API di creazione per l'elenco esplicito](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintassi per contrassegnare un testo facoltativo in un'espressione del modello
Contrassegnare il testo facoltativo nell'espressione usando la sintassi tra parentesi quadre dell'espressione regolare, `[]`. Nel testo facoltativo possono essere nidificate un massimo di due parentesi quadre.

|Criteri con testo facoltativo|Significato|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`e `from {person}` sono opzionali|
|'Puoi aiutarmi[?]|Il segno di punteggiatura è facoltativo|

I segni di punteggiatura (`?`, `!`, `.`) devono essere ignorati ed è necessario ignorarli utilizzando la sintassi delle parentesi quadre nei modelli.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui modelli:

* [Come aggiungere modelli](luis-how-to-model-intent-pattern.md)
* [Come aggiungere un'entità pattern.anyHow to add pattern.any entity](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Concetti relativi ai modelliPatterns Concepts](luis-concept-patterns.md)

Comprendere come viene restituito il [sentiment](luis-reference-prebuilt-sentiment.md) nella risposta .json.