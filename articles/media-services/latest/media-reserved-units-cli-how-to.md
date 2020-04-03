---
title: Utilizzo dell’interfaccia della riga di comando per ridimensionare Media Served Unit - Azure | Microsoft Docs
description: In questo argomento viene spiegato come usare l’interfaccia della riga di comando per ridimensionare l'elaborazione di servizi multimediali con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 79f2df0f94b212ea45c01c825b23f1dbb4cc40db
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582279"
---
# <a name="scaling-media-processing"></a>Ridimensionamento dell'elaborazione di contenuti multimediali

Servizi multimediali di Azure consente di ridimensionare l'elaborazione di contenuti multimediali nell'account gestendo le Media Reserved Unit (MRU). Gli RM determinano la velocità di elaborazione delle attività di elaborazione multimediale. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**. 

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con unità riservate. Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione quando un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Scelta tra i tipi diversi di unità riservata

La tabella seguente consente di scegliere tra diverse velocità di codifica. Fornisce inoltre alcuni casi di riferimento sul [un video che è possibile scaricare](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) per eseguire i propri test:

|Tipo di unità richiesta|Scenario|Risultati di esempio per il [video di 7 min con risoluzione 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codifica con velocità in bit singola. <br/>File SD o con risoluzione inferiore, non dipendenti dall'ora, a basso costo.|La codifica al singolo file MP4 con risoluzione SD a bitrate utilizzando "H264 Single Bitrate SD 16x9" richiede circa 7 minuti.|
| **S2**|Codifica con velocità in bit singola e multipla.<br/>Uso normale per la codifica SD e HD.|La codifica con il preset "H264 Single Bitrate 720p" richiede circa 6 minuti.<br/><br/>La codifica con il preset "H264 Multiple Bitrate 720p" richiede circa 12 minuti.|
| **S3**|Codifica con velocità in bit singola e multipla.<br/>Video Full HD e con risoluzione 4K. Codifica dipendente dall'ora con completamento più rapido.|La codifica con il preset "H264 Single Bitrate 1080p" richiede circa 3 minuti.<br/><br/>La codifica con "Codec video H.264 a bitrate multiplo con risoluzione 1080p" preconfigurato richiede circa 8 minuti.|

## <a name="considerations"></a>Considerazioni

* Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, il tipo di unità di S3 è fortemente consigliato.
* Se si usa il pool condiviso, vale a dire senza unità riservate, le attività di codifica hanno le stesse prestazioni di quando si usano le unità riservate S1. Non c'è tuttavia alcun limite superiore al tempo che le attività possono trascorrere nello stato in coda e in qualsiasi momento può essere eseguita al massimo una sola attività.

Nella parte restante dell'articolo viene illustrato come utilizzare [l'interfaccia](https://aka.ms/ams-v3-cli-ref) della riga di comando di Servizi multimediali v3 per ridimensionare le RM.

> [!NOTE]
> Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 MRU S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Prerequisiti 

[Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Ridimensionare le Media Reserved Unit con l'interfaccia della riga di comando

Eseguire il comando `mru`.

Il comando [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) seguente imposta le Media Reserved Unit sull'account "amsaccount" usando i parametri **count** e **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fatturazione

L'addebito viene effettuato in base al numero di minuti di provisioning delle unità riservate ai supporti nel tuo account. Ciò si verifica indipendentemente dal fatto che siano presenti processi in esecuzione nel tuo account. Per una spiegazione dettagliata, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Passaggio successivo

[Analizzare i video](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Vedere anche

* [Quote e limiti](limits-quotas-constraints.md)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
