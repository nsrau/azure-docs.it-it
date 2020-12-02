---
title: Limiti di mini R per Azure Stack Edge | Microsoft Docs
description: Vengono descritti i limiti di sistema e le dimensioni consigliate per il mini R Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467853"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Limiti di mini R per Azure Stack Edge


Considerare questi limiti quando si distribuisce e si utilizza la soluzione mini R di Azure Stack Edge.

## <a name="azure-stack-edge-service-limits"></a>Limiti dei servizi di Azure Stack Edge

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Limiti per i dispositivi Mini R di Azure Stack Edge

La tabella seguente descrive i limiti per il dispositivo Mini R Azure Stack Edge.

| Descrizione | Limite|
|---|---:|
|No. di file per dispositivo | 100 milioni <!--check with devs-->|
|No. di condivisioni per contenitore | 1|
|Numero massimo. degli endpoint di condivisione e degli endpoint REST per ogni dispositivo| 24 |
|Numero massimo. di account di archiviazione a livelli per dispositivo| 24|
|Dimensione massima dei file scritti in una condivisione| 500 GB|
|Numero massimo di gruppi di risorse per dispositivo| 800|

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limiti delle dimensioni dell'oggetto e dell'account di archiviazione di Azure

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
