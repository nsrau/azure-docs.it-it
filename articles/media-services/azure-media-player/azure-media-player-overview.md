---
title: Panoramica di Azure Media Player
description: Altre informazioni su Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 9b99e24e47da69aa0f081e056230f4b0c1aa6bb1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321723"
---
# <a name="azure-media-player-overview"></a>Panoramica di Azure Media Player #

Azure Media Player è un lettore video Web che riproduce i contenuti multimediali di [Servizi multimediali di Microsoft Azure](https://azure.microsoft.com/services/media-services/) in una vasta gamma di browser e dispositivi. Azure Media Player usa standard di settore come HTML5, MSE (Media Source Extensions) ed EME (Encrypted Media Extensions) per fornire un'esperienza completa di streaming adattivo.  Se questi standard non sono disponibili in un dispositivo o in un browser, Azure Media Player usa una tecnologia di fallback come Flash o Silverlight. Indipendentemente dalla tecnologia di riproduzione usata, gli sviluppatori hanno un'interfaccia JavaScript unificata per accedere alle API, che consente di riprodurre i contenuti di Servizi multimediali di Azure in una vasta gamma di dispositivi e browser senza interventi aggiuntivi.

Servizi multimediali di Microsoft Azure consente di distribuire contenuti nei formati di streaming DASH, Smooth Streaming e HLS per consentirne la riproduzione. Azure Media Player prende in considerazione tutti i vari formati e automaticamente riproduce il link migliore in base alle funzionalità della piattaforma o del browser. Servizi multimediali di Microsoft Azure prevede inoltre la crittografia dinamica degli asset con crittografia comune (PlayReady o Widevine) o envelope AES a 128 bit. Anche Azure Media Player consente la decrittografia di contenuti crittografati con PlayReady o AES a 128 bit, se correttamente configurati.  Per informazioni sulla configurazione del lettore, vedere la sezione [Contenuto protetto](azure-media-player-protected-content.md).

Per richiedere nuove funzionalità, sottoporre idee o inviare feedback, usare il sito [UserVoice per Azure Media Player](https://aka.ms/ampuservoice). In caso di domande o problemi specifici o se si riscontrano bug, inviare un messaggio all'indirizzo ampinfo@microsoft.com.

> [!NOTE]
> Si noti che Azure Media Player supporta solo flussi multimediali provenienti da Servizi multimediali di Azure.

## <a name="license"></a>Licenza ##

Azure Media Player è concesso in licenza e soggetto alle condizioni definite nelle Condizioni di licenza software Microsoft per Azure Media Player. Per le condizioni complete, vedere il [file di licenza](/legal/azure-media-player/azure-media-player-license). Per altre informazioni, vedere l'[informativa sulla privacy](https://www.microsoft.com/en-us/privacystatement/default.aspx).

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)
