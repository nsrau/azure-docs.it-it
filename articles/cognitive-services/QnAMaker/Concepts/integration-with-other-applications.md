---
title: Integrazione con altre applicazioni-QnA Maker
description: QnA Maker si integra con le applicazioni client, come le chat bot e con altri servizi di elaborazione del linguaggio naturale come Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 38b275aa2b8cf5768a2bc95634a7ff4892893eda
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300520"
---
# <a name="design-knowledge-base-for-client-applications"></a>Progettare la Knowledge base per le applicazioni client

QnA Maker si integra con le applicazioni client, come le chat bot e con altri servizi di elaborazione del linguaggio naturale come Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integrazione con un client colloquiale

QnA Maker si integra con applicazioni client di conversazione, ad esempio [Microsoft bot Framework](https://dev.botframework.com/). Non è necessario pulire o trasformare il testo inviato a QnA Maker. QnA Maker accetta i linguaggi naturali e restituisce la risposta migliore.

## <a name="create-a-bot-without-writing-any-code"></a>Crea un bot senza scrivere codice

Dopo aver pubblicato la Knowledge base, creare un bot dalla pagina **pubblica** selezionando il pulsante **Crea bot** . Usare l' [esercitazione bot](../Quickstarts/create-publish-knowledge-base.md) per apprendere cosa accade dopo aver selezionato il pulsante.

## <a name="providing-multi-turn-conversations"></a>Fornire conversazioni a più turni

Un client bot fornisce la migliore risposta selezionata dalla Knowledge base e può fornire richieste di completamento se la risposta fa parte di un set di QnA a più turni. Informazioni su [come](../how-to/multiturn-conversation.md) aggiungere set di risposte e domande a più turni alla Knowledge base.

## <a name="natural-language-processing"></a>Elaborazione del linguaggio naturale

Mentre QnA Maker elabora domande che usano l'elaborazione del linguaggio naturale, può anche essere usato come parte di un sistema più ampio che risponde alle domande da più Knowledge base. È possibile combinare QnA Maker con un altro servizio cognitivo, Language Understanding (LUIS), per fornire l'elaborazione del linguaggio naturale prima di arrivare a una Knowledge base specifica. Altre informazioni su quando e come usare [Luis e QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) insieme.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [concetti](development-lifecycle-knowledge-base.md) del ciclo di sviluppo per QnA Maker.