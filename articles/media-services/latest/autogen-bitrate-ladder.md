---
title: Codificare i video con Codificatore standard in Servizi multimediali - Azure | Microsoft Docs
description: Questo argomento descrive come usare il Codificatore standard in Servizi multimediali per codificare un video di input con una tabella di coppie velocità in bit-risoluzione generata automaticamente, in base alla velocità in bit e alla risoluzione di input.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c25c32f35adc1c017f0f4c012c82bd7e0af8d452
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733318"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Codificare con una tabella di coppie velocità in bit-risoluzione generata automaticamente

## <a name="overview"></a>Panoramica

Questo articolo spiega come usare il Codificatore standard in Servizi multimediali per codificare un video di input in una tabella di coppie velocità in bit-risoluzione generata automaticamente in base alla velocità in bit e alla risoluzione di input. Questa impostazione di codifica integrata (o set di impostazioni) non supererà mai la risoluzione e la velocità in bit di input. Ad esempio, se l'input è 720p a 3 Mbps, l'output resta al massimo a 720p e inizia a una velocità inferiore a 3 Mbps.

### <a name="encoding-for-streaming"></a>Codifica per lo streaming

Se si usa il set di impostazioni **AdaptiveStreaming** in un oggetto **Transform**, si ottiene un output adatto al recapito tramite protocolli di streaming quali HLS e DASH. Quando si usa questo set di impostazioni, il servizio determina in modo intelligente il numero di livelli video da generare e a quale risoluzione e velocità in bit. L'output contiene i file MP4 in cui l'audio con codifica AAC e il video con codifica H.264 non sono di tipo Interleaved.

Per un esempio d'uso di questo set di impostazioni, vedere [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Output

Questa sezione illustra tre esempi dei livelli video di output prodotti dal codificatore di Servizi multimediali in seguito alla codifica con il set di impostazioni **AdaptiveStreaming**. In tutti i casi, l'output contiene un file MP4 solo audio con audio stereo con codifica a 128 kbps.

### <a name="example-1"></a>Esempio 1
L'origine con altezza "1080" e una frequenza frame "29.970" produce 6 livelli video:

|Livello|Altezza:|Larghezza|Velocità in bit (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Esempio 2
L'origine con altezza "720" e una frequenza frame "23.970" produce 5 livelli video:

|Livello|Altezza:|Larghezza|Velocità in bit (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Esempio 3
L'origine con altezza "360" e una frequenza frame "29.970" produce 3 livelli video:

|Livello|Altezza:|Larghezza|Velocità in bit (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
