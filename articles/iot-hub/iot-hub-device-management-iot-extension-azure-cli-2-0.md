---
title: Gestione dei dispositivi Azure IoT con l'estensione IoT dell'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: "Usare l'estensione IoT dell'interfaccia della riga di comando di Azure 2.0 per la gestione dei dispositivi dell'hub IoT di Azure, con opzioni di gestione come i metodi diretti e le proprietà desiderate in dispositivi gemelli."
services: iot-hub
documentationcenter: 
author: chrissie926
manager: timlt
tags: 
keywords: gestione dispositivi iot azure, gestione dispositivi hub iot azure, gestione dispositivi iot, gestione dispositivi hub iot
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 07b9f14048b6618863efd5bd8eb8bcc8f52ec735
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Usare l'estensione IoT per l'interfaccia della riga di comando di Azure 2.0 per la gestione dei dispositivi dell'hub IoT di Azure

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[L'estensione IoT dell'interfaccia della riga di comando di Azure 2.0](https://github.com/Azure/azure-iot-cli-extension) è una nuova estensione IoT open source che amplia le funzionalità dell'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure?view=azure-cli-latest). Questa interfaccia include comandi per l'interazione con Azure Resource Manager e gli endpoint di gestione. Ad esempio, è possibile usarla per creare una macchina virtuale o un hub IoT di Azure. Un'estensione dell'interfaccia della riga di comando di Azure consente a un servizio di Azure di aumentare le potenzialità dell'interfaccia e all'utente di accedere a funzionalità aggiuntive specifiche del servizio. L'estensione IoT offre agli sviluppatori IoT la possibilità di accedere dalla riga di comando a tutte le funzionalità dell'hub IoT, di IoT Edge e del servizio Device Provisioning in hub IoT.

| Opzione di gestione          | Attività                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Metodi diretti             | Far eseguire un'attività al dispositivo, quale l'avvio o l'arresto dell'invio di messaggi o il riavvio del dispositivo.                                        |
| Proprietà desiderate del dispositivo gemello    | Impostare determinati stati di un dispositivo, ad esempio impostare la luce verde per un LED o impostare l'intervallo di invio dei dati di telemetria su 30 minuti.         |
| Proprietà segnalate del dispositivo gemello   | Ottenere lo stato restituito da un dispositivo. Ad esempio, il dispositivo segnala che il LED sta lampeggiando.                                    |
| Tag dei dispositivi gemelli                  | Archiviare i metadati specifici del dispositivo nel cloud, ad esempio il percorso di distribuzione di un distributore automatico.                         |
| Query del dispositivo gemello        | Eseguire query su tutti i dispositivi gemelli per identificare quelli che si trovano in una determinata condizione, ad esempio i dispositivi disponibili per l'uso. |

Per altre informazioni sulle differenze e sull'uso di queste opzioni, vedere [Device-to-cloud communication guidance](iot-hub-devguide-d2c-guidance.md) (Indicazioni sulla comunicazione da dispositivo a cloud) e [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo).

> [!NOTE]
> I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette. Per altre informazioni sui dispositivi gemelli, vedere [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Si apprende l'uso dell'estensione IoT dell'interfaccia della riga di comando di Azure 2.0 con varie opzioni di gestione presenti nel proprio computer di sviluppo.

## <a name="what-you-do"></a>Operazioni da fare

Eseguire l'interfaccia della riga di comando di Azure 2.0 e la relativa estensione IoT con varie opzioni di gestione.

## <a name="what-you-need"></a>Elementi necessari

- Completare l'esercitazione [Configurare il dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) che prevede i requisiti seguenti:
  - Una sottoscrizione di Azure attiva.
  - Un hub IoT di Azure nella sottoscrizione.
  - Un'applicazione client che invia messaggi ad Azure IoT hub.

- Verificare che il dispositivo sia in esecuzione con l'applicazione client durante questa esercitazione.

- [Python 2.7x o Python 3.x](https://www.python.org/downloads/)

- Installare l'interfaccia della riga di comando di Azure 2.0. Un modo semplice per eseguire l'installazione in Windows è quello di scaricare e installare il file [MSI](https://aka.ms/InstallAzureCliWindows). Per installare l'interfaccia della riga di comando di Azure 2.0 nell'ambiente in uso, è possibile anche seguire le istruzioni di installazione disponibili in [Microsoft Docs](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). La versione dell'interfaccia della riga di comando di Azure 2.0 deve essere 2.0.24 o successiva. Usare il comando `az –version` per verificare. 

- Installare l'estensione IoT. Il modo più semplice è quello di eseguire `az extension add --name azure-cli-iot-ext`. Il [file Leggimi dell'estensione IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) illustra diverse modalità per installare l'estensione.


## <a name="log-in-to-your-azure-account"></a>Accedere all'account Azure

Accedere al proprio account di Azure usando il comando seguente:

```bash
az login
```

## <a name="direct-methods"></a>Metodi diretti

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Proprietà desiderate nel dispositivo gemello

Impostare l'intervallo di proprietà desiderato su 3000 eseguendo il comando seguente:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

Questa proprietà può essere letta dal dispositivo.

## <a name="device-twin-reported-properties"></a>Proprietà segnalate del dispositivo gemello

Ottenere le proprietà segnalate del dispositivo eseguendo il comando seguente:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.reported.interval = 3000
```

Una delle proprietà è $metadata.$lastUpdated, che visualizza l'ora di invio o ricezione dell'ultimo messaggio nel dispositivo.

## <a name="device-twin-tags"></a>Tag del dispositivo gemello

Visualizzare i tag e le proprietà del dispositivo eseguendo il comando seguente:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Aggiungere il campo role = temperature&humidity al dispositivo eseguendo il comando seguente:

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Query del dispositivo gemello

Eseguire una query su tutti i dispositivi con il tag role = 'temperature&humidity' eseguendo il comando seguente:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Eseguire una query su tutti i dispositivi meno quelli con il tag role = 'temperature&humidity' eseguendo il comando seguente:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Passaggi successivi

È stato appreso come monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo tra il dispositivo IoT e l'hub IoT di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]