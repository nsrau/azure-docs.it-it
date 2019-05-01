---
title: Caricare file in un account Servizi multimediali di Azure da Azure StorSimple | Microsoft Docs
description: Questo articolo offre una breve panoramica del gestore dati di Azure StorSimple. L'articolo contiene anche collegamenti a esercitazioni che illustrano come estrarre dati da StorSimple e caricarli come asset in un account Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c77b700cab4afd411c3a2df824ee8335cb394cda
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868304"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Caricare file in un account Servizi multimediali di Azure da Azure StorSimple  

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](https://docs.microsoft.com/azure/media-services/latest/). Vedere anche [materiale sussidiario di migrazione dalla v2 alla v3](../latest/migrate-from-v2-to-v3.md)
>
> 
> Il gestore dati di Azure StorSimple è attualmente in anteprima privata. 
> 

## <a name="overview"></a>Panoramica

In Servizi multimediali è possibile caricare i file digitali in un asset. L'asset può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, oltre ai metadati relativi a questi file. Dopo aver caricato i file, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming.

[Azure StorSimple](https://docs.microsoft.com/azure/storsimple/) usa l'archiviazione cloud come un'estensione della soluzione locale e organizza automaticamente i dati in livelli tra archiviazione locale e archiviazione cloud. Il dispositivo StorSimple elimina i duplicati e comprime i dati prima di inviarli al cloud, migliorando notevolmente l'efficienza dell'invio di file di grandi dimensioni nel cloud. Il servizio [gestore dati StorSimple](../../storsimple/storsimple-data-manager-overview.md) include API che consentono di estrarre dati da StorSimple e presentarli come asset di Servizi multimediali di Azure.

## <a name="get-started"></a>Attività iniziali

1. [Creare un account Servizi multimediali](media-services-portal-create-account.md) in cui trasferire gli asset.
2. Eseguire l'iscrizione per l'anteprima del gestore dati, come descritto nell'articolo relativo al [gestore dati StorSimple](../../storsimple/storsimple-data-manager-overview.md).
3. Creare un account del gestore dati StorSimple.
4. Creare un processo di trasformazione dei dati che, se eseguito, estrae i dati da un dispositivo StorSimple e li trasferisce come asset in un account Servizi multimediali di Azure. 

    Quando si avvia l'esecuzione del processo, viene creata una coda di archiviazione. Questa coda viene popolata con i messaggi relativi ai BLOB trasformati non appena pronti. Il nome di questa coda corrisponde a quello della definizione di processo. È possibile usare la coda per stabilire quando un asset è pronto e chiamare l'operazione di Servizi multimediali da eseguire sull'asset. Ad esempio, è possibile usare la coda per attivare una funzione di Azure contenente il codice di Servizi multimediali necessario.

## <a name="see-also"></a>Vedere anche 

[Usare .NET SDK per attivare processi in Gestione dati](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora è possibile codificare gli asset caricati. Per altre informazioni, vedere [Encode assets](media-services-portal-encode.md)(Codificare gli asset).
