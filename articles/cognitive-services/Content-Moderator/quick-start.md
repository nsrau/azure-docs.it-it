---
title: 'Guida introduttiva: Introduzione a Content Moderator'
titlesuffix: Azure Cognitive Services
description: Come iniziare a usare Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: sajagtap
ms.openlocfilehash: f25434814a7fb3d0f49cab539b394970c9bcfb3b
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023441"
---
# <a name="quickstart-get-familiar-with-content-moderator"></a>Guida introduttiva: Acquisire familiarità con Content Moderator

In questa guida introduttiva verrà usato lo strumento di revisione di Content Moderator online per testare le funzionalità di base di Content Moderator senza dover scrivere codice. Se si vuole integrare questo servizio nell'app più rapidamente, vedere le altre guide introduttive elencate nella sezione [Passaggi successivi](#next-steps).

## <a name="prerequisites"></a>Prerequisiti

- Un Web browser

## <a name="set-up-the-review-tool"></a>Configurare lo strumento di revisione
Lo strumento di revisione di Content Moderator è uno strumento basato sul Web che consente a revisori umani di supportare i servizi cognitivi nel processo decisionale. In questa Guida esamineremo il breve processo di configurazione dello strumento di revisione in modo da vedere come funziona il servizio Content Moderator. Passare al sito dello [strumento di revisione di Content Moderator](http://contentmoderator.cognitive.microsoft.com/) e registrarsi.

![Home page di Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Creare un team di revisione

A questo punto, creare un team di revisione. In uno scenario operativo si tratta del gruppo di persone che esaminerà manualmente le decisioni di moderazione del servizio. Per il momento è sufficiente creare un nome di team. Se si intende invitare i colleghi a partecipare al team, immettere in questa finestra i relativi indirizzi di posta elettronica.

![Invitare un membro del team](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Caricare contenuto di esempio

A questo punto, è possibile caricare contenuto di esempio. Selezionare **Try > Image** (Prova > Immagine), **Try > Text** (Prova > Testo ) o **Try > Video** (Prova > Video).

![Scelta di Try Image (Prova immagine) per la moderazione testo](images/tryimagesortext.png)

Inviare il contenuto per la moderazione. Lo strumento di revisione chiamerà internamente le API di moderazione per analizzare il contenuto. Al termine dell'analisi viene visualizzato un messaggio in cui si informa che sono disponibili risultati da rivedere.

![Moderare i file](images/submitted.png)

## <a name="review-moderation-tags"></a>Esaminare i tag di moderazione

Esaminare i tag di moderazione applicati. È possibile visualizzare i tag che sono stati applicati al contenuto e il punteggio assegnato in ogni categoria.

![Esaminare i risultati](images/reviewresults_text.png)

In un progetto l'utente o il team di revisione può modificare questi tag o aggiungere altri tag in base alle esigenze. Per inviare le modifiche, fare clic sul pulsante **Next** (Avanti). Mentre l'applicazione aziendale chiama le API di Content Moderator, il contenuto taggato verrà accodato qui, pronto per essere rivisto dai team di revisione umana. Con questo approccio è possibile esaminare rapidamente volumi elevati di contenuto.

Si è quindi usato lo strumento di revisione di Content Moderator per vedere esempi di operazioni che è possibile eseguire con il servizio Content Moderator. A questo punto è possibile approfondire la conoscenza dello strumento di revisione e imparare a integrarlo in un progetto software usando le API di revisione oppure passare alla sezione [Passaggi successivi](#next-steps) per ottenere informazioni su come usare le API di moderazione nell'app.

## <a name="learn-more-about-the-review-tool"></a>Altre informazioni sullo strumento di revisione

Per altre informazioni sull'uso dello strumento di revisione di Content Moderator, consultare la guida [Human-in-the-Loop](Review-Tool-User-Guide/human-in-the-loop.md) e vedere le API dello strumento di revisione per sapere come ottimizzare l'esperienza di revisione umana:
- L'[API Job](try-review-api-job.md) (Processo) esegue l'analisi dei contenuti usando le API di moderazione e genera revisioni nello strumento di revisione. 
- L'[API Review](try-review-api-review.md) (Revisione) crea direttamente revisioni di immagini, testi o video per i moderatori umani senza eseguire prima l'analisi del contenuto. 
- L'[API Workflow](try-review-api-workflow.md) (Flusso di lavoro) crea, aggiorna e ottiene i dettagli sui flussi di lavoro personalizzati creati dal team.

In alternativa, continuare con i passaggi successivi per iniziare a usare le API di moderazione nel codice.

## <a name="next-steps"></a>Passaggi successivi

In questa sezione vengono fornite informazioni su come usare le API di moderazione nell'app.
- Implementare la moderazione delle immagini. Usare la [console API](try-image-api.md) oppure usare la [guida introduttiva per C#](image-moderation-quickstart-dotnet.md) per analizzare le immagini e rilevare potenziali contenuti spinti e per adulti usando tag, punteggi di attendibilità e altre informazioni estratte.
- Implementare la moderazione del testo. Usare la [console API](try-text-api.md) oppure usare la [guida introduttiva per C#](text-moderation-quickstart-dotnet.md) per analizzare il contenuto di testo per rilevare potenziali contenuti volgari, una classificazione del testo indesiderato automatica (anteprima) e informazioni personali. 
- Implementare la moderazione video. Usare la [guida introduttiva per C#](video-moderation-api.md) per analizzare i video e rilevare potenziali contenuti spinti e per adulti. 
