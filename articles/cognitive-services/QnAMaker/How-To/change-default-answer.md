---
title: Ottenere la risposta predefinita QnA Maker
description: Quando non esiste alcuna corrispondenza con la domanda, viene restituita la risposta predefinita. Potrebbe essere necessario modificare la risposta predefinita dalla risposta predefinita standard.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979972"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Modificare la risposta predefinita per una risorsa QnA Maker

La risposta predefinita per una Knowledge base deve essere restituita quando non viene trovata una risposta. Se si usa un'applicazione client, ad esempio il [servizio Azure bot](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), può anche avere una risposta predefinita separata, che indica che non è stata soddisfatta la soglia del punteggio.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Impostazione della risposta predefinita quando si crea la Knowledge base

Quando si crea una nuova Knowledge base, il testo di risposta predefinito è una delle impostazioni. Se si sceglie di non impostarlo durante il processo di creazione, è possibile modificarlo in un secondo momento con la procedura riportata di seguito.

## <a name="change-default-answer-in-qna-maker-portal"></a>Modificare la risposta predefinita nel portale QnA Maker

Quando non viene restituita alcuna risposta dal servizio QnA Maker, viene restituita la risposta predefinita della Knowledge base.

1. Accedere al portale di [QnA Maker](https://www.qnamaker.ai/) e selezionare la Knowledge base dall'elenco.
1. Selezionare **Impostazioni** nella barra di spostamento.
1. Modificare il valore del **testo della risposta predefinito** nella sezione **Gestisci Knowledge base** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Screenshot del portale di QnA Maker, pagina Impostazioni, con la casella di testo risposta predefinita evidenziata.":::

1. Selezionare **Salva e** Esegui il training per salvare la modifica.

## <a name="next-steps"></a>Passaggi successivi

* [Creare una knowledge base](../How-to/manage-knowledge-bases.md)