---
title: Definire e usare i flussi di lavoro del contenuto tramite lo strumento di revisione - Content Moderator
titlesuffix: Azure Cognitive Services
description: È possibile utilizzare la finestra di progettazione del flusso di lavoro di Azure Content Moderator per definire i flussi di lavoro personalizzati e le soglie in base a criteri di contenuto.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263078"
---
# <a name="define-and-use-moderation-workflows"></a>Definire e usare i flussi di lavoro di moderazione

In questa guida si apprenderà come configurare e usare [flussi di lavoro](../review-api.md#workflows) nel [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) sito Web. I flussi di lavoro sono filtri personalizzati basati sul cloud che è possibile usare per gestire il contenuto in modo più efficiente. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. Questa guida illustra come usare il connettore di Content Moderator (che è incluso per impostazione predefinita) per filtrare il contenuto e configurazione di revisione umana in uno scenario tipico di moderazione.

## <a name="create-a-new-workflow"></a>Creare un nuovo flusso di lavoro

Andare alla [strumento di revisione di Content Moderator](https://contentmoderator.cognitive.microsoft.com/) ed eseguire l'accesso. Nella scheda **Settings** (Impostazioni) selezionare **Workflows** (Flussi di lavoro).

![Impostazione Workflows (Flussi di lavoro)](images/2-workflows-0.png)

Nella schermata successiva, selezionare **Aggiungi flusso di lavoro**.

![Aggiungere un flusso di lavoro](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Assegnare un nome e una descrizione

Denominare il flusso di lavoro, immettere una descrizione e scegliere se il flusso di lavoro gestirà immagini o testo.

![Nome e descrizione del flusso di lavoro](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definire i criteri di valutazione

Nella schermata successiva, passare al **se** sezione. Nel menu a discesa superiore, scegliere **condizione**. In questo modo sarà possibile configurare la condizione in cui il flusso di lavoro eseguirà l'azione. Se si desidera utilizzare più condizioni, scegliere **combinazione** invece. 

Successivamente, selezionare un connettore. Questo esempio usa **Content Moderator**. A seconda del connettore che si sceglie, si otterranno diverse opzioni per l'output dei dati. Vedere le [connettori](./configure.md#connectors) sezione della Guida impostazioni dello strumento di revisione per imparare a configurare altri connettori.

![Selezionare il connettore di flusso di lavoro](images/image-workflow-connect-to.PNG)

Scegliere l'output desiderato da utilizzare e impostare le condizioni per verificare a fronte.

![Definire la condizione del flusso di lavoro](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definire l'azione

Andare alla **quindi** sezione, in cui si seleziona un'azione. Nell'esempio seguente crea una revisione dell'immagine e assegna un tag. Facoltativamente, è possibile aggiungere un percorso alternativo (Else) e impostare un'azione per cui anche.

![Definire l'azione del flusso di lavoro](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Salvare il flusso di lavoro

Si noti il nome del flusso di lavoro. è necessario il nome per avviare un processo di moderazione con l'API di flusso di lavoro (vedere sotto). Infine, salvare il flusso di lavoro utilizzando il **salvare** nella parte superiore della pagina.

## <a name="test-the-workflow"></a>Testare il flusso di lavoro

Ora che sono stati definiti un flusso di lavoro personalizzato, testarlo con contenuto di esempio. Passare a **flussi di lavoro** e selezionare i valori corrispondenti **flusso di lavoro eseguire** pulsante.

![Test del flusso di lavoro](images/image-workflow-execute.PNG)

Salvare questo [immagine di esempio](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) nell'unità locale. Quindi selezionare **file scegliere** e caricare l'immagine al flusso di lavoro.

![Uno strumento di esecuzione con un'offerta sovrapposta sull'immagine](images/sample-text.jpg)

### <a name="track-progress"></a>Monitorare l'avanzamento

È possibile visualizzare lo stato di avanzamento del flusso di lavoro nella finestra popup successiva.

![Tenere traccia dell'esecuzione del flusso di lavoro](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Verifica del flusso di lavoro azione

Andare alla **immagine** disponibile nella scheda **rivedere** e verificare che sia presente una revisione dell'immagine appena creata.

![Verificare le immagini](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida, si è appreso come configurare e usare i flussi di lavoro di moderazione di Content Moderator [strumento di revisione](https://contentmoderator.cognitive.microsoft.com). Successivamente, vedere la [Guida all'API REST](../try-review-api-workflow.md) per imparare a creare flussi di lavoro a livello di codice.
