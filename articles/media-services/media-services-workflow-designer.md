---
title: Creazione di flussi di lavoro di codifica avanzati con Progettazione flussi di lavoro | Microsoft Docs
description: Informazioni su come creare flussi di lavoro di codifica avanzati con Progettazione flussi di lavoro.
services: media-services
documentationcenter: 
author: anilmur
manager: cfowler
editor: 
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: ef277710713d342eb9aaaf119e2985e8c1d82d0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Creazione di flussi di lavoro di codifica avanzati con Progettazione flussi di lavoro
## <a name="overview"></a>Panoramica
**Progettazione flussi di lavoro** è uno strumento desktop di Windows che consente di progettare e costruire flussi di lavoro per la codifica con il **Flusso di lavoro Premium del codificatore multimediale**.
Avvalendosi delle funzionalità di questo strumento per la progettazione di flussi di lavoro, è possibile progettare e creare flussi di lavoro complessi che verranno eseguiti nella versione **Premium di Azure Media Encoder**.  

I flussi di lavoro possono includere la logica decisionale del cliente e le relative diramazioni in base alle proprietà del file di origine di input. È possibile creare flussi di lavoro con proprietà sottoponibili a override e valori dinamici per offrire la possibilità di ripetere e personalizzare facilmente nel cloud anche le attività di codifica più complesse.

Di seguito sono riportati alcuni flussi di lavoro di esempio che è possibile creare:

* Flussi di lavoro basati su decisioni che esaminano la risoluzione del contenuto di origine e codificano solo le tracce di output desiderate.  Risultano particolarmente utili poiché consentono di eliminare le tracce inutilizzate che vengono generate se si esegue inavvertitamente l'upscaling del contenuto di origine.
* Possibilità di usare più file di input per supportare didascalie, sovrapposizioni e l'unione di più contenuti. 

È possibile usare questo strumento anche per modificare qualsiasi [flusso di lavoro pubblicato](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Per ottenere una copia dello strumento per la progettazione di flussi di lavoro, contattare mepd@microsoft.com.
> 
> 

Dopo aver creato un file del flusso di lavoro, lo strumento può essere caricato come risorsa e può quindi essere usato per la codifica di file multimediali. Per informazioni su come codificare con il **Flusso di lavoro Premium del codificatore multimediale** usando **.NET**, vedere [Codifica avanzata con il flusso di lavoro Premium del codificatore multimediale](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Modificare flussi di lavoro esistenti
Lo strumento di progettazione consente anche di modificare i [flussi di lavoro pubblicati](media-services-workflow-designer.md#existing_workflows) predefiniti. È possibile ottenere i file dei flussi di lavoro predefiniti [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Nella cartella è presente anche una descrizione dei file.

I video seguenti illustrano come usare la finestra di progettazione.

### <a name="day-1--getting-started"></a>Giorno 1 - Introduzione
Il video del giorno 1 riguarda:

* Panoramica della finestra di progettazione
* Flussi di lavoro di base - "Hello World"
* Creazione di più file di output MP4 da usare lo streaming di Servizi multimediali di Azure

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Giorno 2
Il video del giorno 2 riguarda:

* Vari scenari di file di origine - Gestione dell'audio
* Flussi di lavoro con logica avanzata
* Fasi del grafico

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Giorno 3
Il video del giorno 3 riguarda:

* Creazione di script all'interno di flussi di lavoro/progetti
* Restrizioni del codificatore corrente
* Domande e risposte

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Passaggio successivo
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

Per ottenere assistenza o ricevere informazioni per la creazione di flussi di lavoro personalizzati nello strumento per la progettazione di flussi di lavoro, inviare un messaggio di posta elettronica a mepd@microsoft.com.

## <a name="see-also"></a>Vedere anche
[Video di formazione sulla finestra di progettazione del flusso di lavoro del codificatore Premium di Azure](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

