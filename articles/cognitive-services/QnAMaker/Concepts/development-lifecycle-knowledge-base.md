---
title: Ciclo di vita della knowledge base - QnA Maker
description: QnA Maker apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta dei dati dalle query degli endpoint.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 7bbf729fa80e4b41a85b8dfd1080decea1bae108
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612274"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo di vita della knowledge base in QnA Maker
QnA Maker apprende meglio in un ciclo iterativo di modifiche ai modelli, esempi di espressioni, pubblicazione e raccolta dei dati dalle query degli endpoint.

![Ciclo di creazione](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Creazione di una knowledge base di QnA Maker
L'endpoint della knowledge base (KB) di QnA Maker fornisce una risposta scelta in base alla corrispondenza migliore per una query utente basata sul contenuto della KB. La creazione di una Knowledge base è un'azione unica per la configurazione di un repository di contenuto di domande, risposte e metadati associati. È possibile creare una KB eseguendo la ricerca per indicizzazione del contenuto preesistente, ad esempio le origini seguenti:

- Pagine di domande frequenti
- Manuali di prodotti
- Coppie di domande e risposte

Leggere le informazioni su come [creare una knowledge base](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Test e aggiornamento della knowledge base

La knowledge base è pronta per i test dopo essere stata popolata con il contenuto, a livello editoriale o tramite estrazione automatica. Il test interattivo può essere eseguito nel portale di QnA Maker, tramite il pannello **test** . Immettere query utente comuni. Si verificherà quindi che le risposte restituite con la risposta corretta e un punteggio di confidenza sufficiente.


* **Per correggere i punteggi di confidenza basso**: aggiungere domande alternative.
* **Quando una query restituisce erroneamente la [risposta predefinita](../How-to/change-default-answer.md)**, aggiungere nuove risposte alla domanda corretta.

Questo ciclo serrato di test-aggiornamento continua finché non si è soddisfatti dei risultati. Leggere le informazioni su come [testare la knowledge base](../How-To/test-knowledge-base.md).

Per le KB di grandi dimensioni, utilizzare test automatizzati con l' [API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e la `isTest` proprietà Body, che esegue una query sulla `test` Knowledge base anziché sulla Knowledge base pubblicata.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Pubblicare la knowledge base
Una volta testata la knowledge base, è possibile pubblicarla. Publish esegue il push della versione più recente della Knowledge base verificata in un indice di ricerca cognitiva di Azure dedicato che rappresenta la Knowledge base **pubblicata** . Viene inoltre creato un endpoint che può essere chiamato nell'applicazione o nel chat bot.

A causa dell'azione di pubblicazione, eventuali ulteriori modifiche apportate alla versione di test della Knowledge base lasciano invariate le versioni pubblicate. La versione pubblicata potrebbe trovarsi in un'applicazione di produzione.

Ognuna di queste knowledge base può essere scelta come destinazione per i test separatamente. Utilizzando le API, è possibile specificare come destinazione la versione di prova della Knowledge base con la `isTest` proprietà Body nella chiamata generateAnswer.

Leggere le informazioni su come [pubblicare la knowledge base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorare l'utilizzo
Per poter registrare i log di chat del servizio, è necessario abilitare Application Insights quando si [crea un servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

È possibile ottenere diverse analisi relative all'utilizzo del servizio. Leggere altre informazioni su come usare Application Insights per ottenere [analisi per il servizio QnA Maker](../How-To/get-analytics-knowledge-base.md).

In base alle informazioni ottenute dall'analisi, apportare [aggiornamenti alla knowledge base](../How-To/edit-knowledge-base.md) come appropriato.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Controllo della versione per i dati nella Knowledge base

Il controllo della versione per i dati viene fornito tramite le funzionalità di importazione/esportazione nella pagina **Impostazioni** del portale di QnA Maker.

È possibile eseguire il backup di una Knowledge base esportando la Knowledge base, in `.tsv` `.xls` formato o. Una volta esportato, includere questo file come parte del normale controllo del controllo del codice sorgente.

Quando è necessario tornare a una versione specifica, è necessario importare il file dal sistema locale. Una Knowledge base esportata **deve** essere utilizzata solo tramite Import nella pagina **Settings** . Non può essere usato come origine dati del documento file o URL. In questo articolo vengono sostituite le domande e le risposte attualmente presenti nella Knowledge base con il contenuto del file importato.

## <a name="test-and-production-knowledge-base"></a>Knowledge base di test e produzione
Una Knowledge base è il repository di domande e set di risposte creati, gestiti e utilizzati tramite QnA Maker. Ogni risorsa QnA Maker può ospitare più Knowledge base.

Una Knowledge base dispone di due stati: *test* e *Published*.

### <a name="test-knowledge-base"></a>Testare la knowledge base

La *Knowledge base test* è la versione attualmente modificata e salvata. La versione di test è stata testata per verificarne l'accuratezza e per completezza delle risposte. Le modifiche apportate alla Knowledge base di test non influiscono sull'utente finale dell'applicazione o del bot di chat. La Knowledge base test è nota come `test` nella richiesta HTTP. Le `test` informazioni sono disponibili nel riquadro di **test** interattivo del portale QnA Maker.

### <a name="production-knowledge-base"></a>Knowledge base di produzione

La *Knowledge base pubblicata* è la versione usata nel bot o nell'applicazione di chat. La pubblicazione di una Knowledge base inserisce il contenuto della relativa versione di test nella versione pubblicata. La Knowledge base pubblicata è la versione utilizzata dall'applicazione tramite l'endpoint. Verificare che il contenuto sia corretto e testato correttamente. La Knowledge base pubblicata è nota come `prod` nella richiesta HTTP.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Suggerimenti per l'apprendimento attivo](./active-learning-suggestions.md)