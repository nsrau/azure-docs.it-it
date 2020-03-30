---
title: Trasformazioni e processi in Servizi multimedialiTransforms and Jobs in Media Services
titleSuffix: Azure Media Services
description: Informazioni su come creare trasformazioni per descrivere le regole per l'elaborazione dei video in Servizi multimediali di Azure.Learn how to create a Transforms to describe the rules for processing your videos in Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571227"
---
# <a name="transforms-and-jobs-in-media-services"></a>Trasformazioni e processi in Servizi multimedialiTransforms and Jobs in Media Services

In questo argomento vengono fornite informazioni dettagliate sulle [trasformazioni](https://docs.microsoft.com/rest/api/media/transforms) e le [posizioni in](https://docs.microsoft.com/rest/api/media/jobs) grado di lavoro e viene illustrata la relazione tra queste entità.

## <a name="overview"></a>Panoramica

### <a name="transformsjobs-workflow"></a>Flusso di lavoro Trasformazioni/Processi

Nel diagramma seguente viene illustrato il flusso di lavoro trasformazioni/processi:The following diagram shows transforms/jobs workflow:

![Flusso di lavoro di trasformazioni e processi in Servizi multimediali di AzureTransforms and jobs workflow in Azure Media Services](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Flusso di lavoro tipico

1. Creare una trasformazione.
2. Invia lavori sotto quella trasformazione.
3. Trasformazioni elenco.
4. Eliminare una trasformazione, se non si prevede di utilizzarla in futuro.

#### <a name="example"></a>Esempio

Supponiamo che tu voglia estrarre il primo fotogramma di tutti i tuoi video come immagine di anteprima: i passaggi da eseguire sono:

1. Definisci la ricetta o la regola per l'elaborazione dei tuoi video: "usa il primo fotogramma del video come miniatura".
2. Per ogni video, si dovrebbe dire il servizio:
    1. Dove trovare quel video.
    2. dove scrivere l'immagine di anteprima dell'output.

Una **trasformazione** consente di creare il file recipe una sola volta (passaggio 1) e inviare i processi che usano quel recipe (passaggio 2).

> [!NOTE]
> Le proprietà di **Transform** e **Job** di tipo Datetime sono sempre in formato UTC.

## <a name="transforms"></a>Trasformazioni

Usare **Trasformazioni** per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni **trasformazione** descrive una ricetta o un flusso di lavoro di attività per l'elaborazione dei file video o audio. Una singola trasformazione può applicare più di una regola. Ad esempio, una trasformazione può specificare che ogni video venga codificato in un file MP4 a un dato bitrate e che venga generata un'immagine di anteprima dal primo fotogramma del video. È possibile aggiungere una voce TransformOutput per ogni regola che si prevede di includere nella trasformazione. I predefiniti vengono utilizzati per indicare alla trasformazione la modalità di elaborazione dei file multimediali di input.

### <a name="viewing-schema"></a>Visualizzazione dello schema

In Servizi multimediali v3, le preimpostazioni sono entità fortemente tipizzate nell'API stessa. È possibile trovare la definizione "schema" per questi oggetti in [Open API Specification (o Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). È inoltre possibile visualizzare le definizioni preimpostate (ad esempio **StandardEncoderPreset**) [nell'API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [in .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)o in altra documentazione di riferimento di Servizi multimediali v3 SDK.

### <a name="creating-transforms"></a>Creazione di trasformazioni

È possibile creare trasformazioni usando REST, l'interfaccia della riga di comando o uno degli SDK pubblicati. L'API di Servizi multimediali v3 è determinata da Azure Resource Manager, quindi è possibile usare modelli di Resource Manager per creare e distribuire trasformazioni nell'account di Servizi multimediali. Si può usare il controllo degli accessi in base al ruolo per bloccare l'accesso alle trasformazioni.

### <a name="updating-transforms"></a>Aggiornamento delle trasformazioniUpdating Transforms

Se è necessario aggiornare la [trasformazione](https://docs.microsoft.com/rest/api/media/transforms), utilizzare l'operazione **di aggiornamento** . È destinato ad apportare modifiche alla descrizione o alle priorità dell'oggetto TransformOutputs sottostante. È consigliabile eseguire tali aggiornamenti al completamento di tutti i processi in corso. Se si intende riscrivere la ricetta, è necessario creare una nuova trasformazione.

### <a name="transform-object-diagram"></a>Diagramma a oggetti di trasformazione

Nel diagramma seguente viene illustrato l'oggetto **Transform** e gli oggetti a cui fa riferimento, incluse le relazioni di derivazione. Le frecce grigie mostrano un tipo a cui il processo fa riferimento e le frecce verdi mostrano le relazioni di derivazione della classe.

Selezionare l'immagine per visualizzarla a dimensioni intere.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Processi

Un **processo** è la richiesta effettiva a Servizi multimediali di applicare la **trasformazione** a un determinato contenuto video o audio di input. Dopo aver creato la trasformazione, è possibile inviare i processi usando le API di Servizi multimediali o uno degli SDK pubblicati. Il **processo** specifica informazioni come la posizione del video di input e quella dell'output. È possibile specificare la posizione del video di input utilizzando: URL HTTPS, URL di sAS o [risorse](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Input del processo da HTTPSJob input from HTTPS

Usare l'input del [processo da HTTPS](job-input-from-http-how-to.md) se il contenuto è già accessibile tramite un URL e non è necessario archiviare il file di origine in Azure, ad esempio importare da S3.Use job input from HTTPS if your content is already accessible via a URL and you don't need to store the source file in Azure (for example, import from S3). Questo metodo è adatto anche se si dispone del contenuto nell'archivio BLOB di Azure, ma non è necessario che il file si trovi in un asset. Attualmente, questo metodo supporta solo un singolo file per l'input.

### <a name="asset-as-job-input"></a>Asset come Input lavoro

Usa [Asset come input del lavoro](job-input-from-local-file-how-to.md) se il contenuto di input è già presente in una risorsa o se il contenuto è archiviato in un file locale. È anche una buona opzione se si prevede di pubblicare l'asset di input per lo streaming o il download (ad esempio, si desidera pubblicare l'mp4 per il download, ma anche se si desidera eseguire il riconoscimento vocale o del rilevamento del volto). Questo metodo supporta risorse su più file (ad esempio, set di streaming MBR codificati localmente).

### <a name="checking-job-progress"></a>Controllo dell'avanzamento del processo

È possibile ottenere l'avanzamento e lo stato dei processi monitorando gli eventi con Griglia di eventi. Per altre informazioni, vedere [Creare e monitorare eventi di Servizi multimediali con Griglia di eventi e l'interfaccia della riga di comando di Azure](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Aggiornamento dei processi

L'operazione di aggiornamento sull'entità [Processo](https://docs.microsoft.com/rest/api/media/jobs) può essere utilizzata per modificare la *descrizione* e le proprietà di *priorità* dopo l'invio del processo. Una modifica alla proprietà relativa alla *priorità* ha effetto solo se il processo è ancora accodato. Se il processo ha iniziato l'elaborazione o è terminato, la modifica della priorità non ha alcun effetto.

### <a name="job-object-diagram"></a>Diagramma a oggetti processo

Nel diagramma seguente viene illustrato l'oggetto **processo** e gli oggetti a cui fa riferimento, incluse le relazioni di derivazione.

Fare clic sull'immagine per visualizzarla a schermo intero.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Configurare Media Reserved Units

Per i processi di analisi audio e video attivati da Servizi multimediali v3 o Registrato Indexer, è consigliabile eseguire il provisioning dell'account con 10 Unità riservate (MRU) multimediali S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).

Per informazioni dettagliate, vedere [Ridimensionamento dell'elaborazione di contenuti multimediali con l'interfaccia della riga di comando](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

* [Codici di errore](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md)

## <a name="next-steps"></a>Passaggi successivi

- Prima di iniziare lo sviluppo, vedere Sviluppo con API di [Servizi multimediali v3](media-services-apis-overview.md) (include informazioni sull'accesso alle API, convenzioni di denominazione e così via).
- Dai un'occhiata a questi tutorial:

    - [Esercitazione: Codificare un file remoto in base all'URL e trasmettere il video in streaming](stream-files-tutorial-with-rest.md)
    - [Esercitazione: Caricare, codificare e riprodurre in streaming video](stream-files-tutorial-with-api.md)
    - [Esercitazione: Analizzare i video con Servizi multimediali v3](analyze-videos-tutorial-with-api.md)
