---
title: Ciclo di vita della knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta dei dati dalle query degli endpoint.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 530869928f7a25e779cb01f0fe392efdbb54c5ba
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695106"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo di vita della knowledge base in QnA Maker
QnA Maker apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta dei dati dalle query degli endpoint. 

![Ciclo di creazione](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Creazione di una knowledge base di QnA Maker
L'endpoint della knowledge base (KB) di QnA Maker fornisce una risposta scelta in base alla corrispondenza migliore per una query utente basata sul contenuto della KB. La creazione di una knowledge base è un'operazione occasionale di configurazione di un repository di contenuti con domande, risposte e i metadati associati. È possibile creare una knowledge effettuando una ricerca per indicizzazione nel contenuto pre-esistente, ad esempio pagine di domande frequenti, manuali di prodotti o coppie strutturate di domande e risposte. Leggere le informazioni su come [creare una knowledge base](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Test e aggiornamento della knowledge base

La knowledge base è pronta per i test dopo essere stata popolata con il contenuto, a livello editoriale o tramite estrazione automatica. Il test interattivo può essere eseguito nel portale di QnA Maker tramite il pannello di **test** immettendo query utente comuni e verificando che le risposte restituite con la risposta corretta e un punteggio di confidenza sufficiente. 

* **Per correggere i punteggi di confidenza basso**: aggiungere domande alternative. 
* **Quando una query restituisce erroneamente la [risposta predefinita](confidence-score.md#change-default-answer)** , aggiungere nuove risposte alla domanda corretta. 

Questo ciclo serrato di test-aggiornamento continua finché non si è soddisfatti dei risultati. Leggere le informazioni su come [testare la knowledge base](../How-To/test-knowledge-base.md).

Per i dati di grandi dimensioni, utilizzare test automatizzati con l' [API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e la proprietà del corpo `isTest` che esegue una query sulla knowledge base `test` invece che sulla Knowledge base pubblicata. 

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

Ognuna di queste knowledge base può essere scelta come destinazione per i test separatamente. Utilizzando le API, è possibile specificare come destinazione la versione di prova della Knowledge base con la proprietà del corpo `isTest` nella chiamata generateAnswer.

Leggere le informazioni su come [pubblicare la knowledge base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorare l'utilizzo
Per poter registrare i log di chat del servizio, è necessario abilitare Application Insights quando si [crea un servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

È possibile ottenere diverse analisi relative all'utilizzo del servizio. Leggere altre informazioni su come usare Application Insights per ottenere [analisi per il servizio QnA Maker](../How-To/get-analytics-knowledge-base.md).

In base alle informazioni ottenute dall'analisi, apportare [aggiornamenti alla knowledge base](../How-To/edit-knowledge-base.md) come appropriato.

## <a name="version-control-of-a-knowledge-base"></a>Controllo della versione di una Knowledge base

Il controllo della versione non viene fornito dal QnA Maker. È necessario esportare la Knowledge base dalla pagina **Impostazioni** e utilizzare la metodologia e gli strumenti personalizzati.

L'esportazione della Knowledge base nel formato TSV o XLS viene completata dalla pagina **Impostazioni** . 

Quando è necessario tornare a una versione specifica, è necessario importare il file dal sistema locale. Dalla pagina **Impostazioni** importare il file TSV o xls. In questo articolo vengono sostituite le domande e le risposte attualmente presenti nella Knowledge base con il contenuto del file importato.   

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Punteggio di attendibilità](./confidence-score.md)

## <a name="see-also"></a>Vedere anche 

[Knowledge base](./knowledge-base.md)
[Panoramica di QnA Maker](../Overview/overview.md)
