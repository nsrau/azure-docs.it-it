---
title: Panoramica di Azure Media Player
description: Altre informazioni su Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 6f3c173c61e9f6d82a51fcaf7e11cda47eb47c1c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87011709"
---
# <a name="azure-media-player-overview"></a>Panoramica di Azure Media Player #

Azure Media Player è un lettore video Web progettato per consentire la riproduzione di contenuti multimediali da [Servizi multimediali di Microsoft Azure](https://azure.microsoft.com/services/media-services/) su una vasta gamma di browser e dispositivi. Azure Media Player usa standard di settore come HTML5, MSE (Media Source Extensions) ed EME (Encrypted Media Extensions) per fornire un'esperienza di streaming adattivo completa.  Se questi standard non sono disponibili in un dispositivo o in un browser, Azure Media Player usa una tecnologia di fallback come Flash o Silverlight. Indipendentemente dalla tecnologia di riproduzione usata, quindi, gli sviluppatori disporranno di un'interfaccia JavaScript unificata per accedere alle API.  In questo modo, i contenuti distribuiti da Servizi multimediali di Azure potranno essere riprodotti su un'ampia gamma di dispositivi e browser senza alcuna operazione aggiuntiva.

Servizi multimediali di Microsoft Azure consente di distribuire contenuti nei formati di streaming DASH, Smooth Streaming e HLS per consentirne la riproduzione. Azure Media Player prende in considerazione tutti i vari formati e automaticamente riproduce il link migliore in base alle funzionalità della piattaforma o del browser. Servizi multimediali di Microsoft Azure consente inoltre la crittografia dinamica degli asset con la crittografia comune (PlayReady o Widevine) o la crittografia envelope AES a 128 bit. Anche Azure Media Player consente la decrittografia di contenuti crittografati con PlayReady o AES a 128 bit, se correttamente configurati.  Per informazioni sulla configurazione, vedere la sezione [Contenuto protetto](azure-media-player-protected-content.md).

Per richiedere nuove funzionalità, sottoporre idee o inviare feedback, usare il sito [UserVoice per Servizi multimediali di Azure](https://aka.ms/ampuservoice). In caso di domande o problemi specifici o se si riscontrano bug, inviare un messaggio all'indirizzo ampinfo@microsoft.com.

[Iscriversi](https://aka.ms/ampsignup) per non perdere mai una versione e rimanere sempre aggiornati con le ultime novità offerte da Azure Media Player.

> [!NOTE]
> Si noti che Azure Media Player supporta solo flussi multimediali provenienti da Servizi multimediali di Azure.

## <a name="license"></a>Licenza ##

Azure Media Player è concesso in licenza e soggetto alle condizioni definite nelle Condizioni di licenza software Microsoft per Azure Media Player. Per le condizioni complete, vedere il [file di licenza](/legal/azure-media-player/azure-media-player-license). Per altre informazioni, vedere l'[informativa sulla privacy](https://www.microsoft.com/en-us/privacystatement/default.aspx).

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)
