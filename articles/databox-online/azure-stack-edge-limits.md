---
title: Limiti per Pro Azure Stack Edge | Microsoft Docs
description: Informazioni sui limiti e le dimensioni consigliate per la distribuzione e l'uso di Azure Stack Edge Pro, inclusi i limiti del servizio, i limiti dei dispositivi e i limiti di archiviazione.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91992757"
---
# <a name="azure-stack-edge-pro-limits"></a>Limiti di Azure Stack Edge Pro

Considerare questi limiti quando si distribuisce e si utilizza la soluzione di Microsoft Azure Stack Edge Pro. 

## <a name="azure-stack-edge-service-limits"></a>Limiti dei servizi di Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Limiti del dispositivo Azure Stack Edge

La tabella seguente descrive i limiti per il dispositivo Azure Stack Edge Pro. 

La tabella seguente descrive i limiti per il dispositivo Azure Stack Edge.

| Descrizione | Valore |
|---|---|
|No. di file per dispositivo |100 milioni |
|No. di condivisioni per contenitore |1 |
|Numero massimo. degli endpoint di condivisione e degli endpoint REST per ogni dispositivo| 24 |
|Numero massimo. di account di archiviazione a livelli per dispositivo| 24|
|Dimensione massima dei file scritti in una condivisione| 5 TB |
|Numero massimo di gruppi di risorse per dispositivo| 800 |

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limiti delle dimensioni dell'oggetto e dell'account di archiviazione di Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
