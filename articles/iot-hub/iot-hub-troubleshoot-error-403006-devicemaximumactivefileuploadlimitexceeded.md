---
title: Risoluzione dei problemi dell'errore dell'hub IoT di Azure 403006 DeviceMaximumActiveFileUploadLimitExceededTroubleshooting Azure IoT Hub error 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Comprendere come risolvere l'errore 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960841"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

In questo articolo vengono descritte le cause e le soluzioni per **403006 DeviceMaximumActiveFileUploadLimitExceeded** errori.

## <a name="symptoms"></a>Sintomi

La richiesta di caricamento del file ha esito negativo con il codice di errore **403006** e un messaggio "Numero di richieste di caricamento file attive non può superare 10".

## <a name="cause"></a>Causa

Ogni client del dispositivo è limitato a [10 caricamenti di file simultanei.](./iot-hub-devguide-quotas-throttling.md#other-limits) 

Puoi facilmente superare il limite se il dispositivo non invia una notifica all'hub IoT al termine del caricamento dei file. Questo problema è in genere causato da una rete lato dispositivo inaffidabile.

## <a name="solution"></a>Soluzione

Assicurarsi che il dispositivo possa notificare immediatamente il completamento del caricamento del [file dell'hub IoT](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Provare quindi a [ridurre il TTL](iot-hub-configure-file-upload.md)del token SAS per la configurazione del caricamento dei file .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui caricamenti di file, vedere [Caricare file con l'hub IoT](./iot-hub-devguide-file-upload.md) e [Configurare i caricamenti di file dell'hub IoT usando il portale](./iot-hub-configure-file-upload.md)di Azure.