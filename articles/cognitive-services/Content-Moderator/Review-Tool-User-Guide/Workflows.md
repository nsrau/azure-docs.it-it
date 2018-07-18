---
title: Definire e usare i flussi di lavoro in Azure Content Moderator | Microsoft Docs
description: Informazioni su come creare flussi di lavoro personalizzati in base ai criteri del contenuto.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/07/2018
ms.author: sajagtap
ms.openlocfilehash: dfe3ba8a2ef1bcbc69ef585b504a9367d9420bf0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373025"
---
# <a name="define-test-and-use-workflows"></a>Definire, testare e usare i flussi di lavoro

È possibile usare la progettazione flussi di lavoro e le API di Azure Content Moderator per definire soglie e flussi di lavoro personalizzati in base ai criteri del contenuto.

I flussi di lavoro "si connettono" all'API Content Moderator usando i connettori. Se sono disponibili connettori per altre API, è possibile usarle. Questo esempio usa il connettore Content Moderator incluso per impostazione predefinita.

## <a name="browse-to-the-workflows-section"></a>Passare alla sezione Workflows (Flussi di lavoro)

Nella scheda **Settings** (Impostazioni) selezionare **Workflows** (Flussi di lavoro).

  ![Impostazione Workflows (Flussi di lavoro)](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Avviare un nuovo flusso di lavoro

Selezionare **Add Workflow** (Aggiungi flusso di lavoro).

  ![Aggiungere un flusso di lavoro](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Assegnare un nome e una descrizione

Assegnare un nome al flusso di lavoro, immettere una descrizione e scegliere se il flusso di lavoro gestisce le immagini o il testo.

  ![Nome e descrizione del flusso di lavoro](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Definire i criteri di valutazione ("condizione")

Nello screenshot seguente vengono visualizzati i campi e le selezioni If-Then-Else che è necessario definire per i flussi di lavoro. Scegliere un connettore. Questo esempio usa **Content Moderator**. A seconda del connettore scelto, cambiano le opzioni disponibili per l'output.

  ![Definire la condizione del flusso di lavoro](images/ocr-workflow-step-2-condition.PNG)

Dopo aver scelto il connettore e l'output desiderati, selezionare un operatore e il valore per la condizione.

## <a name="define-the-action-to-take"></a>Definire l'azione da intraprendere

Selezionare l'azione da eseguire e la condizione da soddisfare. L'esempio seguente crea la revisione di un'immagine, assegna un tag `a` e lo evidenzia per la condizione visualizzata. È anche possibile combinare più condizioni per ottenere i risultati desiderati. Aggiungere facoltativamente un percorso alternativo (Else).

  ![Definire l'azione del flusso di lavoro](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>Salvare il flusso di lavoro

Salvare infine il flusso di lavoro e prendere nota del nome del flusso di lavoro. Il nome è necessario per avviare un processo di moderazione usando l'API di revisione.

## <a name="test-the-workflow"></a>Testare il flusso di lavoro

Ora che è stato definito un flusso di lavoro personalizzato, testarlo con il contenuto di esempio.

Selezionare il pulsante **Execute Workflow** (Esegui flusso di lavoro) corrispondente.

  ![Test del flusso di lavoro](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Caricare un file

Salvare l'[immagine di esempio](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) nell'unità locale. Per testare il flusso di lavoro, selezionare **Choose File(s)** (Scegli file) e caricare l'immagine.

  ![Caricare un file di immagine](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>Tenere traccia del flusso di lavoro

Tenere traccia del flusso di lavoro durante l'esecuzione.

  ![Tenere traccia dell'esecuzione del flusso di lavoro](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Esaminare le immagini contrassegnate in modalità manuale

Per visualizzare la revisione dell'immagine, andare alla scheda **Image** (Immagine) in **Review** (Revisione).

  ![Verificare le immagini](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>Passaggi successivi 

Per richiamare il flusso di lavoro dal codice, usare i flussi di lavoro personalizzati con la [guida introduttiva alla console per l'API `Job`](../try-review-api-job.md) e la [guida introduttiva a .NET SDK](../moderation-jobs-quickstart-dotnet.md).
