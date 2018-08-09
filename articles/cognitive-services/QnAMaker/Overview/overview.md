---
title: Informazioni su QnA Maker - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: QnA Maker consente di sviluppare un servizio di domande e risposte a partire dal contenuto semistrutturato, ad esempio documenti con domande frequenti oppure URL e manuali di prodotti.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: overview
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: f34cec047c18a6db10b5adda82800c51d44c1155
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "36295146"
---
# <a name="what-is-qna-maker"></a>Informazioni su QnA Maker

[QnA Maker](https://qnamaker.ai) consente di sviluppare un servizio di domande e risposte a partire dal contenuto semistrutturato, ad esempio documenti con domande frequenti oppure URL e manuali di prodotti. È possibile creare un modello di domande e risposte adattabile alle query degli utenti, fornendo risposte per il training di un bot che consenta un'interazione naturale e discorsiva.

Un'interfaccia utente grafica facile da usare consente di creare e gestire il servizio, eseguirne il training e renderlo operativo anche senza avere esperienza di sviluppo.

![Panoramica](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>In evidenza

- Esperienza completa **senza codice** per [creare un bot per domande frequenti](https://aka.ms/qnamaker-docs-create-faqbot).
- **Nessuna limitazione della larghezza di banda della rete**. Si paga per l'hosting del servizio e non per il numero di transazioni. Per altri dettagli, vedere la [pagina dei prezzi](https://aka.ms/qnamaker-docs-pricing).
- **Scalabilità in base alle esigenze**. Scegliere gli SKU appropriati dei singoli componenti in base allo scenario. Vedere come [scegliere la capacità](https://aka.ms/qnamaker-docs-capacity) per il servizio QnA Maker.
- **Conformità dei dati completa**. I dati e i componenti di runtime vengono distribuiti nella sottoscrizione di Azure dell'utente ed entro i limiti di conformità. Vedere di seguito per altri dettagli.

## <a name="key-qna-maker-processes"></a>Processi chiave di QnA Maker

QnA Maker offre due servizi chiave per i dati:

* **Estrazione**: i dati di tipo domanda-risposta strutturati vengono estratti da origini dati semistrutturati, ad esempio domande frequenti e manuali di prodotti. Questa estrazione viene eseguita durante la creazione della Knowledge Base. Creare la Knowledge Base [qui](https://aka.ms/qnamaker-docs-createkb).

* **Corrispondenza**: dopo avere [eseguito il training e il test](https://aka.ms/qnamaker-docs-trainkb) della Knowledge Base, la si [pubblica](https://aka.ms/qnamaker-docs-publishkb). Questa operazione abilita un endpoint alla Knowledge Base di QnA Maker, che è quindi possibile usare nel bot o nell'app. Questo endpoint accetta una domanda di un utente e risponde usando la corrispondenza domanda/risposta più elevata della Knowledge Base, con un punteggio di attendibilità per la corrispondenza.

## <a name="qna-maker-architecture"></a>Architettura di QnA Maker

Lo stack di QnA Maker è costituito dalle parti seguenti:

1. **Servizi di gestione di QnA Maker (piano di controllo)**: esperienza di gestione per una Knowledge Base di QnA Maker, che include la creazione iniziale, l'aggiornamento, il training e la pubblicazione. Queste attività possono essere eseguite tramite il [portale](https://qnamaker.ai) o le [API di gestione](https://aka.ms/qnamaker-v4-apis). I servizi di gestione comunicano con il componente di runtime seguente.

2. **Runtime di QnA Maker (piano dati)**: i dati e il runtime vengono distribuiti nella sottoscrizione di Azure dell'utente, nell'area scelta. Il contenuto delle domande/risposte del cliente viene archiviato in [Ricerca di Azure](https://azure.microsoft.com/services/search/) e il runtime viene distribuito come [servizio app](https://azure.microsoft.com/services/app-service/). È anche possibile scegliere facoltativamente di distribuire una risorsa di [Application Insights](https://azure.microsoft.com/services/application-insights/) per l'analisi.

![Architettura](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un servizio QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
