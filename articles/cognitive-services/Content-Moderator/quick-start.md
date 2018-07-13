---
title: Introduzione a Content Moderator di Azure | Microsoft Docs
description: Introduzione a Content Moderator di Azure.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374316"
---
# <a name="get-started-with-content-moderator"></a>Introduzione a Content Moderator

Iniziare con le API di Content Moderator e lo strumento di revisione nei modi seguenti:

- [Iniziare con lo strumento di revisione](#start-with-the-review-tool) per creare le chiavi API e un team di revisione. Esplorare lo strumento di revisione e scoprire come integrarlo usando le API di Content Moderator.
- [Iscriversi a Content Moderator](#start-with-the-apis) nel portale di Azure. Occorre ancora effettuare l'iscrizione online per poter creare un team di revisione.
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

Dopo aver esplorato la moderazione del contenuto e lo strumento di revisione, scoprire come integrare Content Moderator nelle applicazioni aziendali. Usare la sezione seguente per saperne di più e accelerare la comprensione con gli SDK e i campioni.

## <a name="start-with-the-apis"></a>Iniziare a usare le API

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

Altre informazioni sull'avvio della moderazione del contenuto con l'[API Moderazione immagini](image-moderation-api.md).
