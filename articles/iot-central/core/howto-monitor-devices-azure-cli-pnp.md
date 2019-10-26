---
title: Monitorare la connettività dei dispositivi con Azure IoT Central Explorer
description: Monitorare i messaggi dei dispositivi e osservare le modifiche del dispositivo gemello usando l'interfaccia della riga di comando di IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 09/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 2b1c4c64fc02df67f38e36194072efd5db3b8e38
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953174"
---
# <a name="monitor-device-connectivity-using-azure-cli-preview-features"></a>Monitorare la connettività dei dispositivi usando l'interfaccia della riga di comando di Azure

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

Usare l'estensione dell'interfaccia della riga di comando di Azure per visualizzare i messaggi inviati dai dispositivi a IoT Central e osservare le modifiche nel dispositivo gemello. È possibile usare questo strumento per eseguire il debug e osservare la connettività dei dispositivi e diagnosticare i problemi dei messaggi del dispositivo che non raggiungono il cloud o i dispositivi che non rispondono alle modifiche del dispositivo gemello

[Vedere le informazioni di riferimento sulle estensioni dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Prerequisiti

+ L'interfaccia della riga di comando di Azure è installata ed è 2.0.7 o versione successiva. Controllare la versione dell'interfaccia della riga di comando di Azure eseguendo `az --version`. Informazioni su come installare e aggiornare dalla [documentazione dell'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli) della riga di comando di Azure
+ Un account aziendale o dell'Istituto di istruzione in Azure, aggiunto come utente in un'applicazione IoT Central.

## <a name="install-the-iot-central-extension"></a>Installare l'estensione IoT Central

Eseguire il seguente comando dalla riga di comando per l'installazione:

```cmd/sh
az extension add --name azure-cli-iot-ext
```

Controllare la versione dell'estensione eseguendo 
```cmd/sh
az --version
```
Si noterà che l'estensione Azure-CLI-Internet è 0.8.1 o successiva. In caso contrario, eseguire
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>Utilizzo dell'estensione

Nelle sezioni seguenti vengono descritti i comandi e le opzioni comuni che è possibile utilizzare quando si esegue `az iot central`. Per visualizzare il set completo di comandi e opzioni, passare `--help` al `az iot central` o a uno dei relativi sottocomandi.

### <a name="login"></a>Login

Per iniziare, accedere all'interfaccia della riga di comando di Azure. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Ottenere l'ID applicazione dell'app IoT Central
In **Impostazioni amministrazione/applicazione**copiare l' **ID applicazione**. Questa operazione verrà usata nei passaggi successivi.

### <a name="monitor-messages"></a>Monitoraggio dei messaggi
Monitorare i messaggi inviati all'app IoT Central dai dispositivi. Sono incluse tutte le intestazioni e le annotazioni.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Visualizzare le proprietà del dispositivo
Visualizzare le proprietà del dispositivo di lettura e lettura/scrittura correnti per un determinato dispositivo.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare Esplora IoT Central, il passaggio successivo suggerito consiste nell'esplorazione della [gestione dei dispositivi IOT Central](howto-manage-devices.md).
