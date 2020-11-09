---
title: 'Avvio rapido: Provare Content Moderator sul Web'
titleSuffix: Azure Cognitive Services
description: Questa guida di avvio rapido mostra come usare lo strumento di revisione di Content Moderator online per testare le funzionalità di base di Content Moderator senza dover scrivere codice.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: content moderator, moderazione del contenuto
ms.openlocfilehash: d1d9315986f7a6c57c1da012b9034e4f1a3730bc
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93143696"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Avvio rapido: Provare Content Moderator sul Web

Questa guida di avvio rapido mostra come usare lo strumento di revisione di Content Moderator online per testare le funzionalità di base di Content Moderator senza dover scrivere codice. Se si vuole integrare questo servizio nell'app di moderazione del contenuto più rapidamente, vedere gli altri argomenti di avvio rapido elencati nella sezione [Passaggi successivi](#next-steps).

## <a name="prerequisites"></a>Prerequisiti

- Un Web browser

## <a name="set-up-the-review-tool"></a>Configurare lo strumento di revisione
Lo strumento di revisione di Content Moderator è uno strumento basato sul Web che consente ai revisori umani di facilitare il servizio cognitivo nel processo decisionale. Questa guida illustra la breve procedura di configurazione dello strumento di revisione per dimostrare come funziona il servizio Content Moderator. Passare al sito dello [strumento di revisione di Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e registrarsi.

![Home page di Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Creare un team di revisione

A questo punto, creare un team di revisione. In uno scenario operativo, questo team sarà costituito dal gruppo di persone che esamina manualmente le decisioni di moderazione del servizio. Per creare un team, è necessario selezionare un'opzione per **Area** e specificare i valori per **Nome team** e **ID team**. Se si intende invitare i colleghi a partecipare al team, immettere in questa finestra i relativi indirizzi di posta elettronica.

> [!NOTE]
> **Nome team** è un nome descrittivo per il team di revisione. Si tratta del nome visualizzato nel portale di Azure. **ID team** è quello usato per identificare il team di revisione a livello di codice.

> [!div class="mx-imgBorder"]
> ![Invitare un membro del team](images/create-team.png)

Se si sceglie di crittografare i dati usando una chiave gestita dal cliente, verrà richiesto di specificare l' **ID risorsa** di Content Moderator nel piano tariffario di E0. La risorsa specificata deve essere univoca per questo team. 

> [!div class="mx-imgBorder"]
> ![Invitare un membro del team con una chiave gestita dal cliente](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>Caricare contenuto di esempio

A questo punto, è possibile caricare contenuto di esempio. Selezionare **Try > Image** (Prova > Immagine), **Try > Text** (Prova > Testo ) o **Try > Video** (Prova > Video).

> [!div class="mx-imgBorder"]
> ![Try Image (Prova immagine) o Moderazione testo](images/tryimagesortext.png)

Inviare il contenuto per la moderazione. È possibile usare il contenuto di testo di esempio seguente:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Lo strumento di revisione chiamerà internamente le API di moderazione per analizzare il contenuto. Al termine dell'analisi viene visualizzato un messaggio in cui si informa che sono disponibili risultati da rivedere.

> [!div class="mx-imgBorder"]
> ![Moderare i file](images/submitted.png)

## <a name="review-moderation-tags"></a>Esaminare i tag di moderazione

Esaminare i tag di moderazione applicati. È possibile visualizzare i tag che sono stati applicati al contenuto e il punteggio assegnato in ogni categoria. Vedere gli articoli sulla moderazione di [immagini](image-moderation-api.md), [testo](text-moderation-api.md) e [video](video-moderation-api.md) per altre informazioni su cosa indicano i diversi tag di contenuto.

<!-- ![Review results](images/reviewresults_text.png) -->

In un progetto l'utente o il team di revisione può modificare questi tag o aggiungere altri tag in base alle esigenze. Per inviare le modifiche, fare clic sul pulsante **Next** (Avanti). Mentre l'applicazione aziendale chiama le API di Content Moderator, il contenuto taggato verrà accodato qui, pronto per essere rivisto dai team di revisione umana. Con questo approccio è possibile esaminare rapidamente volumi elevati di contenuto.

Si è quindi usato lo strumento di revisione di Content Moderator per vedere esempi delle operazioni che può eseguire il servizio. A questo punto è possibile approfondire la conoscenza dello strumento di revisione e imparare a integrarlo in un progetto software usando le API di revisione oppure passare alla sezione [Passaggi successivi](#next-steps) per ottenere informazioni su come usare le API di moderazione nell'app.

## <a name="learn-more-about-the-review-tool"></a>Altre informazioni sullo strumento di revisione

Per altre informazioni sull'uso dello strumento di revisione di Content Moderator, consultare l'articolo introduttivo allo [strumento di revisione](Review-Tool-User-Guide/human-in-the-loop.md) e vedere le API di questo strumento per comprendere come ottimizzare l'esperienza di revisione umana:
- L'[API Job](try-review-api-job.md) (Processo) esegue l'analisi dei contenuti usando le API di moderazione e genera revisioni nello strumento di revisione. 
- L'[API Review](try-review-api-review.md) (Revisione) crea direttamente revisioni di immagini, testi o video per i moderatori umani senza eseguire prima l'analisi del contenuto. 
- L'[API Workflow](try-review-api-workflow.md) (Flusso di lavoro) crea, aggiorna e ottiene i dettagli sui flussi di lavoro personalizzati creati dal team.

In alternativa, continuare con i passaggi successivi per iniziare a usare le API di moderazione nel codice.

## <a name="next-steps"></a>Passaggi successivi

In questa sezione vengono fornite informazioni su come usare le API di moderazione nell'app.
- Implementare la moderazione delle immagini. Usare la [console API](try-image-api.md) oppure seguire l'[avvio rapido per la libreria client](client-libraries.md) per analizzare le immagini e rilevare potenziali contenuti spinti e per adulti usando tag, punteggi di attendibilità e altre informazioni estratte.
- Implementare la moderazione del testo. Usare la [console API](try-text-api.md) oppure usare l'[avvio rapido per la libreria client](client-libraries.md) per analizzare il contenuto di testo per rilevare potenziali contenuti volgari, una classificazione del testo indesiderato automatica (anteprima) e dati personali.
- Implementare la moderazione video. Seguire la [Guida pratica alla moderazione video per C#](video-moderation-api.md) per analizzare i video e rilevare potenziali contenuti spinti e per adulti. 
