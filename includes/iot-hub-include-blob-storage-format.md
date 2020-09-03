---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89042860"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> È possibile scrivere i dati nell'archiviazione BLOB in formato [Apache Avro](https://avro.apache.org/), che è quello predefinito, oppure in formato JSON (anteprima). 
>    
> La funzionalità di codifica del formato JSON è in anteprima in tutte le aree in cui è disponibile l'hub IoT, a eccezione degli Stati Uniti orientali, degli Stati Uniti occidentali e dell'Europa occidentale. Il formato di codifica può essere impostato solo al momento della configurazione dell'endpoint di archiviazione BLOB. Non è possibile modificare il formato di un endpoint già configurato. Quando si usa la codifica JSON è necessario impostare contentType su JSON e contentEncoding su UTF-8 nelle proprietà di sistema del messaggio. 
>
> Per informazioni più dettagliate sull'uso di un endpoint di archiviazione BLOB, vedere le [indicazioni sul routing all'archiviazione](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).
>