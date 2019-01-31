---
title: Flusso di lavoro di moderazione - Content Moderator
titlesuffix: Azure Cognitive Services
description: I flussi di lavoro vengono usati con le operazioni Job dell'API di revisione per automatizzare la creazione della revisione human-in-the-loop in base alle soglie e ai criteri del contenuto.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 4abe3a864b798552caa27d07a422a7d67b2e4018
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221386"
---
# <a name="automate-moderation-reviews-with-workflows"></a>Automatizzare le verifiche di moderazione dei flussi di lavoro

Content Moderator include strumenti e API per gestire i flussi di lavoro. I flussi di lavoro vengono usati con le [operazioni Job dell'API di revisione](review-api.md) per automatizzare la creazione della revisione human-in-the-loop in base alle soglie e ai criteri del contenuto.

L'API di revisione consente di includere la supervisione umana nel processo di moderazione del contenuto nei modi seguenti:

1. Le operazioni **Job** vengono usate per avviare la creazione di revisione umana e moderazione automatica in un unico passaggio.
1. Le operazioni **Review** vengono usate per la creazione della revisione umana, al di fuori del passaggio di moderazione.
1. Le operazioni **Workflow** vengono usate per gestire i flussi di lavoro che automatizzano l'analisi con soglie per la creazione della revisione.

Questo articolo illustra le operazioni **Workflow**. Vedere la panoramica [Processi e verifiche](review-api.md) per informazioni su revisioni e processi di moderazione del contenuto.

Esaminare il flusso di lavoro **predefinito** è il modo migliore per acquisire familiarità con i flussi di lavoro in Content Moderator.

## <a name="your-first-workflow"></a>Primo flusso di lavoro

Il primo flusso di lavoro include il [team dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/). Se non è già stato fatto, iscriversi.

Passare alla schermata [Workflows (Flussi di lavoro) dello strumento di revisione](Review-Tool-User-Guide/Workflows.md) nella scheda Settings (Impostazioni). Viene visualizzato un flusso di lavoro **predefinito**, come illustrato nell'immagine seguente:

![Flussi di lavoro di Content Moderator](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Aprire il flusso di lavoro predefinito

Usare l'opzione **edit** (modifica) per aprire la pagina di modifica del flusso di lavoro, come illustrato nell'immagine seguente: ![Flusso di lavoro predefinito di Content Moderator](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>Visualizzazione di progettazione

Viene visualizzata la scheda **Designer** (Finestra di progettazione) per il flusso di lavoro. La visualizzazione di progettazione mostra i passaggi seguenti:

1. **Condizione** per il flusso di lavoro da valutare. In questo caso il flusso di lavoro chiama l'API per le immagini di Content Moderator e controlla se l'output `isAdult` è uguale a `true`.
1. **Azione** da eseguire se la condizione è soddisfatta. In questo caso il flusso di lavoro crea una revisione nello strumento di revisione se l'output `isAdult` è `true`.

![Finestra di progettazione del flusso di lavoro predefinito di Content Moderator](images/default-workflow-designer.png)

### <a name="the-json-view"></a>Visualizzazione JSON

Selezionare la scheda **JSON** per visualizzare la definizione JSON del flusso di lavoro.

    {
        "Type": "Logic",
        If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="key-learning"></a>Informazione importante

I flussi di lavoro in Content Moderator sono facili da configurare e flessibili. Se la finestra di progettazione predefinita non soddisfa i requisiti, scrivere la definizione del flusso di lavoro nel formato **JSON**. Usare quindi la definizione JSON con l'[API del flusso di lavoro](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) per creare e gestire il flusso di lavoro dall'applicazione.

## <a name="define-a-custom-workflow"></a>Definire un flusso di lavoro personalizzato

Le funzionalità del flusso di lavoro di Content Moderator consentono di definire e usare i flussi di lavoro personalizzati. Usare la [procedura sui flussi di lavoro dello strumento di revisione](Review-Tool-User-Guide/Workflows.md) per definire un flusso di lavoro personalizzato. Questo flusso di lavoro usa la funzionalità OCR di Content Moderator per estrarre il testo da un'immagine di esempio, quindi crea una revisione nello strumento di revisione.

### <a name="the-sample-image"></a>Immagine di esempio

Salvare l'[immagine di esempio](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) nell'unità locale. Questa immagine è necessaria per l'esercizio.

### <a name="the-designer-view"></a>Visualizzazione di progettazione

Selezionare la scheda **Designer** (Finestra di progettazione) e l'[esercitazione sulla creazione di un flusso di lavoro](Review-Tool-User-Guide/Workflows.md) per definire un flusso di lavoro personalizzato. L'immagine seguente illustra la visualizzazione **Condition** (Condizione) della finestra di progettazione. Per gli altri passaggi, vedere l'esercitazione.

![Content Moderator - Condizione del flusso di lavoro](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>Visualizzazione JSON

Selezionare la scheda **JSON** per visualizzare la definizione JSON seguente del flusso di lavoro personalizzato. Si noti che le istruzioni **If-Then** nella definizione JSON corrispondono ai passaggi definiti con la visualizzazione di progettazione.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>Risultato del flusso di lavoro

Dopo avere testato il flusso di lavoro dalla schermata dei flussi di lavoro, viene creata la revisione seguente. Passare alla scheda **Image** (Immagine) in **Review** (Revisione) per visualizzare la revisione.
Il flusso di lavoro ha creato la revisione perché il test della condizione primaria è risultato positivo alla presenza di testo. La revisione ha anche evidenziato il tag **`a`** nella revisione dell'immagine.

![Content Moderator - output del flusso di lavoro semplice](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Flusso di lavoro avanzato con combinazione

### <a name="the-sample-image"></a>Immagine di esempio

Usare la stessa [immagine di esempio](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) usata nella sezione precedente.

Questa volta sostituire la condizione primaria con una combinazione di due controlli. Oltre a controllare il testo, verificare anche la presenza di contenuto volgare nel testo. Il flusso di lavoro crea una revisione se trova il testo **e** vi rileva contenuti volgari.

### <a name="the-designer-view"></a>Visualizzazione di progettazione

Per sostituire la **condizione** con una **combinazione**, modificare il flusso di lavoro. L'immagine seguente mostra la nuova visualizzazione visibile nella finestra di progettazione.

![Content Moderator - Condizione del flusso di lavoro modificata](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>Visualizzazione JSON

Selezionare la scheda **JSON** per visualizzare la definizione JSON seguente del flusso di lavoro personalizzato modificato. Si noti che le istruzioni **If-Then** nella definizione JSON corrispondono ai nuovi passaggi aggiunti al flusso di lavoro.

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>Risultato del flusso di lavoro

Dopo avere testato nuovamente il flusso di lavoro, non vengono trovate revisioni. Per confermare l'assenza di revisioni, passare alla scheda **Image** (Immagine) in **Review** (Revisione).
Il flusso di lavoro non ha creato la revisione perché non è riuscito a rilevare contenuto volgare nel testo estratto.

![Content Moderator - output del flusso di lavoro modificato](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>API del flusso di lavoro

Le [operazioni Workflow](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) forniscono l'interfaccia di programmazione alle funzionalità del flusso di lavoro. L'API del flusso di lavoro consente di creare flussi di lavoro, ottenere i dettagli dei flussi di lavoro e aggiornare le definizioni dei flussi di lavoro.

### <a name="get-all-workflow-details"></a>Ottenere [tutti] i dettagli del flusso di lavoro

L'operazione **Workflow-Get** (Flusso di lavoro - Recupero) accetta gli input seguenti:

- **team**: L'ID team creato una volta configurato l'[account dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Il nome del flusso di lavoro. Usare `default` per iniziare.
- **Ocp-Apim-Subscription-Key**: Disponibile nella scheda **Impostazioni**. Per altre informazioni, vedere la [panoramica](overview.md).

Se l'operazione viene completata correttamente, lo **stato della risposta** è `200 OK` e la casella **Response content** (Contenuto della risposta) visualizza la definizione del flusso di lavoro nel formato JSON.
Per altre informazioni, vedere la [guida introduttiva alla console dell'API del flusso di lavoro](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Creare o aggiornare il flusso di lavoro

L'operazione di creazione e aggiornamento consente di creare il flusso di lavoro dall'API.

L'operazione **Workflow-Create or Update** (Flusso di lavoro - Creazione o aggiornamento) accetta gli input seguenti:

- **team**: L'ID team creato una volta configurato l'[account dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Il nome del flusso di lavoro. Usare `default` per iniziare.
- **Ocp-Apim-Subscription-Key**: Disponibile nella scheda **Impostazioni**. Per altre informazioni, vedere la [panoramica](overview.md).

Se l'operazione viene completata correttamente, lo **stato della risposta** è `200 OK` e la casella **Response content** (Contenuto della risposta) visualizza `true`. Per altre informazioni, [eseguire il test drive dell'operazione `Create`](try-review-api-job.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare flussi di lavoro personalizzati, vedere l'[esercitazione sui flussi di lavoro dello strumento di revisione](Review-Tool-User-Guide/Workflows.md). 

Eseguire il test drive della [console per l'API del flusso di lavoro](try-review-api-job.md) e usare gli esempi di codice dell'API REST. 

Usare infine i flussi di lavoro personalizzati con le operazioni **Job**, come illustrato nella [console dell'API Job](try-review-api-job.md) e nella [guida introduttiva a .NET per i processi](moderation-jobs-quickstart-dotnet.md).
