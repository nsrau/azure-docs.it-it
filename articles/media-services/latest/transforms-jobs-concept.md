---
title: Trasformazioni e processi in servizi multimediali
titleSuffix: Azure Media Services
description: Informazioni su come creare una trasformazione per descrivere le regole per l'elaborazione dei video in servizi multimediali di Azure.
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
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571227"
---
# <a name="transforms-and-jobs-in-media-services"></a>Trasformazioni e processi in servizi multimediali

In questo argomento vengono fornite informazioni dettagliate sulle [trasformazioni](https://docs.microsoft.com/rest/api/media/transforms) e sui [processi](https://docs.microsoft.com/rest/api/media/jobs) e viene illustrata la relazione tra queste entità.

## <a name="overview"></a>Panoramica

### <a name="transformsjobs-workflow"></a>Flusso di lavoro trasformazioni/processi

Il diagramma seguente illustra il flusso di lavoro trasformazioni/processi:

![Flusso di lavoro di trasformazioni e processi in servizi multimediali di Azure](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Flusso di lavoro tipico

1. Creare una trasformazione.
2. Inviare i processi nella trasformazione.
3. Elencare le trasformazioni.
4. Eliminare una trasformazione se non si prevede di usarla in futuro.

#### <a name="example"></a>Esempio

Si supponga di voler estrarre il primo frame di tutti i video come immagine di anteprima, ovvero i passaggi da eseguire:

1. Definire la ricetta o la regola per l'elaborazione dei video: "usare la prima cornice del video come anteprima".
2. Per ogni video si indicherà al servizio:
    1. Dove trovare il video.
    2. dove scrivere l'immagine di anteprima dell'output.

Una **trasformazione** consente di creare il file recipe una sola volta (passaggio 1) e inviare i processi che usano quel recipe (passaggio 2).

> [!NOTE]
> Le proprietà della **trasformazione** e del **processo** del tipo DateTime sono sempre in formato UTC.

## <a name="transforms"></a>Trasformazioni

Usare **Trasformazioni** per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni **trasformazione** descrive una ricetta o un flusso di lavoro di attività per l'elaborazione di file video o audio. Una singola trasformazione può applicare più di una regola. Ad esempio, una trasformazione può specificare che ogni video venga codificato in un file MP4 a un dato bitrate e che venga generata un'immagine di anteprima dal primo fotogramma del video. È possibile aggiungere una voce TransformOutput per ogni regola che si prevede di includere nella trasformazione. Usare i set di impostazioni per indicare alla trasformazione come devono essere elaborati i file multimediali di input.

### <a name="viewing-schema"></a>Visualizzazione dello schema

In servizi multimediali V3 i set di impostazioni sono entità fortemente tipizzate nell'API stessa. È possibile trovare la definizione dello schema per questi oggetti in [Open API Specification (o spavalderia)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). È anche possibile visualizzare le definizioni di set di impostazioni (ad esempio **StandardEncoderPreset**) nell' [API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)o altri documenti di riferimento di Media Services V3 SDK.

### <a name="creating-transforms"></a>Creazione di trasformazioni

È possibile creare trasformazioni usando REST, l'interfaccia della riga di comando o uno degli SDK pubblicati. L'API di Servizi multimediali v3 è determinata da Azure Resource Manager, quindi è possibile usare modelli di Resource Manager per creare e distribuire trasformazioni nell'account di Servizi multimediali. Si può usare il controllo degli accessi in base al ruolo per bloccare l'accesso alle trasformazioni.

### <a name="updating-transforms"></a>Aggiornamento delle trasformazioni

Se è necessario aggiornare la [trasformazione](https://docs.microsoft.com/rest/api/media/transforms), utilizzare l'operazione di **aggiornamento** . È concepito per apportare modifiche alla descrizione o per le priorità dei TransformOutputs sottostanti. È consigliabile eseguire tali aggiornamenti al termine di tutti i processi in corso. Se si intende riscrivere la ricetta, è necessario creare una nuova trasformazione.

### <a name="transform-object-diagram"></a>Trasformazione diagramma oggetti

Il diagramma seguente mostra l'oggetto **Transform** e gli oggetti a cui fa riferimento, incluse le relazioni di derivazione. Le frecce grigie mostrano un tipo a cui fa riferimento il processo e le frecce verdi mostrano le relazioni di derivazione delle classi.

Selezionare l'immagine per visualizzarla a schermo intero.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Processi

Un **processo** è la richiesta effettiva a servizi multimediali di applicare la **trasformazione** a un video di input o a un contenuto audio specifico. Dopo aver creato la trasformazione, è possibile inviare i processi usando le API di Servizi multimediali o uno degli SDK pubblicati. Il **processo** specifica informazioni come il percorso del video di input e la posizione dell'output. È possibile specificare il percorso del video di input usando: URL HTTPS, URL SAS o [Asset](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Input del processo da HTTPS

Usare l' [input del processo da https](job-input-from-http-how-to.md) se il contenuto è già accessibile tramite un URL e non è necessario archiviare il file di origine in Azure, ad esempio importare da S3. Questo metodo è adatto anche se il contenuto è disponibile nell'archiviazione BLOB di Azure, ma non è necessario che il file si trovi in un asset. Attualmente, questo metodo supporta solo un singolo file per l'input.

### <a name="asset-as-job-input"></a>Asset come input del processo

Usare [asset come input del processo](job-input-from-local-file-how-to.md) se il contenuto di input è già in un asset o se il contenuto viene archiviato nel file locale. È anche un'opzione utile se si prevede di pubblicare l'asset di input per lo streaming o il download (si consiglia di pubblicare il MP4 per il download, ma anche di eseguire il riconoscimento vocale o del viso). Questo metodo supporta asset con più file (ad esempio, set di flussi MBR che sono stati codificati localmente).

### <a name="checking-job-progress"></a>Verifica dello stato del processo

È possibile ottenere l'avanzamento e lo stato dei processi monitorando gli eventi con Griglia di eventi. Per altre informazioni, vedere [Creare e monitorare eventi di Servizi multimediali con Griglia di eventi e l'interfaccia della riga di comando di Azure](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Aggiornamento di processi

L'operazione di aggiornamento sull'entità [Job](https://docs.microsoft.com/rest/api/media/jobs) può essere utilizzata per modificare la *Descrizione* e le proprietà di *priorità* dopo l'invio del processo. Una modifica alla proprietà relativa alla *priorità* ha effetto solo se il processo è ancora accodato. Se il processo ha iniziato l'elaborazione o è terminato, la modifica della priorità non ha alcun effetto.

### <a name="job-object-diagram"></a>Diagramma oggetti processo

Il diagramma seguente mostra l'oggetto **processo** e gli oggetti a cui fa riferimento, incluse le relazioni di derivazione.

Fare clic sull'immagine per visualizzarla a schermo intero.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Configurare Media Reserved Units

Per i processi di analisi audio e analisi dei video attivati da Media Services V3 o Video Indexer, è consigliabile effettuare il provisioning dell'account con 10 media reserved Unit S3 (MRU). Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).

Per informazioni dettagliate, vedere [Ridimensionamento dell'elaborazione di contenuti multimediali con l'interfaccia della riga di comando](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

* [Codici di errore](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtraggio, ordinamento e paging delle entità di servizi multimediali](entities-overview.md)

## <a name="next-steps"></a>Passaggi successivi

- Prima di iniziare lo sviluppo, vedere [sviluppo con le API di servizi multimediali V3](media-services-apis-overview.md) (include informazioni sull'accesso alle API, alle convenzioni di denominazione e così via).
- Vedere le esercitazioni seguenti:

    - [Esercitazione: codificare un file remoto in base all'URL e trasmettere il video](stream-files-tutorial-with-rest.md)
    - [Esercitazione: caricare, codificare e trasmettere in streaming video](stream-files-tutorial-with-api.md)
    - [Esercitazione: analizzare i video con servizi multimediali V3](analyze-videos-tutorial-with-api.md)
