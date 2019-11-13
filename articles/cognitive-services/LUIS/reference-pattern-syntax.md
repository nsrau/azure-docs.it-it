---
title: Riferimento alla sintassi del modello-LUIS
titleSuffix: Azure Cognitive Services
description: Creare entità per estrarre i dati chiave da espressioni utente nelle app Language Understanding (LUIS). I dati estratti vengono utilizzati dall'applicazione client.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: c1353ae530493c34413399a7fbbfe56fb74d452f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010756"
---
# <a name="pattern-syntax"></a>Sintassi dei criteri

La sintassi dei criteri è un modello per un'espressione. Il modello deve contenere parole ed entità che si vuole far corrispondere, nonché le parole e la punteggiatura che si vuole ignorare. **Non** si tratta di un'espressione regolare. 

Le entità nei criteri sono racchiuse tra parentesi graffe, `{}`. I criteri possono includere entità ed entità con ruoli. [Pattern. any](luis-concept-entity-types.md#patternany-entity) è un'entità usata solo nei modelli. 

La sintassi del modello supporta la sintassi seguente:

|Funzione|Sintassi|Livello di annidamento|Esempio|
|--|--|--|--|
|Entità| parentesi graffe {}|2|Dove è il formato {nome-entità}?|
|Facoltativa|[]-parentesi quadre<BR><BR>È previsto un limite di 3 per i livelli di nidificazione di qualsiasi combinazione di facoltativo e raggruppamento |2|Il punto interrogativo è facoltativo [?]|
|Raggruppamento|()-parentesi|2|is (a \| b)|
|oppure| \|-barra verticale (pipe)<br><br>È previsto un limite di 2 sulle barre verticali (o) in un gruppo |-|Dove è form ({form-name-Short} &#x7c; {form-name-Long} &#x7c; {form-Number})| 
|inizio e/o fine dell'espressione|^-punto di inserimento|-|^ iniziare il enunciato<br>l'espressione è stata eseguita ^<br>^ corrispondenza letterale Strict dell'intero enunciato con {Number} entità ^|

## <a name="nesting-syntax-in-patterns"></a>Annidamento della sintassi nei modelli

La sintassi **facoltativa** , con parentesi quadre, può essere annidata in due livelli. Ad esempio: `[[this]is] a new form`. Questo esempio consente le espressioni seguenti: 

|Esempio di espressione facoltativa annidata|Spiegazione|
|--|--|
|si tratta di un nuovo modulo|trova la corrispondenza di tutte le parole nel modello|
|è un nuovo modulo|corrisponde alla parola facoltativa esterna e alle parole non facoltative nel modello|
|un nuovo form|corrisponde solo alle parole obbligatorie|

La sintassi di **raggruppamento** , con parentesi, può essere annidata in due livelli. Ad esempio: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Questa funzionalità consente di trovare una corrispondenza tra le tre entità. 

Se Entity1 è un percorso con ruoli quali Origin (Seattle) e Destination (Cairo) ed Entity 2 è un nome di edificio noto da un'entità List (RedWest-C), le espressioni seguenti eseguiranno il mapping a questo modello:

|Esempio di espressione di raggruppamento annidato|Spiegazione|
|--|--|
|RedWest-C|corrisponde all'entità di raggruppamento esterna|
|Seattle|corrisponde a una delle entità di raggruppamento interne|
|Cairo|corrisponde a una delle entità di raggruppamento interne|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limiti di annidamento per i gruppi con sintassi facoltativa

Una combinazione di **raggruppamento** con sintassi **facoltativa** ha un limite di 3 livelli di annidamento.

|Consentito|Esempio|
|--|--|
|Sì|([(test1 &#x7c; test2)] &#x7c; test3)|
|No|([([test1] &#x7c; test2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limiti di annidamento per i gruppi con sintassi or-Ing

Una combinazione di **raggruppamento** con la sintassi **or-ing** ha un limite di 2 barre verticali.

|Consentito|Esempio|
|--|--|
|Sì|(test1 &#x7c; test2 &#x7c; (test3 &#x7c; test4))|
|No|(test1 &#x7c; test2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

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

Le parole del titolo del libro non creano confusione in LUIS, perché LUIS sa dove termina il titolo del libro, in base al modello. qualsiasi entità.

## <a name="explicit-lists"></a>Elenchi espliciti

creare un [elenco esplicito](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) tramite l'API di creazione per consentire l'eccezione nei casi seguenti:

* Il modello contiene un [pattern. any](luis-concept-entity-types.md#patternany-entity)
* Questa sintassi del modello consente la possibilità di un'estrazione di entità non corretta basata sull'espressione. 

Ad esempio, si supponga che un criterio contenga sia la sintassi facoltativa, `[]`, che la sintassi dell'entità, `{}`, combinate in modo da estrarre i dati in modo non corretto.

Si consideri il criterio "[find] email about {subject} [from {person}]".

Nelle espressioni seguenti le entità **subject** e **person** vengono estratte in modo corretto e non corretto:

|Espressione|Entità|Estrazione corretta|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

Nella tabella precedente, l'oggetto deve essere `the man from La Mancha` (titolo di un libro), ma poiché l'oggetto include la parola facoltativa `from`, il titolo viene stimato in modo errato. 

Per risolvere questa eccezione nel criterio, aggiungere `the man from la mancha` come corrispondenza dell'elenco esplicito per l'entità {subject} usando l'[API di creazione per l'elenco esplicito](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintassi per contrassegnare un testo facoltativo in un'espressione del modello
Contrassegnare il testo facoltativo nell'espressione usando la sintassi tra parentesi quadre dell'espressione regolare, `[]`. Nel testo facoltativo possono essere nidificate un massimo di due parentesi quadre.

|Criteri con testo facoltativo|Significato|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` e `from {person}` sono facoltativi|
|' Posso aiutarmi [?]|Il segno di punteggiatura è facoltativo|

I segni di punteggiatura (`?`, `!`, `.`) devono essere ignorati ed è necessario ignorarli usando la sintassi tra parentesi quadre nei modelli. 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui modelli:

* [Come aggiungere modelli](luis-how-to-model-intent-pattern.md)
* [Come aggiungere pattern. any Entity](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Concetti relativi ai modelli](luis-concept-patterns.md)

Informazioni sul modo in cui viene restituito il [sentimento](luis-reference-prebuilt-sentiment.md) nella risposta JSON.