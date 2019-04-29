---
title: Limiti di Azure Data Box Gateway | Microsoft Docs
description: Descrive i limiti di sistema e le dimensioni consigliate per Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755233"
---
# <a name="azure-data-box-gateway-limits"></a>Limiti del Gateway di finestra di dati Azure

Quando si distribuisce e si usa la soluzione Microsoft Azure Data Box Gateway, tenere presenti i limiti seguenti. 


## <a name="data-box-gateway-service-limits"></a>Limiti del servizio Data Box Gateway

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limiti del dispositivo Data Box Gateway

La tabella seguente illustra i limiti del dispositivo Data Box Gateway.

| DESCRIZIONE | Value |
|---|---|
| No. di file per dispositivo |100 milioni <br> Il limite è di circa 25 milioni di file per ogni 2 TB di spazio su disco con un limite massimo di 100 milioni |
| No. di condivisioni per dispositivo |24 |
| No. di condivisioni per ogni contenitore di archiviazione di Azure |1 |
|Dimensione massima dei file scritti in una condivisione|Per un dispositivo virtuale di 2 TB, le dimensioni massime del file sono di 500 GB. <br> La dimensione massima del file aumenta con la dimensione del disco dati nel rapporto indicato in precedenza fino a raggiungere un massimo di 5 TB. |

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limiti delle dimensioni dell'oggetto e dell'account di archiviazione di Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
