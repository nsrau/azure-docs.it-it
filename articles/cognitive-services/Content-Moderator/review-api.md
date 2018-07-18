---
title: Azure Content Moderator - Processi di moderazione e revisioni human-in-the-loop | Microsoft Docs
description: Applicare la supervisione umana alla moderazione automatica per risultati ottimali.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/21/2018
ms.author: sajagtap
ms.openlocfilehash: 35b3cdaa410712c3fd08d3df4ebe4c83e3955d50
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373252"
---
# <a name="moderation-jobs-and-reviews"></a>Revisioni e processi di moderazione

Combinare funzionalità di moderazione automatica e human-in-the-loop usando l'[API di revisione](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) di Azure Content Moderator per ottenere risultati ottimali per l'azienda.

L'API di revisione consente di includere la supervisione umana nel processo di moderazione del contenuto nei modi seguenti:

* Le operazioni `Job` vengono usate per avviare la creazione di revisione umana e moderazione automatica in un unico passaggio.
* Le operazioni `Review` vengono usate per la creazione della revisione umana, al di fuori del passaggio di moderazione.
* Le operazioni `Workflow` vengono usate per gestire i flussi di lavoro che automatizzano l'analisi con soglie per la creazione della revisione.

Le operazioni `Job` e `Review` accettano gli endpoint callback per la ricezione dello stato e dei risultati.

Questo articolo illustra le operazioni `Job` e `Review`. Vedere [Panoramica dei flussi di lavoro](workflow-api.md) per informazioni su come creare, modificare e ottenere le definizioni dei flussi di lavoro.

## <a name="job-operations"></a>Operazioni di processo

### <a name="start-a-job"></a>Avviare un processo
Usare l'operazione `Job.Create` per avviare un processo di creazione di revisione umana e moderazione. Content Moderator analizza il contenuto e valuta il flusso di lavoro designato. In base ai risultati del flusso di lavoro, crea le revisioni o ignora il passaggio. Invia anche i tag post-moderazione e post-revisione all'endpoint callback.

Gli input includono le informazioni seguenti:

- ID del team di revisione.
- Contenuto da moderare.
- Nome del flusso di lavoro. Quello predefinito è il flusso di lavoro "default".
- Punto di callback API per le notifiche.
 
La risposta seguente mostra l'identificatore del processo avviato. Usare l'identificatore del processo per ottenere lo stato del processo e ricevere informazioni dettagliate.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Ottenere lo stato di processo

Usare l'operazione `Job.Get` e l'identificatore del processo per ottenere i dettagli di un processo in esecuzione o completato. L'operazione viene immediatamente restituita mentre il processo di moderazione viene eseguito in modo asincrono. I risultati vengono restituiti tramite l'endpoint callback.

Gli input includono le informazioni seguenti:

- ID del team di revisione: identificatore del processo restituito dall'operazione precedente

La risposta include le informazioni seguenti:

- Identificatore della revisione creata. Usare questo ID per ottenere i risultati della revisione finale.
- Stato del processo (completato o in corso): tag di moderazione assegnati (coppie chiave-valore).
- Report di esecuzione del processo.
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
            {
                "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
                },
                {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
                },
                {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
                }
            ]
        }
 
![Revisione di immagini per moderatori umani](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Operazioni Review

### <a name="create-reviews"></a>Creare le revisioni

Usare l'operazione `Review.Create` per creare le revisioni umane. È possibile moderarle altrove o usare la logica personalizzata per assegnare i tag di moderazione.

Gli input per questa operazione includono:

- Contenuto da esaminare.
- Tag assegnati (coppie chiave-valore) per la revisione da parte di moderatori umani.

La risposta seguente mostra l'identificatore della revisione:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Ottenere lo stato della revisione
Usare l'operazione `Review.Get` per ottenere i risultati al termine di una revisione umana dell'immagine moderata. Si riceve una notifica tramite l'endpoint callback specificato. 

L'operazione restituisce due set di tag: 

* I tag assegnati dal servizio di moderazione
* I tag al termine della revisione umana

Gli input includono almeno:

- Nome del team di revisione
- Identificatore della revisione restituito dall'operazione precedente

La risposta include le informazioni seguenti:

- Stato della revisione
- Tag (coppie chiave-valore) confermati dal revisore umano
- Tag (coppie chiave-valore) assegnati dal servizio di moderazione

Nella risposta di esempio seguente vengono visualizzati sia i tag assegnati dal revisore (**reviewerResultTags**) che i tag iniziali (**metadata**):

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il test drive della [console per l'API Job](try-review-api-job.md) e usare gli esempi di codice dell'API REST. Se si ha familiarità con Visual Studio e C#, vedere anche la [guida introduttiva a .NET per i processi](moderation-jobs-quickstart-dotnet.md). 
* Per le revisioni, iniziare con la [console per l'API di revisione](try-review-api-review.md) e usare i codici di esempio dell'API REST. Vedere quindi la [guida introduttiva a .NET per le revisioni](moderation-reviews-quickstart-dotnet.md).
* Per le revisioni di video, usare la [guida introduttiva alle revisioni di video](video-reviews-quickstart-dotnet.md) e vedere come [aggiungere trascrizioni alla revisione di un video](video-transcript-reviews-quickstart-dotnet.md).
