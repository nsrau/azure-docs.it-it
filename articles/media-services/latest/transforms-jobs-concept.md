---
title: Trasformazioni e processi in Servizi multimediali di Azure | Microsoft Docs
description: Quando si usa Servizi multimediali, è necessario creare un oggetto Transform per descrivere le regole o le specifiche per l'elaborazione dei video. Questo articolo offre una panoramica dell'oggetto Transform e del relativo utilizzo.
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
ms.openlocfilehash: 466ab0737aa5af40bd1bc137b98ab57a48feafde
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637350"
---
# <a name="transforms-and-jobs"></a>Trasformazioni e processi

In questo argomento vengono fornite informazioni dettagliate sulle [trasformazioni](https://docs.microsoft.com/rest/api/media/transforms) e sui [processi](https://docs.microsoft.com/rest/api/media/jobs) e viene illustrata la relazione tra queste entità. 

## <a name="overview"></a>Panoramica 

### <a name="transformsjobs-workflow"></a>Flusso di lavoro trasformazioni/processi

Il diagramma seguente mostra il flusso di lavoro di trasformazioni e processi.

![Trasformazioni](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Flusso di lavoro tipico

1. Creare un oggetto Transform 
2. Inviare i processi a tale oggetto Transform 
3. Elencare gli oggetti Transform 
4. Eliminare una trasformazione se non si intende usarla in futuro. 

#### <a name="example"></a>Esempio

Si supponga di voler estrarre il primo frame di tutti i video come immagine di anteprima, i passaggi da eseguire sono: 

1. Definire il recipe o la regola per l'elaborazione dei video, ad esempio "usare il primo fotogramma del video come anteprima". 
2. Per ogni video è possibile indicare al servizio: 
    1. dove trovare il video,  
    2. dove scrivere l'immagine di anteprima dell'output. 

Una **trasformazione** consente di creare il file recipe una sola volta (passaggio 1) e inviare i processi che usano quel recipe (passaggio 2).

> [!NOTE]
> Le proprietà della **trasformazione** e del **processo** di tipo Datetime sono sempre in formato UTC.

## <a name="transforms"></a>Trasformazioni

Usare **Trasformazioni** per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni **trasformazione** descrive un recipe o un flusso di lavoro di attività per l'elaborazione dei file video o audio. Una singola trasformazione può applicare più di una regola. Ad esempio, una trasformazione può specificare che ogni video venga codificato in un file MP4 a un dato bitrate e che venga generata un'immagine di anteprima dal primo fotogramma del video. È possibile aggiungere una voce TransformOutput per ogni regola che si prevede di includere nella trasformazione. Usare i set di impostazioni per indicare alla trasformazione come devono essere elaborati i file multimediali di input.

### <a name="viewing-schema"></a>Visualizzazione dello schema

In servizi multimediali V3 i set di impostazioni sono entità fortemente tipizzate nell'API stessa. È possibile trovare la definizione dello schema per questi oggetti in [Open API Specification (o spavalderia)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). È anche possibile visualizzare le definizioni di set di impostazioni (ad esempio **StandardEncoderPreset**) nell' [API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (o altri documenti di riferimento di Media Services V3 SDK).

### <a name="creating-transforms"></a>Creazione di trasformazioni

È possibile creare trasformazioni con REST, CLI o usare uno degli SDK pubblicati. L'API di Servizi multimediali v3 è determinata da Azure Resource Manager, quindi è possibile usare modelli di Resource Manager per creare e distribuire trasformazioni nell'account di Servizi multimediali. Si può usare il controllo degli accessi in base al ruolo per bloccare l'accesso alle trasformazioni.

### <a name="updating-transforms"></a>Aggiornamento delle trasformazioni

Se è necessario aggiornare la [trasformazione](https://docs.microsoft.com/rest/api/media/transforms), utilizzare l'operazione di **aggiornamento** . È concepito per apportare modifiche alla descrizione o per le priorità dei TransformOutputs sottostanti. È consigliabile eseguire tali aggiornamenti dopo il completamento di tutti i processi in corso. Se si intende riscrivere la ricetta, è necessario creare una nuova trasformazione.

### <a name="transform-object-diagram"></a>Trasformazione diagramma oggetti

Il diagramma seguente mostra l'oggetto **Transform** e gli oggetti a cui fa riferimento, incluse le relazioni di derivazione. Le frecce grigie mostrano un tipo a cui fa riferimento il processo e le frecce verdi mostrano le relazioni di derivazione delle classi.<br/>Fare clic sull'immagine per visualizzarla a schermo intero.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>Processi

Un **processo** è la richiesta effettiva inviata a Servizi multimediali per applicare la **trasformazione** a determinati contenuti audio o video di input. Dopo aver creato la trasformazione, è possibile inviare i processi usando le API di Servizi multimediali o uno degli SDK pubblicati. Il **processo** specifica informazioni come la posizione del video di input e quella dell'output. È possibile specificare il percorso del video di input mediante: URL HTTPS, URL di firma di accesso condiviso o [asset](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Input del processo da HTTPS

Usare l' [input del processo da https](job-input-from-http-how-to.md) se il contenuto è già accessibile tramite un URL e non è necessario archiviare il file di origine in Azure, ad esempio importare da S3. Questo metodo è adatto anche se il contenuto è disponibile nell'archiviazione BLOB di Azure, ma non è necessario che il file si trovi in un asset. Attualmente, questo metodo supporta solo un singolo file per l'input.

### <a name="asset-as-job-input"></a>Asset come input del processo

Usare [asset come input del processo](job-input-from-local-file-how-to.md) se il contenuto di input è già in un asset o se il contenuto viene archiviato nel file locale. È anche un'opzione utile se si prevede di pubblicare l'asset di input per lo streaming o il download, ad eccezione del caso in cui si vuole pubblicare il MP4 per il download, ma si vuole anche eseguire il riconoscimento vocale o del viso. Questo metodo supporta asset con più file (ad esempio, set di flussi MBR che sono stati codificati localmente).

### <a name="checking-job-progress"></a>Verifica dello stato del processo

È possibile ottenere l'avanzamento e lo stato dei processi monitorando gli eventi con Griglia di eventi. Per altre informazioni, vedere [Creare e monitorare eventi di Servizi multimediali con Griglia di eventi e l'interfaccia della riga di comando di Azure](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Aggiornamento di processi

L'operazione di aggiornamento (Update) per l'entità [Job](https://docs.microsoft.com/rest/api/media/jobs) può essere usata per modificare la *descrizione* e le proprietà relative alla *priorità* dopo l'invio del processo. Una modifica alla proprietà relativa alla *priorità* ha effetto solo se il processo è ancora accodato. Se il processo ha iniziato l'elaborazione o è terminato, la modifica della priorità non ha alcun effetto.

### <a name="job-object-diagram"></a>Diagramma oggetti processo

Il diagramma seguente mostra l'oggetto **processo** e gli oggetti a cui fa riferimento, incluse le relazioni di derivazione.<br/>Fare clic sull'immagine per visualizzarla a schermo intero.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>Configurare Media Reserved Units

Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 Media Reserved Units (MRU) S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).

Per informazioni dettagliate, vedere [Ridimensionamento dell'elaborazione di contenuti multimediali con l'interfaccia della riga di comando](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedere anche

* [Codici di errore](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtraggio, ordinamento e paging delle entità di servizi multimediali](entities-overview.md)

## <a name="next-steps"></a>Passaggi successivi

- Prima di iniziare lo sviluppo, vedere [sviluppo con le API di servizi multimediali V3](media-services-apis-overview.md) (include informazioni sull'accesso alle API, alle convenzioni di denominazione e così via).
- Vedere le esercitazioni seguenti:

    - [Esercitazione: Codificare un file remoto in base all'URL e trasmettere il video](stream-files-tutorial-with-rest.md)
    - [Esercitazione: Caricare, codificare e trasmettere in streaming video](stream-files-tutorial-with-api.md)
    - [Esercitazione: Analizzare i video con servizi multimediali V3](analyze-videos-tutorial-with-api.md)
