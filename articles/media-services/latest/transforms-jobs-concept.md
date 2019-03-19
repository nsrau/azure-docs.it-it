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
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 1c2ec576211741390ef91233101261a7881e4180
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652213"
---
# <a name="transforms-and-jobs"></a>Trasformazioni e processi

Questo argomento illustra dettagliatamente [trasforma](https://docs.microsoft.com/rest/api/media/transforms) e [processi](https://docs.microsoft.com/rest/api/media/jobs) e illustra la relazione tra queste entità. Il diagramma seguente mostra il flusso di lavoro di trasformazioni e processi.

![Trasformazioni](./media/encoding/transforms-jobs.png)

> [!NOTE]
> Le proprietà della **trasformazione** e del **processo** di tipo Datetime sono sempre in formato UTC.

## <a name="transforms"></a>Trasformazioni

Usare **Trasformazioni** per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni **trasformazione** descrive un recipe o un flusso di lavoro di attività per l'elaborazione dei file video o audio. Una singola trasformazione può applicare più di una regola. Ad esempio, una trasformazione può specificare che ogni video venga codificato in un file MP4 a un dato bitrate e che venga generata un'immagine di anteprima dal primo fotogramma del video. È possibile aggiungere una voce TransformOutput per ogni regola che si prevede di includere nella trasformazione. È possibile creare trasformazioni nell'account di Servizi multimediali usando l'API di Servizi multimediali v3 o uno degli SDK pubblicati. L'API di Servizi multimediali v3 è determinata da Azure Resource Manager, quindi è possibile usare modelli di Resource Manager per creare e distribuire trasformazioni nell'account di Servizi multimediali. Si può usare il controllo degli accessi in base al ruolo per bloccare l'accesso alle trasformazioni.

L'operazione di aggiornamento (Update) per l'entità [Transform](https://docs.microsoft.com/rest/api/media/transforms) ha lo scopo di apportare modifiche alla descrizione o alle priorità di TransformOutputs sottostanti. È consigliabile eseguire tali aggiornamenti dopo il completamento di tutti i processi in corso. Se si intende riscrivere il file recipe, è necessario creare una nuova trasformazione.

## <a name="jobs"></a>Processi

Un **processo** è la richiesta effettiva inviata a Servizi multimediali per applicare la **trasformazione** a determinati contenuti audio o video di input. Dopo aver creato la trasformazione, è possibile inviare i processi usando le API di Servizi multimediali o uno degli SDK pubblicati. Il **processo** specifica informazioni come la posizione del video di input e quella dell'output. È possibile specificare il percorso del video di input mediante: URL HTTPS, URL di firma di accesso condiviso o [asset](https://docs.microsoft.com/rest/api/media/assets).  

Uso [processo di input da HTTPS](job-input-from-http-how-to.md) se il contenuto è accessibile tramite un URL già e non è necessario archiviare il file di origine in Azure (ad esempio, importazione da S3). Questo metodo è adatto anche se si dispone del contenuto nell'archivio Blob di Azure ma non necessario per il file in un Asset. Attualmente, questo metodo supporta solo un singolo file di input.
 
Uso [Asset come input del processo](job-input-from-local-file-how-to.md) se il contenuto di input è già in un Asset o il contenuto viene archiviato nel file locale. È anche un'opzione valida se si prevede di pubblicare l'asset di input per lo streaming o download (ad esempio si desidera pubblicare il mp4 per il download, ma si vuole anche eseguire operazioni di riconoscimento vocale per rilevamento viso o di testo). Questo metodo supporta l'asset a più file (ad esempio, MBR streaming set che sono stati codificati in locale).
 
È possibile ottenere l'avanzamento e lo stato dei processi monitorando gli eventi con Griglia di eventi. Per altre informazioni, vedere [Creare e monitorare eventi di Servizi multimediali con Griglia di eventi e l'interfaccia della riga di comando di Azure](job-state-events-cli-how-to.md).

L'operazione di aggiornamento (Update) per l'entità [Job](https://docs.microsoft.com/rest/api/media/jobs) può essere usata per modificare la *descrizione* e le proprietà relative alla *priorità* dopo l'invio del processo. Una modifica alla proprietà relativa alla *priorità* ha effetto solo se il processo è ancora accodato. Se il processo ha iniziato l'elaborazione o è terminato, la modifica della priorità non ha alcun effetto.

## <a name="typical-workflow"></a>Flusso di lavoro tipico

1. Creare un oggetto Transform 
2. Inviare i processi a tale oggetto Transform 
3. Elencare gli oggetti Transform 
4. Eliminare una trasformazione se non si intende usarla in futuro. 

### <a name="example"></a>Esempio

Si supponga di voler estrarre il primo frame di tutti i video come immagine di anteprima, i passaggi da eseguire sono: 

1. Definire il recipe o la regola per l'elaborazione dei video, ad esempio "usare il primo fotogramma del video come anteprima". 
2. Per ogni video è possibile indicare al servizio: 
    1. dove trovare il video,  
    2. dove scrivere l'immagine di anteprima dell'output. 

Una **trasformazione** consente di creare il file recipe una sola volta (passaggio 1) e inviare i processi che usano quel recipe (passaggio 2).

## <a name="job-error-codes"></a>Codici di errore dei processi

Vedere i [codici di errore](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="paging"></a>Paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](entities-overview.md).

## <a name="configure-media-reserved-units"></a>Configurare Media Reserved Units

Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 Media Reserved Units (MRU) S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).

Per informazioni dettagliate, vedere [Ridimensionamento dell'elaborazione di contenuti multimediali con l'interfaccia della riga di comando](media-reserved-units-cli-how-to.md).

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Eseguire il caricamento, la codifica e lo streaming di video con .NET](stream-files-tutorial-with-api.md)
- [Esercitazione: Analizzare i video con Servizi multimediali v3 usando .NET](analyze-videos-tutorial-with-api.md)
