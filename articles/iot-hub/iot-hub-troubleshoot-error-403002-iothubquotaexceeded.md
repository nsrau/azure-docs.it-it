---
title: Risoluzione dei problemi dell'hub Azure Internet Error 403002 IoTHubQuotaExceeded
description: Informazioni su come correggere l'errore 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76961114"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Questo articolo descrive le cause e le soluzioni per gli errori di **403002 IoTHubQuotaExceeded** .

## <a name="symptoms"></a>Sintomi

Tutte le richieste all'hub Internet non riescono con l'errore  **403002 IoTHubQuotaExceeded**. In portale di Azure, l'elenco dei dispositivi dell'hub Internet non viene caricato.

## <a name="cause"></a>Causa

Viene superata la quota di messaggi giornaliera per l'hub Internet. 

## <a name="solution"></a>Soluzione

[Aggiornare o aumentare il numero di unità nell'hub](iot-hub-upgrade.md) Internet o attendere il giorno UTC successivo per l'aggiornamento della quota giornaliera.

## <a name="next-steps"></a>Passaggi successivi

* Per comprendere il modo in cui le operazioni vengono conteggiate per la quota, ad esempio query gemelle e metodi diretti, vedere [informazioni sui prezzi dell'hub](iot-hub-devguide-pricing.md#charges-per-operation)
* Per configurare il monitoraggio per l'utilizzo delle quote giornaliere, impostare un avviso con il *numero totale di messaggi usati*per la metrica. Per istruzioni dettagliate, vedere la pagina relativa alla [configurazione di metriche e avvisi con l'hub](tutorial-use-metrics-and-diags.md#set-up-metrics) Internet.