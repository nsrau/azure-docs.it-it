---
title: Ciclo di vita della knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta dei dati dalle query degli endpoint.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3f78b8a2566137d596f4ab3f083e1d14289365c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61379284"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo di vita della knowledge base in QnA Maker
QnA Maker apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta dei dati dalle query degli endpoint. 

![Ciclo di creazione](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Creazione di una knowledge base di QnA Maker
L'endpoint della knowledge base (KB) di QnA Maker fornisce una risposta scelta in base alla corrispondenza migliore per una query utente basata sul contenuto della KB. La creazione di una knowledge base è un'operazione occasionale di configurazione di un repository di contenuti con domande, risposte e i metadati associati. È possibile creare una knowledge effettuando una ricerca per indicizzazione nel contenuto pre-esistente, ad esempio pagine di domande frequenti, manuali di prodotti o coppie strutturate di domande e risposte. Leggere le informazioni su come [creare una knowledge base](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Test e aggiornamento della knowledge base

La knowledge base è pronta per i test dopo essere stata popolata con il contenuto, a livello editoriale o tramite estrazione automatica. Test interattiva possono essere eseguiti nel portale di QnA Maker tramite il **Test** pannello immettendo le query utente comuni e verifica che le risposte restituite con la risposta corretta e il punteggio di confidenza sufficienti. 

* **Per correggere i punteggi di confidenza basso in**: aggiungere domande alternative. 
* **Quando una query restituisce in modo errato il [risposta predefinita](confidence-score.md#change-default-answer)** : aggiungere nuove risposte per la domanda corretta. 

Questo ciclo serrato di test-aggiornamento continua finché non si è soddisfatti dei risultati. Leggere le informazioni su come [testare la knowledge base](../How-To/test-knowledge-base.md).

Per la Knowledge base di grandi dimensioni, utilizzare test automatici con il [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e il `isTest` proprietà body le query che il `test` della knowledge base anziché knowledge base pubblicata. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Pubblicare la knowledge base
Una volta testata la knowledge base, è possibile pubblicarla. La pubblicazione inserisce la versione più recente della knowledge base testata in un indice di Ricerca di Azure dedicato che rappresenta la knowledge base **pubblicata**. Viene inoltre creato un endpoint che può essere chiamato nell'applicazione o nel chat bot.

In questo modo, eventuali modifiche apportate alla versione di test della knowledge base non influiscono sulla versione pubblicata, che potrebbe essere in uso in un'applicazione di produzione.

Ognuna di queste knowledge base può essere scelta come destinazione per i test separatamente. Usando le API, è possibile assegnare la versione di prova della knowledge base con `isTest` proprietà nella chiamata generateAnswer body.

Leggere le informazioni su come [pubblicare la knowledge base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorare l'utilizzo
Per poter registrare i log di chat del servizio, è necessario abilitare Application Insights quando si [crea un servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

È possibile ottenere diverse analisi relative all'utilizzo del servizio. Leggere altre informazioni su come usare Application Insights per ottenere [analisi per il servizio QnA Maker](../How-To/get-analytics-knowledge-base.md).

In base alle informazioni ottenute dall'analisi, apportare [aggiornamenti alla knowledge base](../How-To/edit-knowledge-base.md) come appropriato.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Punteggio di attendibilità](./confidence-score.md)

## <a name="see-also"></a>Vedere anche 

[Knowledge base](./knowledge-base.md)
[Panoramica di QnA Maker](../Overview/overview.md)
