---
title: Asset in Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo descrive le caratteristiche degli asset e come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>Asset

Un'entità **Asset** contiene file digitali, tra cui video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, con i relativi metadati. Una volta caricati in un asset, i file digitali possono essere usati nei flussi di lavoro di codifica e trasmissione di Servizi multimediali.

Un asset viene mappato a un contenitore BLOB nell'[account di archiviazione di Azure](storage-account-concept.md) e i file contenuti nell'asset vengono archiviati come BLOB in blocchi in tale contenitore. È possibile interagire con i file di un'entità Asset nei contenitori tramite i client dell'SDK di archiviazione.

Servizi multimediali di Azure supporta i livelli BLOB quando l'account usa l'archiviazione Utilizzo generico v2 (GPv2). Con GPv2, è possibile spostare i file in un'archiviazione offline sicura o ad accesso sporadico. L'archiviazione offline sicura è adatta all'archiviazione di file in formato intermedio quando non sono più necessari (ad esempio, dopo la codifica).

In Servizi multimediali v3, l'input del processo può essere creato a partire da asset o da URL HTTP(S). Per creare un asset che può essere usato come input per il processo, vedere [Create a job input from a local file](job-input-from-local-file-how-to.md) (Creare un input del processo da un file locale).

Inoltre, consultare gli articoli sugli [account di archiviazione in Servizi multimediali](storage-account-concept.md) e su [trasformazioni e processi](transform-concept.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
