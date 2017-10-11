---
title: Gestione dei dispositivi Azure IoT con iothub-explorer | Microsoft Docs
description: "Usare lo strumento dell'interfaccia della riga di comando iothub-explorer per la gestione di dispositivi dell'hub IoT di Azure, con i metodi diretti e le opzioni di gestione delle proprietà desiderate nei dispositivi gemelli."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: gestione dispositivi iot azure, gestione dispositivi hub iot azure, gestione dispositivi iot, gestione dispositivi hub iot
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.openlocfilehash: 5b7a5057bdfb5920fbb5759bed1f5561cfa1d7e0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Usare iothub-explorer per la gestione di dispositivi hub IoT di Azure

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) è uno strumento dell'interfaccia della riga di comando eseguito in un computer host che consente di gestire le identità dei dispositivi nel registro dell'hub IoT. Include opzioni di gestione che consentono di eseguire varie attività.

| Opzione di gestione          | Attività                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Metodi diretti             | Far eseguire un'attività al dispositivo, quale l'avvio o l'arresto dell'invio di messaggi o il riavvio del dispositivo.                                        |
| Proprietà desiderate del dispositivo gemello    | Impostare determinati stati di un dispositivo, ad esempio impostare la luce verde per un LED o impostare l'intervallo di invio dei dati di telemetria su 30 minuti.         |
| Proprietà segnalate del dispositivo gemello   | Ottenere lo stato restituito da un dispositivo. Ad esempio, il dispositivo segnala che il LED sta lampeggiando.                                    |
| Tag dei dispositivi gemelli                  | Archiviare i metadati specifici del dispositivo nel cloud, ad esempio il percorso di distribuzione di un distributore automatico.                         |
| Messaggi da cloud a dispositivo   | Inviare notifiche a un dispositivo. Ad esempio "Oggi è molto probabile che piova. Meglio non dimenticare l'ombrello".              |
| Query del dispositivo gemello        | Eseguire query su tutti i dispositivi gemelli per identificare quelli che si trovano in una determinata condizione, ad esempio i dispositivi disponibili per l'uso. |

Per altre informazioni sulle differenze e sull'uso di queste opzioni, vedere [Device-to-cloud communication guidance](iot-hub-devguide-d2c-guidance.md) (Indicazioni sulla comunicazione da dispositivo a cloud) e [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo).

> [!NOTE]
> I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette. Per altre informazioni sui dispositivi gemelli, vedere [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Si apprende l'uso di iothub-explorer con varie opzioni di gestione presenti nel proprio computer di sviluppo.

## <a name="what-you-do"></a>Operazioni da fare

Eseguire iothub-explorer con diverse opzioni di gestione.

## <a name="what-you-need"></a>Elementi necessari

- Completare l'esercitazione [Configurare il dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) che prevede i requisiti seguenti:
  - Una sottoscrizione di Azure attiva.
  - Un hub IoT di Azure nella sottoscrizione.
  - Un'applicazione client che invia messaggi all'hub IoT di Azure.
- Verificare che il dispositivo sia in esecuzione con l'applicazione client durante questa esercitazione.
- iothub-explorer, [Installare iothub-explorer](https://github.com/azure/iothub-explorer) sul computer di sviluppo.

## <a name="connect-to-your-iot-hub"></a>Accedere all'hub IoT

Accedere all'hub IoT eseguendo il comando seguente:

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>Usare iothub-explorer con i metodi diretti

Chiamare il metodo `start` nell'app per dispositivi per inviare messaggi all'hub IoT eseguendo il comando seguente:

```bash
iothub-explorer device-method <your device Id> start
```

Chiamare il metodo `stop` nell'app per dispositivi per interrompere l'invio di messaggi all'hub IoT eseguendo il comando seguente:

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>Usare iothub-explorer con le proprietà desiderate del dispositivo gemello

Impostare l'intervallo di proprietà desiderato su 3000 eseguendo il comando seguente:

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

Questa proprietà può essere letta dal dispositivo.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>Usare iothub-explorer con le proprietà segnalate del dispositivo gemello

Ottenere le proprietà segnalate del dispositivo eseguendo il comando seguente:

```bash
iothub-explorer get-twin <your device id>
```

Una delle proprietà è $metadata.$lastUpdated, che visualizza l'ora di invio o ricezione dell'ultimo messaggio nel dispositivo.

## <a name="use-iothub-explorer-with-twins-tags"></a>Usare iothub-explorer con i tag del dispositivo gemello

Visualizzare i tag e le proprietà del dispositivo eseguendo il comando seguente:

```bash
iothub-explorer get-twin <your device id>
```

Aggiungere il campo role = temperature&humidity al dispositivo eseguendo il comando seguente:

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"

```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>Usare iothub-explorer con i messaggi da cloud a dispositivo

Inviare un messaggio "Hello World" al dispositivo eseguendo il comando seguente:

```bash
iothub-explorer send <device-id> "Hello World"
```

Per uno scenario reale dell'uso di questo comando, vedere [Usare iothub-explorer per inviare e ricevere messaggi tra il dispositivo e l'hub IoT](iot-hub-explorer-cloud-device-messaging.md).

## <a name="use-iothub-explorer-with-device-twins-queries"></a>Usare iothub-explorer con le query del dispositivo gemello

Eseguire una query su tutti i dispositivi con il tag role = 'temperature&humidity' eseguendo il comando seguente:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Eseguire una query su tutti i dispositivi meno quelli con il tag role = 'temperature&humidity' eseguendo il comando seguente:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Passaggi successivi

Si è appreso l'uso di iothub-explorer con diverse opzioni di gestione.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
