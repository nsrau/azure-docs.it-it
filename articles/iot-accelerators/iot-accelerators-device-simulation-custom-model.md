---
title: Configurare un modello dispositivo personalizzato - Azure | Microsoft Docs
description: Questo articolo descrive come configurare un modello dispositivo personalizzato nell'acceleratore di soluzione di Simulazione dispositivi.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967492"
---
# <a name="device-models"></a>Modelli di dispositivo

Quando si configura una simulazione è possibile scegliere di usare un modello di dispositivo esistente con un set di sensori o creare un modello di dispositivo personalizzato con i sensori simulati scelti. I sensori personalizzati consentono di modellare in modo più preciso i dispositivi reali.

## <a name="pre-configured-device-models"></a>Modelli di dispositivo preconfigurati

L'acceleratore di soluzione di Simulazione dispositivi offre tre modelli di dispositivo preconfigurati: refrigeratori, ascensori e veicoli.

I modelli di dispositivi preconfigurati includono più sensori con comportamenti avanzati definiti in un file JavaScript. Non è possibile configurare questi comportamenti dell'interfaccia utente Web.

La tabella seguente mostra le configurazioni possibili per ogni modello di dispositivo preconfigurato:

| Modello del dispositivo  | Sensore           | Unità  |
| ------------- | ---------------- | ----- |
| Chiller (Refrigeratore)       | umidità         | %     |
|               | pressure         | psig  |
|               | temperatura      | F     |
| Elevator (Montacarichi)      | Piano            |       |
|               | Vibrazione        | MM    |
|               | Temperatura      | F     |
| Truck (Veicolo)         | Latitude         |       |
|               | Longitudine        |       |
|               | velocità            | mph   |
|               | temperatura carico | F     |

## <a name="custom-device-models"></a>Modelli di dispositivi personalizzati

I modelli di dispositivo personalizzati consentono di definire sensori che rappresentano più fedelmente dispositivi specifici. Un dispositivo personalizzato può avere fino a 10 sensori personalizzati.

Quando si seleziona il tipo di modello di dispositivo personalizzato, è possibile aggiungere nuovi sensori facendo clic su **+Add sensor** (+Aggiungi sensore).

[![Configurare un sensore](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

Per i sensori personalizzati sono disponibili le proprietà seguenti:

| Campo     | DESCRIZIONE |
| --------- | ----------- |
| Sensor Name (Nome del sensore) | Nome descrittivo per il sensore, ad esempio **temperatura** o **velocità**.  |
| Comportamento  | I comportamenti consentono di ottenere dati di telemetria variabili da un messaggio all'altro, per simulare dati reali. Il comportamento **Increment** (Incremento) consente di aumentare il valore di un'unità per ogni messaggio inviato a partire dal valore minimo. Quando viene raggiunto il valore massimo, il processo riparte dal valore minimo. Il comportamento **Decrement** (Decremento) è uguale a **increment** (Incremento), ma alla rovescia. Il comportamento **Random** (Casuale) genera un valore casuale compreso tra i valori minimo e massimo. |
| Min Value (Valore minimo) | Numero più basso dell'intervallo di valori accettabili. |
| Max Value (Valore massimo) | Numero più alto dell'intervallo di valori accettabili. |
| Unità      | Unità di misura per il sensore, ad esempio °C o km/h. |

## <a name="next-steps"></a>Passaggi successivi

In questa Guida dettagliata, si è appreso come configurare un modello di dispositivo personalizzato per una simulazione. Successivamente, sarà possibile esplorare alcuni degli altri [acceleratori di soluzioni IoT](about-iot-accelerators.md).