---
title: Integrazione con altre applicazioni - QnA Maker
description: QnA Maker si integra nelle applicazioni client, come i bot di chat e con altri servizi di elaborazione del linguaggio naturale, ad esempio LuiS (Language Understanding).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804189"
---
# <a name="design-knowledge-base-for-client-applications"></a>Progettare la Knowledge Base per le applicazioni client

QnA Maker si integra nelle applicazioni client, come i bot di chat e con altri servizi di elaborazione del linguaggio naturale, ad esempio LuiS (Language Understanding).

## <a name="integration-with-a-conversational-client"></a>Integrazione con un cliente conversazionale

QnA Maker si integra con applicazioni client conversazionali come [Microsoft Bot Framework](https://dev.botframework.com/). Il testo inviato a QnA Maker non deve essere pulito o trasformato. QnA Maker accetta le lingue naturali e restituisce la risposta migliore.

## <a name="create-a-bot-without-writing-any-code"></a>Creare un bot senza scrivere codiceCreate a bot without writing any code

Dopo aver pubblicato la Knowledge Base, creare un bot dalla pagina **Pubblica** selezionando il pulsante **Crea bot.** Usare [l'esercitazione](../Quickstarts/create-publish-knowledge-base.md) sul bot per scoprire cosa accade dopo aver selezionato il pulsante.

## <a name="providing-multi-turn-conversations"></a>Fornire conversazioni a più turni

Un client bot fornisce la migliore risposta selezionata dalla Knowledge Base e può fornire prompt di follow-up se la risposta fa parte di una coppia QnA a più turni. [Scopri come](../how-to/multiturn-conversation.md) aggiungere gruppi di domande e risposte di conversazione su più turni alla tua Knowledge Base.

## <a name="natural-language-processing"></a>Elaborazione del linguaggio naturale

Mentre QnA Maker elabora domande che utilizzano l'elaborazione del linguaggio naturale, può anche essere utilizzato una parte di un sistema più ampio che risponde alle domande da più basi di conoscenza. È possibile combinare QnA Maker con un altro servizio cognitivo, Language Understanding (LUIS), per fornire l'elaborazione del linguaggio naturale prima di accedere a una Knowledge Base specifica. Scopri di più su quando e come usare [LUIS e QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) insieme.

## <a name="next-steps"></a>Passaggi successivi

Imparare i concetti del ciclo [di](development-lifecycle-knowledge-base.md) sviluppo per QnA Maker.