---
title: Risoluzione dei problemi dell'errore dell'hub IoT di Azure 403002 IoTHubQuotaExceededTroubleshooting Azure IoT Hub error 403002 IoTHubQuotaExceeded
description: Informazioni su come correggere l'errore 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961114"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

In questo articolo vengono descritte le cause e le soluzioni per gli errori **403002 IoTHubQuotaExceeded.**

## <a name="symptoms"></a>Sintomi

Tutte le richieste all'hub IoT hanno esito negativo con l'errore **403002 IoTHubQuotaExceeded**. Nel portale di Azure l'elenco dei dispositivi hub IoT non viene caricato.

## <a name="cause"></a>Causa

La quota giornaliera dei messaggi per l'hub IoT viene superata. 

## <a name="solution"></a>Soluzione

[Aggiornare o aumentare il numero di unità nell'hub IoT](iot-hub-upgrade.md) o attendere il giorno UTC successivo per l'aggiornamento della quota giornaliera.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come vengono conteggiate le operazioni per la quota, ad esempio query gemelle e metodi diretti, vedere [Informazioni sui prezzi dell'hub IoT](iot-hub-devguide-pricing.md#charges-per-operation)
* Per impostare il monitoraggio per l'utilizzo giornaliero delle quote, impostare un avviso con la metrica *Numero totale di messaggi utilizzati.* Per istruzioni dettagliate, vedere [Configurare metriche e avvisi con l'hub IoT](tutorial-use-metrics-and-diags.md#set-up-metrics)