---
title: Cos'è il servizio QnA Maker?
description: QnA Maker è un servizio NLP basato sul cloud che consente di creare facilmente un livello di conversazione naturale sui dati. Può essere usato per trovare la risposta più appropriata per qualsiasi tipo specifico di input in linguaggio naturale della knowledge base (KB) personalizzata di informazioni.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 05/26/2020
ms.custom: cog-serv-seo-aug-2020
keywords: qna maker, chatbot con poco codice, conversazioni a più turni
ms.openlocfilehash: 6a5ea51086e3ab532966c9cea9eb866334494bba
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874425"
---
# <a name="what-is-qna-maker"></a>Informazioni su QnA Maker

QnA Maker è un servizio di elaborazione del linguaggio naturale (NLP, Natural Language Processing) basato sul cloud che consente di creare un livello di conversazione naturale sui dati. Si usa per trovare la risposta più appropriata per qualsiasi tipo specifico di input della knowledge base (KB) personalizzata di informazioni.

QnA Maker viene solitamente usato per sviluppare applicazioni client conversazionali, che includono applicazioni di social media, chatbot e applicazioni desktop abilitate per il riconoscimento vocale.

## <a name="when-to-use-qna-maker"></a>Quando usare QnA Maker

* **Quando si hanno informazioni statiche**: usare QnA Maker quando la knowledge base di risposte contiene informazioni statiche. Questa knowledge base è personalizzata in base alle proprie esigenze ed è stata creata con documenti come [PDF e URL](../concepts/content-types.md).
* **Quando si vuole fornire la stessa risposta a una richiesta, a una domanda o a un comando**: se diversi utenti inviano la stessa domanda, viene restituita la stessa risposta.
* **Quando si vogliono filtrare informazioni statiche in base a meta informazioni**: aggiungere tag di [metadati](../how-to/metadata-generateanswer-usage.md) per fornire ulteriori opzioni di filtro pertinenti per gli utenti dell'applicazione client e per le informazioni. Le informazioni comuni dei metadati includono [chiacchiere](../how-to/chit-chat-knowledge-base.md), tipo o formato, scopo e aggiornamenti del contenuto.
* **Quando si vuole gestire una conversazione con bot che include informazioni statiche**: la knowledge base fornisce una risposta al comando o al testo della conversazione dell'utente. Se la risposta fa parte di un flusso di conversazione predeterminato, rappresentato nella knowledge base con un [contesto a più turni](../how-to/multiturn-conversation.md), il bot può fornire facilmente questo flusso.

## <a name="what-is-a-knowledge-base"></a>Cos'è una knowledge base?

QnA Maker [importa il contenuto](../concepts/knowledge-base.md) in una knowledge base costituita da una coppia di domande e risposte. Il processo di importazione estrae informazioni sulla relazione tra le parti del contenuto strutturato e semistrutturato in modo da implicare le relazioni tra le coppie di domande e risposte. È possibile modificare queste coppie di domande e risposte o aggiungerne di nuove.

Il contenuto della coppia di domande e risposte include:
* Tutte le forme alternative della domanda
* Tag dei metadati usati per filtrare le opzioni di risposta durante la ricerca
* Richieste di completamento per continuare l'affinamento della ricerca

![Esempio di domanda e risposta con metadati](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Dopo aver pubblicato la knowledge base, un'applicazione client invia la domanda di un utente all'endpoint. Il servizio QnA Maker elabora la domanda e restituisce la risposta migliore.

## <a name="create-a-chat-bot-programmatically"></a>Creare un chatbot a livello di codice

Una volta pubblicata una knowledge base di QnA Maker, un'applicazione client invia una domanda al relativo endpoint e riceve i risultati come risposta JSON. Un'applicazione client comune per QnA Maker è un chatbot.

![Inviare una domanda a un bot e ottenere una risposta dal contenuto della knowledge base](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Passaggio|Azione|
|:--|:--|
|1|L'applicazione client invia la _domanda_ dell'utente (testo in parole proprie), "How do I programmatically update my Knowledge Base?" all'endpoint della knowledge base.|
|2|QnA Maker usa la knowledge base sottoposta a training per fornire la risposta corretta e le eventuali richieste di completamento che possono servire per affinare la ricerca della risposta migliore. QnA Maker restituisce una risposta in formato JSON.|
|3|L'applicazione client usa la risposta JSON per prendere decisioni su come continuare la conversazione. Queste decisioni possono includere la visualizzazione della risposta principale e la presentazione di più scelte per affinare la ricerca della risposta migliore. |
|||

## <a name="build-low-code-chat-bots"></a>Sviluppare chatbot con poco codice

Il portale di QnA Maker offre l'esperienza completa di creazione di knowledge base. È possibile importare documenti, nel formato corrente, nella knowledge base. Questi documenti, ad esempio domande frequenti, manuali di prodotti, fogli di calcolo o pagine Web, vengono convertiti in coppie di domande e risposte. Ogni coppia viene analizzata per trovare richieste di completamento e viene collegata ad altre coppie. Il formato _Markdown_ finale supporta una presentazione avanzata, con immagini e collegamenti.

Una volta modificata la knowledge base, pubblicarla in un [bot app Web di Azure](https://azure.microsoft.com/services/bot-service/) funzionante senza scrivere codice. Testare il bot nel [portale di Azure](https://portal.azure.com) oppure scaricarlo e continuare con lo sviluppo.

## <a name="high-quality-responses-with-layered-ranking"></a>Risposte di alta qualità con la classificazione a più livelli

Il sistema di QnA Maker è un approccio di classificazione a più livelli. I dati vengono archiviati in Ricerca di Azure, che funge anche da primo livello di classificazione. I risultati principali di Ricerca di Azure vengono quindi passati attraverso il modello di riclassificazione NLP di QnA Maker per produrre i risultati finali e il punteggio di confidenza.

## <a name="multi-turn-conversations"></a>Conversazioni a più turni

QnA Maker offre richieste a più turni e apprendimento attivo per migliorare le coppie di domande e risposte di base.

Le **richieste a più turni** offrono la possibilità di collegare coppie di domande e risposte. Questo collegamento consente all'applicazione client di fornire una risposta principale e aggiunge altre domande per affinare la ricerca di una risposta finale.

Quando la knowledge base riceve le domande dagli utenti nell'endpoint pubblicato, QnA Maker applica l'**apprendimento attivo** a queste domande reali per suggerire le modifiche da apportare alla knowledge base per migliorare la qualità.

## <a name="development-lifecycle"></a>Ciclo di vita di sviluppo

QnA Maker fornisce funzionalità di creazione, training e pubblicazione oltre alle autorizzazioni per la collaborazione per integrarsi nell'intero ciclo di via di sviluppo.

> [!div class="mx-imgBorder"]
> ![Immagine concettuale del ciclo di sviluppo](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="complete-a-quickstart"></a>Completare una guida di avvio rapido

Sono disponibili guide di avvio rapido nei linguaggi di programmazione più diffusi, ognuna progettata per insegnare gli schemi progettuali di base e consentire all'utente di eseguire il codice in meno di 10 minuti. Per accedere alla guida di avvio rapido per ogni funzionalità, vedere l'elenco seguente.

* [Introduzione alla libreria client di QnA Maker](../quickstarts/quickstart-sdk.md)
* [Introduzione al portale di QnA Maker](../quickstarts/create-publish-knowledge-base.md)
* [Introduzione alle API REST di QnA Maker](../quickstarts/quickstart-rest-curl.md)


## <a name="next-steps"></a>Passaggi successivi
QnA Maker offre tutto il necessario per creare, gestire e distribuire una knowledge base personalizzata.

> [!div class="nextstepaction"]
> [Esaminare le modifiche più recenti](../whats-new.md)
