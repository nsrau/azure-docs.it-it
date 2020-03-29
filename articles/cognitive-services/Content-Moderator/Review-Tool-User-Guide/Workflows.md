---
title: Definire e utilizzare i flussi di lavoro del contenuto tramite lo strumento di revisione - Content Moderator
titleSuffix: Azure Cognitive Services
description: È possibile usare la finestra di progettazione del flusso di lavoro di Azure Content Moderator per definire flussi di lavoro e soglie personalizzati in base ai criteri del contenuto.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754245"
---
# <a name="define-and-use-moderation-workflows"></a>Definire e utilizzare i flussi di lavoro di moderazione

In questa guida verrà illustrato come impostare e utilizzare [i flussi](../review-api.md#workflows) di lavoro nel sito Web dello [strumento di revisione.](https://contentmoderator.cognitive.microsoft.com) I flussi di lavoro sono filtri personalizzati basati su cloud che è possibile utilizzare per gestire i contenuti in modo più efficiente. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. In questa guida viene illustrato come utilizzare il connettore Content Moderator (incluso per impostazione predefinita) per filtrare il contenuto e impostare revisioni umane in uno scenario di moderazione tipico.

## <a name="create-a-new-workflow"></a>Creare un nuovo flusso di lavoro

Vai allo strumento Di revisione [moderatore dei contenuti](https://contentmoderator.cognitive.microsoft.com/) e accedi. Nella scheda **Settings** (Impostazioni) selezionare **Workflows** (Flussi di lavoro).

![Impostazione Workflows (Flussi di lavoro)](images/2-workflows-0.png)

Nella schermata successiva selezionare **Aggiungi flusso di lavoro**.

![Aggiungere un flusso di lavoro](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Assegnare un nome e una descrizione

Assegnare un nome al flusso di lavoro, immettere una descrizione e scegliere se il flusso di lavoro gestirà immagini o testo.

![Nome e descrizione del flusso di lavoro](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definire i criteri di valutazione

Nella schermata successiva passare alla sezione **If.** Nel menu a discesa in alto scegliere **Condizione**. In questo modo sarà possibile configurare la condizione in base alla quale il flusso di lavoro intraprenderà un'azione. Se si desidera utilizzare più condizioni, scegliere **Combinazione.** 

Selezionare quindi un connettore. Questo esempio usa **Content Moderator**. A seconda del connettore scelto, si otterranno diverse opzioni per l'output dei dati. Vedere la sezione [Connettori](./configure.md#connectors) della guida alle impostazioni dello strumento di revisione per informazioni su come configurare altri connettori.

![Selezionare il connettore del flusso di lavoro](images/image-workflow-connect-to.PNG)

Scegliere l'output desiderato da utilizzare e impostare le condizioni con cui verificarlo.

![Definire la condizione del flusso di lavoro](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definire l'azione

Passare alla sezione **Quindi,** in cui è possibile selezionare un'azione. L'esempio seguente crea una revisione dell'immagine e assegna un tag. Facoltativamente, è possibile aggiungere un percorso alternativo (Else) e impostare un'azione anche per questo.

![Definire l'azione del flusso di lavoro](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Salvare il flusso di lavoro

Prendere nota del nome del flusso di lavoro; è necessario il nome per avviare un processo di moderazione con l'API del flusso di lavoro (vedere di seguito). Infine, salvare il flusso di lavoro utilizzando il pulsante **Salva** nella parte superiore della pagina.

## <a name="test-the-workflow"></a>Testare il flusso di lavoro

Dopo aver definito un flusso di lavoro personalizzato, testarlo con contenuto di esempio. Passare a **Flussi di lavoro** e selezionare il pulsante **Esegui flusso di lavoro** corrispondente.

![Test del flusso di lavoro](images/image-workflow-execute.PNG)

Salvare questa [immagine di esempio nell'unità](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) locale. Selezionare **quindi Scegli file** e caricare l'immagine nel flusso di lavoro.

![Un corridore con una citazione sovrapposta all'immagine](images/sample-text.jpg)

### <a name="track-progress"></a>Monitorare l'avanzamento

È possibile visualizzare lo stato di avanzamento del flusso di lavoro nella finestra popup successiva.

![Tenere traccia dell'esecuzione del flusso di lavoro](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Verificare l'azione del flusso di lavoroVerify workflow

Passare alla scheda **Immagine** in **Revisione** e verificare che sia presente una revisione dell'immagine appena creata.

![Verificare le immagini](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Passaggi successivi

In questa guida è stato illustrato come impostare e utilizzare i flussi di lavoro di moderazione dallo strumento Content Moderator [Review](https://contentmoderator.cognitive.microsoft.com). Vedere quindi la [guida all'API REST](../try-review-api-workflow.md) per informazioni su come creare flussi di lavoro a livello di codice.
