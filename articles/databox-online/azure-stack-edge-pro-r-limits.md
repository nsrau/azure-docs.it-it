---
title: Limiti R Pro di Azure Stack Edge | Microsoft Docs
description: Descrive i limiti di sistema e le dimensioni consigliate per il Azure Stack Edge Pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467633"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Limiti R Pro di Azure Stack Edge

Prendere in considerazione questi limiti quando si distribuisce e si utilizza la soluzione Azure Stack Edge Pro.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Limiti del servizio R di Azure Stack Edge Pro

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Limiti per i dispositivi R di Azure Stack Edge Pro

La tabella seguente descrive i limiti per il dispositivo R Azure Stack Edge Pro.

| Descrizione | valore |
|---|---|
|No. di file per dispositivo |100 milioni |
|No. di condivisioni per contenitore |1 |
|Numero massimo. degli endpoint di condivisione e degli endpoint REST per ogni dispositivo| 24 |
|Numero massimo. di account di archiviazione a livelli per dispositivo| 24|
|Dimensione massima dei file scritti in una condivisione| 5 TB |
|Numero massimo di gruppi di risorse per dispositivo| 800 |

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limiti delle dimensioni dell'oggetto e dell'account di archiviazione di Azure

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack Edge Pro](azure-stack-edge-pro-r-deploy-prep.md)
