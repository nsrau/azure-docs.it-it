---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.author: diberry
ms.openlocfilehash: 02610e647e2138cbf52f86c22107feec2d61273b
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604946"
---
Servizi cognitivi fornisce due servizi di elaborazione del linguaggio naturale, [Language Understanding](../luis/what-is-luis.md) e [QnA Maker,](../qnamaker/overview/overview.md)ognuno con uno scopo diverso. Capire quando utilizzare ogni servizio e come si complimentano a vicenda.

L'elaborazione del linguaggio naturale (NLP) consente all'applicazione client, ad esempio un chat bot, di lavorare con gli utenti, usando il linguaggio naturale. Un utente immette una frase o una frase. Il testo dell'utente può avere una grammatica, un'ortografia e una punteggiatura insufficienti. Il servizio cognitivo può comunque eseguire la frase utente, restituendo informazioni necessarie al bot di chat per aiutare l'utente.

## <a name="cognitive-services-with-nlp"></a>Servizi cognitivi con NLP

Language Understanding (LUIS) e QnA Maker forniscono NLP. L'applicazione client invia il testo in linguaggio naturale. Il servizio accetta il testo, lo elabora e restituisce un risultato.

## <a name="when-to-use-each-service"></a>Quando utilizzare ogni servizio

Language Understanding (LUIS) e QnA Maker risolvono diversi problemi. LUIS determina lo scopo del testo di un utente (noto come espressione), mentre QnA Maker determina la risposta al testo di un utente (noto come query).

Per scegliere il servizio corretto, è necessario comprendere il testo dell'utente proveniente dall'applicazione client e le informazioni necessarie per l'applicazione client dal servizio di gestione cognitiva.

Se il bot di `How do I get to the Human Resources building on the Seattle North campus?`chat riceve il testo, usare la tabella seguente per comprendere il funzionamento di ogni servizio con il testo.

|Service|L'applicazione client determina|
|--|--|
|LUIS|**Determina l'intenzione del testo dell'utente:** il servizio non restituisce la risposta alla domanda. Ad esempio, questo testo viene `FindLocation` classificato come corrispondente alla finalità.<br>|
|QnA Maker|**Restituisce la risposta alla domanda** da una Knowledge Base personalizzata. Ad esempio, questo testo viene determinato come una `Get on the #9 bus and get off at Franklin street`domanda con la risposta di testo statico di .|
|||

## <a name="when-do-you-use-luis"></a>Quando si utilizza LUIS?

Usare LUIS quando è necessario conoscere l'intenzione dell'espressione come parte di un processo nel bot di chat. Continuando con il `How do I get to the Human Resources building on the Seattle North campus?`testo di esempio, , una volta che si conosce l'intenzione dell'utente è trovare una posizione, è possibile passare i dettagli sull'espressione (tirata con entità) a un altro servizio, ad esempio un server di trasporto, per ottenere la risposta.

Non è necessario combinare LUIS e QnA Maker per determinare le finalità.

È possibile combinare i due servizi per questa espressione, se il bot di chat deve elaborare il testo in base alle intenzioni e alle entità (utilizzando LUIS) e trovare la risposta di testo statico specifico (tramite QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Quando si utilizza QnA Maker?

Utilizzare QnA Maker quando si dispone di una knowledge base statica di risposte. Questa knowledge base è personalizzata in base alle proprie esigenze ed è stata creata con documenti come PDF e URL.

Continuando con l'espressione di esempio, `How do I get to the Human Resources building on the Seattle North campus?`inviare il testo, come query, al servizio QnA Maker pubblicato e ricevere la risposta migliore.

Non è necessario combinare LUIS e QnA Maker per determinare la risposta alla domanda.

È possibile combinare i due servizi per questa espressione, se il bot di chat deve elaborare il testo in base alle intenzioni e alle entità (utilizzando LUIS) e trovare la risposta (tramite QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Utilizzare entrambi i servizi quando la Knowledge Base è incompleta

Se si sta creando la Knowledge Base di QnA Maker ma si sa che il dominio soggetto sta cambiando (ad esempio informazioni tempestive), è possibile combinare i servizi LUIS e QnA Maker. In questo modo è possibile utilizzare le informazioni nella Knowledge Base, ma anche utilizzare LUIS per determinare l'intenzione di un utente. Una volta che l'applicazione client ha l'intenzione, può richiedere informazioni rilevanti da un'altra fonte.

L'applicazione client dovrebbe monitorare le risposte di LUIS e QnA Maker per i punteggi. Se il punteggio di QnA Maker è inferiore a una soglia arbitraria, usare le informazioni sulle finalità e sull'entità restituite da LUIS per passare le informazioni a un servizio di terze parti.

Continuando con il `How do I get to the Human Resources building on the Seattle North campus?`testo di esempio, , si supponga che QnA Maker restituisca un punteggio di confidenza basso. Utilizzare la finalità restituita `FindLocation` da LUIS e tutte `Human Resources building` `Seattle North campus`le entità estratte, ad esempio e , per inviare queste informazioni a un servizio di mapping o di ricerca per un'altra risposta.

È possibile presentare questa risposta di terze parti all'utente per la convalida. Una volta che hai l'approvazione dell'utente, puoi tornare a QnA Maker per aggiungere le informazioni per far crescere le tue conoscenze.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Usare entrambi i servizi quando il bot di chat necessita di ulteriori informazioniUse both services when your chat bot needs more information

Se il bot di chat necessita di più informazioni di quelle fornite da uno dei due servizi, per continuare con un albero delle decisioni, usare entrambi i servizi ed elaborare entrambe le risposte nell'applicazione client.

Usare lo strumento Bot framework **[Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** per compilare un processo per l'utilizzo di entrambi i servizi. Questo strumento crea un'applicazione LUIS superiore di finalità che invia tra LUIS e QnA Maker come applicazioni figlio.

Utilizzare l'esempio del generatore di bot, **NLP with dispatch**, in [C'](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) o [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), per implementare questo tipo di bot di chat.

## <a name="best-practices"></a>Procedure consigliate

Implementare le procedure consigliate per ogni servizio:Implement best practices for each service:

* Procedure consigliate [per LUIS](../luis/luis-concept-best-practices.md)
* Best practice [di QnA Maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Vedere anche

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Interfaccia di comando di spedizione](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Esempi di framework botBot framework samples](https://github.com/Microsoft/BotBuilder-Samples)
* [Servizio bot di AzureAzure bot service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Emulatore bot di AzureAzure bot emulator](https://github.com/Microsoft/BotFramework-Emulator)
* [Chat Web del framework bot](https://github.com/microsoft/BotFramework-WebChat)