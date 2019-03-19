---
title: Miglioramento delle stime grazie ai criteri
titleSuffix: Language Understanding - Azure Cognitive Services
description: Un modello consente di ottenere maggiore accuratezza in relazione a una finalità senza fornire molte altre espressioni.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: diberry
ms.openlocfilehash: 42ac75b6ed0d4489ccae014b9cfe3b08269c1218
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547419"
---
# <a name="patterns-improve-prediction-accuracy"></a>Migliorare l'accuratezza della stima con i criteri
I criteri sono progettati per migliorare l'accuratezza quando vi sono più espressioni molto simili.  Un modello consente di ottenere maggiore accuratezza in relazione a una finalità senza fornire molte altre espressioni. 

## <a name="patterns-solve-low-intent-confidence"></a>I criteri risolvono il problema dell'attendibilità ridotta della finalità
Si consideri un'app di risorse umane che genera report nel grafico aziendale su un dipendente. Dato un nome e una relazione dipendente, LUIS restituisce i dipendenti coinvolti. Si consideri un dipendente, Tom, con un manager di nome Alice e un team di sottoposti di nome Michael, Rebecca e Carl.

![Immagine del grafico aziendale](./media/luis-concept-patterns/org-chart.png)

|Espressioni|Finalità stimata|Punteggio finalità|
|--|--|--|
|Chi è il sottoposto di Tom?|GetOrgChart|.30|
|Come si chiama il sottoposto di Tom?|GetOrgChart|.30|

Se un'app ha tra le 10 e le 20 espressioni con diverse lunghezze di frase, diverso ordine di parole e persino diverse parole (sinonimi di "sottoposto", "gestire", "report"), LUIS può restituire un punteggio corrispondente a un'attendibilità bassa. Creare un modello per comprendere l'importanza dell'ordine di word, LUIS. 

I criteri risolvono le situazioni seguenti: 

* Il punteggio di finalità è bassa
* L'intento corretto non è il punteggio superiore ma troppo vicini al punteggio superiore. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>I criteri non sono una garanzia della finalità
I criteri usano una combinazione di tecnologie di stima. L'impostazione di una finalità per l'espressione di un modello in un criterio non è una garanzia per la stima della finalità ma è un segnale. 

<a name="patterns-do-not-improve-entity-detection"/>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>I modelli non migliorano il rilevamento di entità apprese macchina

Un modello è destinato principalmente per consentire la stima di Intent e ruoli. L'entità pattern.any viene utilizzato per estrarre le entità in formato libero. Mentre i modelli usano le entità, un modello non consente di rilevare un'entità apprese macchina.  

Non aspettarsi un miglioramento della stima delle entità se si comprimono più espressioni in un singolo criterio. Per le entità semplici da attivare, è necessario aggiungere espressioni o usare entità elenco, altrimenti il criterio non viene attivato.

## <a name="patterns-use-entity-roles"></a>I criteri usano i ruoli delle entità
Se due o più entità in un criterio hanno una relazione di tipo contestuale, i criteri usano i [ruoli](luis-concept-roles.md) delle entità per estrarre informazioni contestuali sull'entità. Ciò equivale agli elementi figlio nelle gerarchiche di entità, ma è disponibile **solo** nei criteri. 

## <a name="prediction-scores-with-and-without-patterns"></a>Punteggi di stima con e senza criteri
Con un numero sufficiente di espressioni di esempio, LUIS dovrebbe poter aumentare l'attendibilità delle stime anche senza criteri. I criteri aumentano il punteggio di attendibilità senza dovere specificare un numero così elevato di espressioni.  

## <a name="pattern-matching"></a>Corrispondenza dei criteri
Per cercare una corrispondenza tra criteri, prima vengono rilevate le entità all'interno del criterio, poi vengono convalidate le parole restanti e l'ordine delle parole del criterio. Le entità nel criterio sono necessarie perché venga individuata una corrispondenza del criterio. Il criterio viene applicato a livello di token, non a livello di carattere. 

## <a name="pattern-syntax"></a>Sintassi dei criteri
La sintassi dei criteri è un modello per un'espressione. Il modello deve contenere parole ed entità che si vuole far corrispondere, nonché le parole e la punteggiatura che si vuole ignorare. **Non** si tratta di un'espressione regolare. 

Le entità nei criteri sono racchiuse tra parentesi graffe, `{}`. I criteri possono includere entità ed entità con ruoli. [Pattern.Any](luis-concept-entity-types.md#patternany-entity) è un'entità usata solo nei modelli. 

Tipo di sintassi supporta la sintassi seguente:

|Funzione|Sintassi|Livello di nidificazione|Esempio|
|--|--|--|--|
|Entità| {} -le parentesi graffe|2|Dov'è il formato {nome entità}?|
|Facoltativo|[]: le parentesi quadre<BR><BR>È previsto un limite di 3 su livelli di annidamento qualsiasi combinazione di raggruppamento e facoltativi |2|Il punto interrogativo è facoltativo [?]|
|raggruppamento|() - parentesi|2|is (a \| b)|
|oppure| \| -verticale della barra (barra verticale)<br><br>È previsto un limite di 2 sulle barre verticali (o) in un gruppo |-|Dove è form ({breve nome-modulo} &#x7c; {nome-modulo-prolungata} &#x7c; {numero modulo})| 
|inizio e/o fine del utterance|^-punto di inserimento|-|^ inizio il utterance<br>viene eseguita la utterance ^<br>^ rigida corrispondenza letterale dell'intera utterance con l'entità {number} ^|

## <a name="nesting-syntax-in-patterns"></a>Sintassi di annidamento nei modelli

Il **facoltativi** sintassi tra parentesi quadre, possono essere due livelli annidati. Ad esempio: `[[this]is] a new form`. In questo esempio consente di ottenere le espressioni seguenti: 

|Esempio utterance facoltativi annidati|Spiegazione|
|--|--|
|si tratta di un nuovo modulo|corrisponde a tutte le parole ve vzoru|
|è un nuovo modulo|word facoltativo outer corrispondenze e le parole non facoltativi nel modello|
|un nuovo modulo|solo parole corrispondenze obbligate|

Il **raggruppamento** sintassi tra parentesi, può essere due livelli annidati. Ad esempio: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. In questo modo, una delle tre entità per cui trovare una corrispondenza. 

Se Entity1 è un percorso con i ruoli, ad esempio origine (Seattle) e di destinazione (Cairo) e 2 di entità è un nome dell'edificio noti da un'entità di elenco (RedWest-C), le espressioni seguenti esegue il mapping a questo modello:

|Esempio di raggruppamento annidati utterance|Spiegazione|
|--|--|
|RedWest-C|corrisponde a entità esterne raggruppamento|
|Seattle|corrisponde a una delle entità di raggruppamento interna|
|Cairo|corrisponde a una delle entità di raggruppamento interna|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limiti di annidamento per i gruppi con sintattici facoltativi

Una combinazione di **raggruppamento** con **facoltativo** sintassi presenta un limite pari a 3 livelli di annidamento.

|Consentito|Esempio|
|--|--|
|Sì|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|No |( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limiti di annidamento per i gruppi con la sintassi o un'operazione

Una combinazione di **raggruppamento** con **o un'operazione** sintassi prevede un limite di 2 barre verticali.

|Consentito|Esempio|
|--|--|
|Sì|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|No |( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

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

Le parole del titolo del libro non sono fuorviante LUIS perché LUIS sappia dove termina il titolo del libro, basato sull'entità Pattern.any.

## <a name="explicit-lists"></a>Elenchi espliciti

creare un [elenco esplicito](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) tramite l'API di creazione e modifica per consentire l'eccezione quando:

* Il modello contiene un [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* E che tipo di sintassi consente la possibilità di un'estrazione di entità non corretto in base l'utterance. 

Ad esempio, si supponga che un criterio contenga sia la sintassi facoltativa, `[]`, che la sintassi dell'entità, `{}`, combinate in modo da estrarre i dati in modo non corretto.

Si consideri il criterio "[find] email about {subject} [from {person}]".

Nelle espressioni seguenti le entità **subject** e **person** vengono estratte in modo corretto e non corretto:

|Espressione|Entità|Estrazione corretta|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

Nella tabella precedente, il soggetto deve essere `the man from La Mancha` (a titolo di un libro) tuttavia, poiché l'oggetto include la parola facoltativa `from`, il titolo viene stimato in modo non corretto. 

Per risolvere questa eccezione nel criterio, aggiungere `the man from la mancha` come corrispondenza dell'elenco esplicito per l'entità {subject} usando l'[API di creazione per l'elenco esplicito](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Sintassi per contrassegnare un testo facoltativo in un'espressione del modello
Contrassegnare il testo facoltativo nell'espressione usando la sintassi tra parentesi quadre dell'espressione regolare, `[]`. Nel testo facoltativo possono essere nidificate un massimo di due parentesi quadre.

|Criteri con testo facoltativo|Significato|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` e `from {person}` sono facoltativi|
|'. Può aiutarmi [?]|Il segno di punteggiatura è facoltativo|

Segni di punteggiatura (`?`, `!`, `.`) devono essere ignorate ed è necessario ignorarli utilizzando la sintassi di parentesi quadre nei modelli. 

## <a name="pattern-only-apps"></a>Criterio di solo app
È possibile compilare un'app con gli Intent che non dispone di alcun espressioni di esempio, purché vi sia un modello per finalità di ogni. Per un'app solo modello, il modello non deve contenere le entità apprese macchina in quanto queste richiedono espressioni di esempio. 

## <a name="best-practices"></a>Procedure consigliate
Apprendere le [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esegui l'esercitazione per implementare i criteri](luis-tutorial-pattern.md)
