---
title: Supporto delle lingue - QnA Maker
titleSuffix: Azure Cognitive Services
description: Elenco delle impostazioni cultura e dei linguaggi naturali supportati da QnA Maker per la knowledge base. Non combinare linguaggi diversi nella stessa knowledge base.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: dc910c01c401468a3dae392a6318344bee25efb7
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375641"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Supporto delle lingue per una risorsa QnA Maker e Knowledge base

Questo articolo descrive le opzioni di supporto del linguaggio per le risorse QnA Maker e le Knowledge base. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versione stabile)](#tab/v1)

La lingua per il servizio viene selezionata quando si crea la prima Knowledge base nella risorsa. Tutte le Knowledge base aggiuntive nella risorsa devono essere nella stessa lingua. 

La lingua determina la pertinenza dei risultati QnA Maker fornisce in risposta alle query degli utenti. La risorsa QnA Maker e tutte le Knowledge base all'interno di tale risorsa supportano una sola lingua. Il linguaggio singolo è necessario per fornire i risultati ottimali per la risposta a una query.

# <a name="qna-maker-managed-preview-release"></a>[Gestione QnA Maker (versione di anteprima)](#tab/v2)

In QnA Maker gestiti è possibile scegliere di rendere le impostazioni della lingua a livello di singolo Knowledge base. Questa impostazione può essere abilitata solo con la Knowledge base del servizio. Una volta impostato, non è possibile modificare le impostazioni della lingua per il servizio. 

Se si seleziona Impostazioni lingua come specifiche della Knowledge base, è possibile creare le basi di conoscenza di lingue diverse nel servizio stesso. 

---

## <a name="single-language-per-resource"></a>Singola lingua per risorsa

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versione stabile)](#tab/v1)

Considerare quanto segue:

* Un servizio QnA Maker e tutte le relative Knowledge base supportano solo una lingua.
* La lingua viene impostata in modo esplicito al momento della creazione della prima Knowledge base del servizio.
* Il linguaggio è determinato dai file e dagli URL aggiunti al momento della creazione della Knowledge base.
* Non è possibile modificare la lingua per altre Knowledge base nel servizio.
* Il linguaggio viene usato dal servizio ricerca cognitiva (Ranker #1) e dal servizio di QnA Maker (Ranker #2) per generare la risposta migliore a una query.

# <a name="qnamaker-managed-preview"></a>[Gestito da QnAMaker (anteprima)](#tab/v2)
![Impostazione della lingua in QnA Maker gestita](../media/language-support/language-setting-managed.png)

Se **non si seleziona la casella di controllo per abilitare l'impostazione della lingua per la Knowledge base** , tenere presente quanto segue: 
* Un servizio QnA Maker e tutte le relative Knowledge base supporteranno solo una lingua.
* La lingua viene impostata in modo esplicito al momento della creazione della prima Knowledge base del servizio
* Il linguaggio è determinato dai file e dagli URL aggiunti quando viene creata la Knowledge base
* Non è possibile modificare la lingua per altre Knowledge base nel servizio
* Il linguaggio viene usato dal servizio ricerca cognitiva (Ranker #1) e dal servizio QnA Maker (Ranker #2) per generare la risposta migliore a una query

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Supporto di più lingue in una risorsa QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versione stabile)](#tab/v1)
Questa funzionalità non è supportata nella versione stabile attualmente disponibile a livello generale (GA). Consultare QnA Maker Managed per testare questa funzionalità. 

# <a name="qna-maker-managed-preview-release"></a>[Gestione QnA Maker (versione di anteprima)](#tab/v2)
* Quando si crea la prima Knowledge base nel servizio, è possibile scegliere di abilitare l'impostazione della lingua per ogni Knowledge base. Selezionare la casella di controllo per creare Knowledge base appartenenti a lingue diverse all'interno di un servizio.
* Non è possibile modificare l'opzione di impostazione della lingua per il servizio, una volta creata la prima Knowledge base.
* Se si abilitano impostazioni della lingua specifiche per ogni Knowledge base, anziché disporre di un indice di test per il servizio, sarà presente un indice di test per ogni Knowledge base. 

![Impostazione della lingua in QnA Maker gestita](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Supporto di più lingue in una Knowledge base

Se è necessario supportare un sistema Knowledge base, che include diverse lingue, è possibile:

* Utilizzare il [servizio di conversione](../../translator/translator-info-overview.md) per tradurre una domanda in una sola lingua prima di inviare la domanda alla Knowledge base. In questo modo è possibile concentrarsi sulla qualità di una singola lingua e sulla qualità delle domande e risposte alternative.
* Creare una risorsa di QnA Maker e una Knowledge base all'interno di tale risorsa per ogni lingua. In questo modo è possibile gestire le domande alternative separate e il testo di risposta più sfumato per ogni lingua. In questo modo si ottiene una maggiore flessibilità, ma è necessario un costo di manutenzione molto più elevato quando le domande o le risposte cambiano in tutte le lingue.


## <a name="languages-supported"></a>Lingue supportate

L'elenco seguente contiene le lingue supportate per una risorsa QnA Maker. 

|Linguaggio|
|--|
|Arabo|
|Armeno|
|Bengalese|
|Basco|
|Bulgaro|
|Catalano|
|Cinese semplificato|
|Cinese tradizionale|
|Croato|
|Ceco|
|Danese|
|Olandese|
|Inglese|
|Estone|
|Finlandese|
|Francese|
|Galiziano|
|Tedesco|
|Greco|
|Gujarati|
|Ebraico|
|Hindi|
|Ungherese|
|Islandese|
|Indonesiano|
|Irlandese|
|Italiano|
|Giapponese|
|Kannada|
|Coreano|
|Lettone|
|Lituano|
|Malayalam|
|Malese|
|Norvegese|
|Polacco|
|Portoghese|
|Punjabi|
|Romeno|
|Russo|
|Serbo (alfabeto cirillico)|
|Serbo (alfabeto latino)|
|Slovacco|
|Sloveno|
|Spagnolo|
|Svedese|
|Tamil|
|Telugu|
|Thai|
|Turco|
|Ucraino|
|Urdu|
|Vietnamita|

## <a name="query-matching-and-relevance"></a>Query corrispondente e pertinenza
QnA Maker dipende dagli [analizzatori del linguaggio ricerca cognitiva di Azure](https://docs.microsoft.com/rest/api/searchservice/language-support) per fornire i risultati.

Sebbene le funzionalità di ricerca cognitiva di Azure siano in par per le lingue supportate, QnA Maker dispone di un rango aggiuntivo che si trova sopra i risultati di ricerca di Azure. In questo modello di classificazione vengono usate alcune funzionalità speciali basate su parole e semantiche nei linguaggi seguenti.

|Lingue con rango aggiuntivo|
|--|
|Cinese|
|Ceco|
|Olandese|
|Inglese|
|Francese|
|Tedesco|
|Ungherese|
|Italiano|
|Giapponese|
|Coreano|
|Polacco|
|Portoghese|
|Spagnolo|
|Svedese|

Questo rango aggiuntivo è un lavoro interno del rango del QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Selezione della lingua](../how-to/language-knowledge-base.md)
