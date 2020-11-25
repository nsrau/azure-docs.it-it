---
title: Riproduzione con Azure Media Player - Azure | Microsoft Docs
description: Azure Media Player è un lettore video Web progettato per consentire la riproduzione di contenuti multimediali da Servizi multimediali di Microsoft Azure su una vasta gamma di browser e dispositivi.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: inhenkel
ms.openlocfilehash: a3b32110a7236d47946c3a1f901aaedf51b47775
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022218"
---
# <a name="playback-with-azure-media-player"></a>Riproduzione con Azure Media Player

Azure Media Player è un lettore video Web progettato per consentire la riproduzione di contenuti multimediali da Servizi multimediali di Microsoft Azure su una vasta gamma di browser e dispositivi. Azure Media Player usa standard di settore come HTML5, MSE (Media Source Extensions) ed EME (Encrypted Media Extensions) per fornire un'esperienza di streaming adattivo completa. Se questi standard non sono disponibili in un dispositivo o in un browser, Azure Media Player usa una tecnologia di fallback come Flash o Silverlight. Indipendentemente dalla tecnologia di riproduzione usata, quindi, gli sviluppatori disporranno di un'interfaccia JavaScript unificata per accedere alle API. In questo modo, i contenuti distribuiti da Servizi multimediali di Azure potranno essere riprodotti su un'ampia gamma di dispositivi e browser senza alcuna operazione aggiuntiva.

Servizi multimediali di Microsoft Azure consente di distribuire contenuti nei formati di streaming HLS; DASH e Smooth Streaming per consentirne la riproduzione. Azure Media Player prende in considerazione tutti i vari formati e automaticamente riproduce il link migliore in base alle funzionalità della piattaforma o del browser. Servizi multimediali consente inoltre la crittografia dinamica degli asset con la crittografia PlayReady o la crittografia della busta AES a 128 bit. Anche Azure Media Player consente la decrittografia di contenuti crittografati con PlayReady o AES a 128 bit, se correttamente configurati.

> [!NOTE]
> La riproduzione HTTPS è necessaria per il contenuto crittografato con Widevine.

## <a name="use-azure-media-player-demo-page"></a>Usare la pagina demo di Azure Media Player

### <a name="start-using"></a>Iniziare l'utilizzo

È possibile usare la [pagina demo Azure Media Player](https://aka.ms/azuremediaplayer) per riprodurre esempi di Servizi multimediali di Azure nel proprio flusso.  

Per riprodurre un nuovo video, incollare un URL diverso e fare clic su **Update** (Aggiorna).

Per configurare diverse opzioni di riproduzione (ad esempio, tecnologia, lingua o crittografia), selezionare **Advanced Options** (opzioni avanzate).

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorare la diagnostica di un flusso video

È possibile usare la [pagina demo di Azure Media Player](https://aka.ms/azuremediaplayer) per monitorare la diagnostica di un flusso video.

![Diagnostica di Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Configurare Azure Media Player nel codice HTML

Azure Media Player è semplice da configurare. Bastano pochi istanti per ottenere la riproduzione di base di contenuti multimediali dall'account Servizi multimediali. Vedere la [documentazione di Azure Media Player](../azure-media-player/azure-media-player-overview.md) per informazioni dettagliate su come installare e configurare Azure Media Player.

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [Esempi di Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)