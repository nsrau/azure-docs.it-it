---
title: Disponibilità a livello di area di servizi multimediali di Azure | Microsoft Docs
description: In questo articolo viene illustrata una panoramica delle funzionalità Servizi multimediali di Microsoft Azure e della disponibilità a livello di area del servizio.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: cf84e45ff43643c7e9a983b867194a7920aa1b57
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351900"
---
# <a name="media-services-regional-availability"></a>Disponibilità a livello di area di servizi multimediali

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. Esplorare l'ultima versione, [Servizi multimediali v3](../latest/media-services-overview.md). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-from-v2-to-v3.md)

Servizi multimediali di Microsoft Azure (AMS) consente di caricare, archiviare e codificare contenuti video o audio in modo sicuro, nonché creare pacchetti di tali contenuti per la distribuzione in streaming live e on demand a vari client (ad esempio, TV, PC e dispositivi mobili).

AMS opera in più aree geografiche in tutto il mondo, offrendo la flessibilità necessaria per scegliere dove compilare le applicazioni. In questo articolo viene illustrata una panoramica delle funzionalità Servizi multimediali di Microsoft Azure e della disponibilità a livello di area del servizio.

Per altre informazioni sull'intera infrastruttura globale di Azure, vedere [geografie di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="ams-accounts"></a>Account AMS

Usare i [prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) per determinare se servizi multimediali è disponibile in un'area specifica.

## <a name="streaming-endpoints"></a>Endpoint di streaming

I clienti di Servizi multimediali possono scegliere un endpoint di streaming **Standard** o **Premium**.

|Nome|Stato|Region
|---|---|---|
|Standard|GA|Tutti|
|Premium|GA|Tutti|

## <a name="live-encoding"></a>Codifica live

Disponibile in tutte le aree eccetto: Germania, Brasile meridionale, India occidentale, India meridionale e India centrale.

## <a name="encoding-media-processors"></a>Processori di contenuti multimediali di codifica

AMS offre due codificatori su richiesta: **Media Encoder Standard** e **Flusso di lavoro Premium del codificatore multimediale**. Per altre informazioni, vedere [Panoramica e confronto dei codificatori multimediali su richiesta di Azure](media-services-encode-asset.md).

|Nome processore di contenuti multimediali|Stato|Regioni
|---|---|---|
|Codificatore multimediale standard|GA|Tutti|
|Flusso di lavoro Premium del codificatore multimediale|GA|Tutti tranne Cina|

## <a name="analytics-media-processors"></a>Processori di contenuti multimediali di analisi

Analisi Servizi multimediali è una raccolta di componenti per sintesi vocale e visione artificiale che permettono a organizzazioni e aziende di derivare in modo più semplice analisi approfondite di utilità pratica dai loro file video. Per altre informazioni, vedere [Panoramica di Analisi Servizi multimediali di Azure](./legacy-components.md).

> [!NOTE]
> Alcuni processori di contenuti multimediali di analisi verranno ritirati. Per informazioni sulle date di ritiro, vedere l'argomento relativo ai [componenti legacy](legacy-components.md).

|Nome processore di contenuti multimediali|Stato|Region
|---|---|---|
|Rilevamento multimediale volti di Azure|Anteprima|Tutti|
|Azure Media Indexer|GA|Tutti|
|Rilevatore multimediale di movimento Azure|Anteprima|Tutti|
|Riconoscimento ottico dei caratteri multimediale di Azure|Anteprima|Tutti|
|Azure Media Redactor|GA|Tutti|
|Anteprime video multimediali di Azure|Anteprima|Tutti|

## <a name="protection"></a>Protezione

Servizi multimediali di Microsoft Azure consente di proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Per altre informazioni, vedere l'argomento relativo alla [protezione dei contenuti di AMS](media-services-content-protection-overview.md).

|Crittografia|Stato|Regioni|
|---|---|---| 
|Archiviazione|GA|Tutti|
|Chiavi AES-128|GA|Tutti|
|Fairplay|GA|Tutti|
|PlayReady|GA|Tutti|
|Widevine|GA|Tutti tranne la Germania, il governo federale e la Cina.

> [!NOTE]
> Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="reserved-units-rus"></a>Unità riservate

Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account.

Disponibile in tutte le aree.

## <a name="reserved-unit-ru-type"></a>Tipo di unità riservata

Un account di servizi multimediali è associato a un tipo di unità riservata che determina la velocità con cui vengono completate le attività di elaborazione di contenuti multimediali. È possibile scegliere tra i tipi di unità riservata seguenti: S1, S2 o S3.

|Nome tipo di unità riservata|Stato|Regioni
|---|---|---|
|S1|GA|Tutti|
|S2|GA|Tutti tranne Brasile meridionale e India occidentale|
|S3|GA|Tutti tranne India occidentale|

## <a name="next-steps"></a>Passaggi successivi

[Eseguire la migrazione a Servizi multimediali v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]