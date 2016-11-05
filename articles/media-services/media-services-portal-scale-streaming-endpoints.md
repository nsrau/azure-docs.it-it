---
title: " Ridimensionare gli endpoint di streaming con il portale di Azure | Microsoft Docs"
description: Questa esercitazione descrive i passaggi per ridimensionare gli endpoint di streaming mediante il portale di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: juliako

---
# Ridimensionare gli endpoint di streaming con il portale di Azure
## Overview
> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> 
> 

Uno degli scenari più frequenti dell'utilizzo di Servizi multimediali di Azure riguarda la distribuzione ai client di contenuto video in streaming a bitrate adattivo. Servizi multimediali supporta le seguenti tecnologie di streaming a velocità in bit adattiva: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (solo per i titolari di licenza Adobe PrimeTime/Access).

Servizi multimediali include la funzionalità per la creazione dinamica dei pacchetti, che consente di distribuire contenuto con codifica MP4 a bitrate adattivo nei formati supportati dal servizio, come MPEG DASH, HLS, Smooth Streaming, HDS, in modalità JIT senza dover archiviare le versioni in pacchetti di ognuno di questi formati di streaming.

Per sfruttare i vantaggi del servizio di creazione dinamica dei pacchetti, è necessario seguire questa procedura:

* Codificare il file (di origine) in formato intermedio in un set di file MP4 a bitrate adattivo. La procedura per la codifica è descritti più avanti in questa esercitazione.
* Creare almeno un'unità di streaming per l'*endpoint di streaming* da cui si prevede di distribuire il contenuto. I passaggi seguenti illustrano come modificare il numero di unità di streaming.

Con la creazione dinamica dei pacchetti si archiviano e si pagano solo i file in un unico formato di archiviazione e Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

È possibile inoltre controllare la capacità del servizio endpoint di streaming in modo da poter gestire esigenze di larghezza di banda crescenti modificando le unità di streaming. Si consiglia di allocare una o più unità di scala per le applicazioni nell'ambiente di produzione. Le unità di streaming forniscono capacità di uscita dedicata acquistabile in incrementi di 200 Mbps e una funzionalità aggiuntiva che include: [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md), integrazione con la rete CDN e configurazione avanzata. Per altre informazioni, vedere [Gestione degli endpoint di streaming con il portale di Azure](media-services-portal-manage-streaming-endpoints.md).

## Ridimensionare gli endpoint di streaming
Per creare e modificare il numero di unità riservate di streaming, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella finestra **Impostazioni** selezionare **Endpoint di streaming**.
3. Fare clic sull'endpoint di streaming da ridimensionare.
4. Spostare il dispositivo di scorrimento per specificare il numero di unità di streaming

![Endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Si applicano le considerazioni seguenti:

* L'allocazione di nuove unità di streaming può richiedere circa 20 minuti.
* Attualmente, se si riporta a zero qualsiasi valore positivo delle unità di streaming, è possibile che lo streaming on demand venga disabilitato per un periodo che può durare fino a un'ora.
* Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo. Per informazioni sui prezzi, vedere [Dettagli prezzi di Servizi multimediali](http://go.microsoft.com/fwlink/?LinkId=275107).

## Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->