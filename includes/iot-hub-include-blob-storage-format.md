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
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612095"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> È possibile scrivere i dati nell'archiviazione BLOB in formato [Apache Avro](https://avro.apache.org/), che è quello predefinito, oppure in formato JSON (anteprima). 
>    
> La funzionalità di codifica del formato JSON è in anteprima in tutte le aree in cui è disponibile l'hub IoT, a eccezione degli Stati Uniti orientali, degli Stati Uniti occidentali e dell'Europa occidentale. Il formato di codifica può essere impostato solo al momento della configurazione dell'endpoint di archiviazione BLOB. Non è possibile modificare il formato di un endpoint già configurato. Quando si usa la codifica JSON è necessario impostare contentType su JSON e contentEncoding su UTF-8 nelle proprietà di sistema del messaggio. 
>
> Per informazioni più dettagliate sull'uso di un endpoint di archiviazione BLOB, vedere le [indicazioni sul routing all'archiviazione](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>