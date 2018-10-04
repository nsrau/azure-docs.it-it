---
title: Introduzione a Content Moderator
titlesuffix: Azure Cognitive Services
description: Come iniziare a usare Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: c2ac0ccd89b5f1436a151e3d69c5d7423090f244
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225295"
---
# <a name="get-started-with-content-moderator"></a>Introduzione a Content Moderator

Per iniziare a usare Content Moderator:

- [Iniziare con lo strumento di revisione](#start-with-the-review-tool) per ottenere la chiave API e creare un team di revisione. Il vantaggio è che è possibile usare la chiave API per chiamare le API di moderazione per analizzare il contenuto e le API di revisione per generare revisioni, senza ulteriori passaggi.
- [Iscriversi a Content Moderator](#start-with-the-apis) in Azure per ottenere la chiave API. Consultare il [Riferimento API](api-reference.md) e gli [SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Occorre ancora effettuare l'iscrizione online per poter creare un team di revisione.
- [Usare il connettore e i modelli di Flow](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) per estrarre un'ampia gamma di integrazioni con una finestra di progettazione facile da usare.

Indipendentemente dall'opzione scelta, vedere l'articolo sulla [gestione delle credenziali](review-tool-user-guide/credentials.md) per trovare le credenziali dell'API.

## <a name="start-with-the-review-tool"></a>Iniziare a usare lo strumento di revisione
[Registrarsi](http://contentmoderator.cognitive.microsoft.com/) sul sito Web dello strumento di revisione di Content Moderator.

![Home page di Content Moderator](images/homepage.PNG)

### <a name="create-a-review-team"></a>Creare un team di revisione
Assegnare un nome al team. Se si intende invitare i colleghi è possibile farlo immettendo i relativi indirizzi e-mail.

![Invitare un membro del team](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Caricare immagini o immettere del testo
Fare clic su **Prova > Immagine** oppure **Prova > Testo**. Caricare fino a cinque immagini di esempio o immettere il testo di esempio da moderare.

![Try Image (Prova immagine) o Moderazione testo](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Inviare alla moderazione automatica
Inviare il contenuto alla moderazione automatica. Lo strumento di revisione chiama internamente le API di moderazione per analizzare il contenuto. Una volta completata l'analisi, viene visualizzato un messaggio che riporta i risultati in attesa di revisione.

![Moderare i file](images/submitted.png)

### <a name="review-and-confirm-results"></a>Rivedere e confermare i risultati
Rivedere i tag moderati automaticamente, se necessario modificarli e inviarli selezionando il pulsante **Avanti**. Mentre l'applicazione aziendale chiama le API di Content Moderator, il contenuto taggato inizia ad accodarsi, pronto per essere rivisto dai team di revisione umana. Esaminare rapidamente grandi volumi di contenuti usando questo approccio.

![Esaminare i risultati](images/reviewresults.png)

Imparare a usare tutte le [funzionalità dello strumento di revisione](Review-Tool-User-Guide/human-in-the-loop.md) o passare alla sezione successiva per altre informazioni sulle API. Saltare il passaggio di registrazione perché si dispone della chiave API fornita nello strumento di revisione, come mostrato nell'articolo [Manage credentials](review-tool-user-guide/credentials.md) (Gestire le credenziali).

### <a name="use-the-apis"></a>Usare le API

Imparare a integrare Content Moderator alle applicazioni aziendali. Consultare il [Riferimento API](api-reference.md) e gli [SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net).

## <a name="subscribe-in-the-azure-portal"></a>Iscriversi nel portale di Azure

[Iscriversi a Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) nel portale di Azure. Iniziare a usare una delle API seguenti:

### <a name="image-moderation"></a>Moderazione immagini

Iniziare con la [console API](try-image-api.md) oppure usare l'[Avvio rapido di .NET](image-moderation-quickstart-dotnet.md) per analizzare le immagini e rilevare potenziali contenuti spinti e per adulti usando tag, punteggi di attendibilità e altre informazioni estratte.

### <a name="text-moderation"></a>Moderazione testo

Iniziare con la [console API](try-text-api.md) oppure usare l'[Avvio rapido di .NET](text-moderation-quickstart-dotnet.md) per analizzare il contenuto di testo per rilevare potenziali contenuti volgari, una classificazione del testo indesiderato automatica (anteprima) e informazioni personali. 


### <a name="video-moderation"></a>Moderazione video

Iniziare con l'[Avvio rapido di .NET](video-moderation-api.md) per analizzare i video e rilevare potenziali contenuti spinti e per adulti. 


### <a name="review-apis"></a>Verificare le API

Iniziare da qui, scegliendo tra le API Job (Processo), Review (Revisione) e Workflow (Flusso di lavoro).

- L'[API Job](try-review-api-job.md) (Processo) esegue l'analisi dei contenuti usando le API di moderazione e genera revisioni nello strumento di revisione. 
- L'[API Review](try-review-api-review.md) (Revisione) crea direttamente revisioni di immagini, testi o video per i moderatori umani senza eseguire prima l'analisi del contenuto. 
- L'[API Workflow](try-review-api-workflow.md) (Flusso di lavoro) crea, aggiorna e ottiene i dettagli sui flussi di lavoro personalizzati creati dal team.

## <a name="next-steps"></a>Passaggi successivi

Consultare il [Riferimento API](api-reference.md) e gli [SDK](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Iniziare subito a usare l'integrazione con gli [esempi di SDK per .NET](sdk-and-samples.md#net-sdk-samples), gli [esempi di API REST con C#](https://github.com/sanjeev3/azure-docs-pr/blob/master/articles/cognitive-services/Content-Moderator/sdk-and-samples.md#rest-api-samples-in-c) e le [esercitazioni](sdk-and-samples.md#tutorials).
