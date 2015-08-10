<properties 
	pageTitle="Creazione di flussi di lavoro di codifica avanzati con Progettazione flussi di lavoro" 
	description="Informazioni su come creare flussi di lavoro di codifica avanzati con Progettazione flussi di lavoro." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="juliako"/>


#Creazione di flussi di lavoro di codifica avanzati con Progettazione flussi di lavoro

##Panoramica
**Progettazione flussi di lavoro** è uno strumento autonomo che consente di creare flussi di lavoro e/o grafici per la codifica con il **Flusso di lavoro Premium del codificatore multimediale**.

Questo strumento può essere usato anche per modificare [flussi di lavoro esistenti](media-services-workflow-designer.md#existing_workflows).

>[AZURE.NOTE]Per ottenere una copia dello strumento Progettazione flussi di lavoro, contattare mepd@microsoft.com.


Dopo aver creato un file del flusso di lavoro, lo strumento può essere caricato come risorsa e può quindi essere usato per la codifica di file multimediali. Per informazioni su come codificare con il **Flusso di lavoro Premium del codificatore multimediale** usando **.NET**, vedere [Codifica avanzata con il flusso di lavoro Premium del codificatore multimediale](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modificare i flussi di lavoro esistenti

È possibile modificare i file del flusso di lavoro predefiniti usando lo strumento di progettazione. È possibile ottenere i file del flusso di lavoro predefiniti [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Nella cartella è presente anche una descrizione dei file.

I video seguenti illustrano come usare la finestra di progettazione.

###Giorno 1 - Introduzione

Il video del giorno 1 riguarda:

- Panoramica della finestra di progettazione
- Flussi di lavoro di base - "Hello World"
- Creazione di più file di output MP4 da usare lo streaming di Servizi multimediali di Azure

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###Giorno 2

Il video del giorno 2 riguarda:

- Vari scenari di file di origine - Gestione dell'audio
- Flussi di lavoro con logica avanzata
- Fasi del grafico

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###Giorno 3

Il video del giorno 3 riguarda:

- Creazione di script all'interno di flussi di lavoro/progetti
- Restrizioni del codificatore corrente
- Domande e risposte
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]

##Vedere anche

[Video di formazione sulla finestra di progettazione del flusso di lavoro del codificatore Premium di Azure](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<!---HONumber=July15_HO5-->