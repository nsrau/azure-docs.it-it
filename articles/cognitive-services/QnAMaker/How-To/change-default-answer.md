---
title: Ottenere la risposta predefinita QnA Maker
description: Quando non esiste alcuna corrispondenza con la domanda, viene restituita la risposta predefinita. Potrebbe essere necessario modificare la risposta predefinita dalla risposta predefinita standard.
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: d37e63d84be58e6ccd2f1e23a1344961d39ffa01
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054175"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Modificare la risposta predefinita per una risorsa QnA Maker

La risposta predefinita per una Knowledge base deve essere restituita quando non viene trovata una risposta. Se si usa un'applicazione client, ad esempio il [servizio Azure bot](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), può anche avere una risposta predefinita separata, che indica che non è stata soddisfatta la soglia del punteggio.

## <a name="types-of-default-answer"></a>Tipi di risposta predefinita

Esistono due tipi di risposte predefinite nella Knowledge base. È importante comprendere come e quando ogni viene restituita da una query di stima:


|Tipo di domanda|Descrizione della risposta|
|--|--|
|Risposta KB quando non viene determinata alcuna risposta|`No good match found in KB.`-Quando l' [API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) non trova alcuna risposta corrispondente alla domanda, `DefaultAnswer` viene restituita l'impostazione del servizio app. Tutte le Knowledge base nella stessa risorsa QnA Maker condividono lo stesso testo di risposta predefinito.<br>È possibile gestire l'impostazione nel portale di Azure, tramite il servizio app o con le API REST per [ottenere](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings) o [aggiornare](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings) l'impostazione.|
|Testo istruzione richiesta di completamento|Quando si usa una richiesta di completamento in un flusso di conversazione, potrebbe non essere necessaria una risposta nella coppia di QnA perché si desidera che l'utente scelga tra le richieste di completamento. In questo caso, impostare un testo specifico impostando il testo di risposta predefinito, restituito con ogni stima per le richieste di completamento. Il testo deve essere visualizzato come testo istruttivo per la selezione di richieste di completamento. Un esempio di questo testo di risposta predefinito è `Please select from the following choices` . Questa configurazione è illustrata nelle sezioni successive di questo documento. Può anche essere impostato come parte della definizione della Knowledge base di `defaultAnswerUsedForExtraction` usando l' [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

### <a name="client-application-integration"></a>Integrazione di applicazioni client

Per un'applicazione client, ad esempio un bot con il **servizio Azure bot**, è possibile scegliere tra gli scenari comuni seguenti:

* Utilizzare l'impostazione della Knowledge base
* Usare un testo diverso nell'applicazione client per distinguere quando viene restituita una risposta ma non soddisfa la soglia del punteggio. Questo testo può essere testo statico archiviato nel codice oppure può essere archiviato nell'elenco delle impostazioni dell'applicazione client.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Imposta la risposta predefinita della richiesta di completamento quando si crea la Knowledge base

Quando si crea una nuova Knowledge base, il testo di risposta predefinito è una delle impostazioni. Se si sceglie di non impostarlo durante il processo di creazione, è possibile modificarlo in un secondo momento con la procedura riportata di seguito.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>Modificare la risposta predefinita della richiesta di completamento nel portale QnA Maker

Quando non viene restituita alcuna risposta dal servizio QnA Maker, viene restituita la risposta predefinita della Knowledge base.

1. Accedere al portale di [QnA Maker](https://www.qnamaker.ai/) e selezionare la Knowledge base dall'elenco.
1. Selezionare **Impostazioni** nella barra di spostamento.
1. Modificare il valore del **testo della risposta predefinito** nella sezione **Gestisci Knowledge base** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Screenshot del portale di QnA Maker, pagina Impostazioni, con la casella di testo risposta predefinita evidenziata.":::

1. Selezionare **Salva e** Esegui il training per salvare la modifica.

## <a name="next-steps"></a>Passaggi successivi

* [Creare una knowledge base](../How-to/manage-knowledge-bases.md)
