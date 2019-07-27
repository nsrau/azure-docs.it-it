---
title: Definire e usare i flussi di lavoro di contenuto tramite lo strumento di revisione-Content Moderator
titleSuffix: Azure Cognitive Services
description: È possibile usare la finestra di progettazione dei flussi di lavoro di Azure Content Moderator per definire i flussi di lavoro e le soglie personalizzati in base ai criteri di contenuto.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 9b87529014a0eeb5561cd166a29f2309198733b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565640"
---
# <a name="define-and-use-moderation-workflows"></a>Definire e usare flussi di lavoro di moderazione

In questa guida si apprenderà come configurare e usare i [flussi di lavoro](../review-api.md#workflows) nel sito Web [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com) . I flussi di lavoro sono filtri personalizzati basati sul cloud che è possibile usare per gestire il contenuto in modo più efficiente. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. Questa guida illustra come usare il connettore Content Moderator (incluso per impostazione predefinita) per filtrare il contenuto e configurare le recensioni umane in uno scenario di moderazione tipico.

## <a name="create-a-new-workflow"></a>Creazione di un nuovo flusso di lavoro

Passare allo [strumento di verifica content moderator](https://contentmoderator.cognitive.microsoft.com/) ed eseguire l'accesso. Nella scheda **Settings** (Impostazioni) selezionare **Workflows** (Flussi di lavoro).

![Impostazione Workflows (Flussi di lavoro)](images/2-workflows-0.png)

Nella schermata successiva selezionare **Aggiungi flusso di lavoro**.

![Aggiungere un flusso di lavoro](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Assegnare un nome e una descrizione

Assegnare un nome al flusso di lavoro, immettere una descrizione e scegliere se il flusso di lavoro gestirà immagini o testo.

![Nome e descrizione del flusso di lavoro](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definire i criteri di valutazione

Nella schermata successiva passare alla sezione **if** . Nel menu a discesa in alto scegliere **condizione**. In questo modo sarà possibile configurare la condizione in cui il flusso di lavoro eseguirà l'azione. Se si desidera utilizzare più condizioni, scegliere invece **combinazione** . 

Selezionare quindi un connettore. Questo esempio usa **Content Moderator**. A seconda del connettore scelto, si otterranno opzioni diverse per l'output dei dati. Vedere la sezione [Connectors](./configure.md#connectors) della Guida alle impostazioni dello strumento di revisione per informazioni su come configurare altri connettori.

![Seleziona connettore flusso di lavoro](images/image-workflow-connect-to.PNG)

Scegliere l'output desiderato da usare e impostare le condizioni per la verifica.

![Definire la condizione del flusso di lavoro](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definire l'azione

Passare alla sezione **then** , in cui è possibile selezionare un'azione. Nell'esempio seguente viene creata una revisione dell'immagine e viene assegnato un tag. Facoltativamente, è possibile aggiungere un percorso alternativo (Else) e impostare un'azione anche per tale percorso.

![Definire l'azione del flusso di lavoro](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Salvare il flusso di lavoro

Annotare il nome del flusso di lavoro; è necessario il nome per avviare un processo di moderazione con l'API del flusso di lavoro (vedere di seguito). Infine, salvare il flusso di lavoro utilizzando il pulsante **Salva** nella parte superiore della pagina.

## <a name="test-the-workflow"></a>Testare il flusso di lavoro

Ora che è stato definito un flusso di lavoro personalizzato, testarlo con il contenuto di esempio. Passare a **flussi di lavoro** e selezionare il pulsante **Esegui flusso di lavoro** corrispondente.

![Test del flusso di lavoro](images/image-workflow-execute.PNG)

Salvare l' [immagine di esempio](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) nell'unità locale. Selezionare quindi **scegliere i file** e caricare l'immagine nel flusso di lavoro.

![Runner con virgolette sovrapposte sull'immagine](images/sample-text.jpg)

### <a name="track-progress"></a>Monitorare l'avanzamento

È possibile visualizzare lo stato di avanzamento del flusso di lavoro nella finestra popup successiva.

![Tenere traccia dell'esecuzione del flusso di lavoro](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Verifica azione flusso di lavoro

Passare alla scheda **immagine** in **Verifica** e verificare che sia presente una nuova immagine creata.

![Verificare le immagini](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come configurare e usare i flussi di lavoro di moderazione dallo [strumento content moderator Review](https://contentmoderator.cognitive.microsoft.com). Vedere quindi la [Guida dell'API REST](../try-review-api-workflow.md) per informazioni su come creare flussi di lavoro a livello di codice.
