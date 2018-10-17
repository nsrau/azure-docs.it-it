---
title: Informazioni su QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker è un servizio API basato sul cloud che applica l'intelligence dell'apprendimento automatico personalizzato alla domanda in linguaggio naturale posta da un utente per fornire la risposta più appropriata.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 742c18815445b038e85c33a96743790491976945
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901108"
---
# <a name="what-is-qna-maker"></a>Informazioni su QnA Maker

QnA Maker è un servizio della knowledge base per la gestione di domande e risposte che applica l'intelligence dell'apprendimento automatico personalizzato alla domanda in linguaggio naturale posta da un utente per determinare la risposta più appropriata.

Con QnA Maker il servizio basato sul cloud viene sviluppato a partire dal contenuto semistrutturato disponibile, come documenti di domande frequenti oppure URL, manuali di prodotti e domande e risposte personalizzate. Grazie al [portale Web](https://qnamaker.ai) di facile utilizzo è possibile creare, gestire, eseguire il training e pubblicare il servizio anche senza essere uno sviluppatore. Dopo la pubblicazione del servizio in un endpoint, è possibile usare un'applicazione client, ad esempio un chatbot, per gestire la conversazione con un utente per ottenere le domande e fornire le risposte. 

![Panoramica](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Processi chiave di QnA Maker

QnA Maker offre due servizi chiave per i dati:

* **Estrazione**: i dati di tipo domanda-risposta strutturati vengono estratti da [origini dati](../Concepts/data-sources-supported.md) semistrutturate, ad esempio domande frequenti e manuali di prodotti. Questa estrazione può essere eseguita durante la [creazione](https://aka.ms/qnamaker-docs-createkb) della knowledge base oppure in un secondo momento, durante il processo di modifica.

* **Corrispondenza**: dopo avere [eseguito il training e il test](https://aka.ms/qnamaker-docs-trainkb) della Knowledge Base, la si [pubblica](https://aka.ms/qnamaker-docs-publishkb). Questa operazione abilita un endpoint alla knowledge base di QnA Maker, che è quindi possibile usare nel bot o nell'app client. Questo endpoint accetta una domanda di un utente e restituisce la risposta migliore della knowledge base, indicando anche un punteggio di attendibilità per la corrispondenza.

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>Architettura di QnA Maker

QnA Maker è costituito dai servizi API seguenti:

1. **Servizi di gestione di QnA Maker**: esperienza di gestione per una knowledge base di QnA Maker, che include la creazione iniziale, l'aggiornamento, il training e la pubblicazione. Queste attività possono essere eseguite tramite il [portale](https://qnamaker.ai) o le [API di gestione](https://aka.ms/qnamaker-v4-apis). 

2. **Servizio di previsione di QnA Maker**: servizio distribuito nella sottoscrizione di Azure nell'area specificata. Il contenuto della knowledge base del cliente viene archiviato in [Ricerca di Azure](https://azure.microsoft.com/services/search/) e l'endpoint viene distribuito come [servizio app](https://azure.microsoft.com/services/app-service/). È anche possibile scegliere facoltativamente di distribuire una risorsa di [Application Insights](https://azure.microsoft.com/services/application-insights/) per l'analisi.

![Architettura](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>Principali caratteristiche del servizio

- Esperienza completa **senza codice** per [creare un bot per domande frequenti](https://aka.ms/qnamaker-docs-create-faqbot).
- **Nessuna limitazione della larghezza di banda della rete per le previsioni**. Si paga per l'hosting del servizio e non per il numero di transazioni. Per altri dettagli, vedere la [pagina dei prezzi](https://aka.ms/qnamaker-docs-pricing).
- **Ridimensionamento in base alle esigenze**. Scegliere gli SKU appropriati dei singoli componenti in base allo scenario. Vedere come [scegliere la capacità](https://aka.ms/qnamaker-docs-capacity) per il servizio QnA Maker.
- **Conformità dei dati completa**. I componenti del servizio di previsione vengono distribuiti nella sottoscrizione di Azure ed entro i limiti di conformità.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un servizio QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
