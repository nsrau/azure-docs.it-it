<properties 
	pageTitle="Gestione dei processi di Servizi multimediali di Azure" 
	description="Questo argomento fornisce una panoramica della gestione dei processi di Servizi multimediali di Azure" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="juliako"/>

##Gestione dei processi

Un **processo** contiene i metadati relativi all'elaborazione da eseguire. Ogni **processo** contiene una o più **attività** che specificano un'attività di elaborazione atomica, i relativi asset di input, gli asset di output, un processore di contenuti multimediali e le impostazioni associate. Per altre informazioni sulle impostazioni del codificatore, vedere Guide dei decodificatori e Schemi del codificatore.

Un processo di codifica viene in genere combinato con altre attività di elaborazione, ad esempio creazione di pacchetti o crittografia di uno o più asset generati dal codificatore. Le attività contenute in un processo possono essere concatenate, in modo che l'asset di output di un'attività sia indicato come asset di input dell'attività successiva. In questo modo un processo può contenere tutte le operazioni di elaborazione necessarie per una presentazione multimediale.


>[AZURE.NOTE]Attualmente è previsto un limite di 30 attività per processo. Se è necessario concatenare più di 30 attività, creare più processi in modo da contenerle tutte.

##Utilizzo di processi/attività

Questa sezione contiene collegamenti alle attività comuni che vengono svolte durante l'uso di processi/attività.

###Acquisizione del processore di contenuti multimediali

Ottenere il processore di contenuti multimediali con **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

###Creazione di processi 

Un processo è un'entità contenente i metadati relativi a un set di attività, ad esempio la codifica o l'indicizzazione. Ogni attività esegue in modo atomico un'operazione sugli asset di input. Per un esempio su come creare processi di codifica, vedere:

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###Indicizzazione

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###Codifica 

Codificare con **Codificatore multimediale di Azure** tramite il **portale di gestione di Azure**, **.NET** o **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###Monitoraggio dello stato di avanzamento dei processi

Monitorare lo stato di avanzamento dei processi tramite il **portale di gestione di Azure**, **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

##Collegamenti correlati

[Quote e limitazioni](../media-services-quotas-and-limitations) - Descrive le quote usate e le limitazioni del codificatore di Servizi multimediali

<!--HONumber=47-->
