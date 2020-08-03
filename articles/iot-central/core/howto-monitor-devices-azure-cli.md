---
title: Monitorare la connettività dei dispositivi con Azure IoT Central Explorer
description: Monitorare i messaggi dei dispositivi e osservare le modifiche del dispositivo gemello usando l'interfaccia della riga di comando di IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli
services: iot-central
manager: corywink
ms.openlocfilehash: b9cf8b862f77af833016d6aabb0ccae7bea8e24a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497427"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure

*Questo argomento si applica agli sviluppatori di dispositivi e ai generatori di soluzioni.*

Usare l'estensione dell'interfaccia della riga di comando di Azure per visualizzare i messaggi inviati dai dispositivi a IoT Central e osservare le modifiche nel dispositivo gemello. È possibile usare questo strumento per eseguire il debug e osservare la connettività dei dispositivi e diagnosticare i problemi dei messaggi del dispositivo che non raggiungono il cloud o i dispositivi che non rispondono alle modifiche del dispositivo gemello

[Vedere le informazioni di riferimento sulle estensioni dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Prerequisiti

+ L'interfaccia della riga di comando di Azure è installata ed è 2.0.7 o versione successiva. Controllare la versione dell'interfaccia della riga di comando di Azure eseguendo `az --version` . Informazioni su come installare e aggiornare dalla [documentazione dell'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli) della riga di comando di Azure
+ Un account aziendale o dell'Istituto di istruzione in Azure, aggiunto come utente in un'applicazione IoT Central.

## <a name="install-the-iot-central-extension"></a>Installare l'estensione IoT Central

Eseguire il seguente comando dalla riga di comando per l'installazione:

```azurecli
az extension add --name azure-iot
```

Controllare la versione dell'estensione eseguendo:

```azurecli
az --version
```

Si noterà che l'estensione Azure-Internet è 0.8.1 o versione successiva. In caso contrario, eseguire:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Uso dell'estensione

Nelle sezioni seguenti vengono descritti i comandi e le opzioni comuni che è possibile utilizzare quando si esegue `az iot central` . Per visualizzare il set completo di comandi e opzioni, passare `--help` a `az iot central` o a uno dei relativi sottocomandi.

### <a name="login"></a>Accedi

Per iniziare, accedere all'interfaccia della riga di comando di Azure. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Ottenere l'ID applicazione dell'app IoT Central
In **Impostazioni amministrazione/applicazione**copiare l' **ID applicazione**. Questo valore verrà usato nei passaggi successivi.

### <a name="monitor-messages"></a>Monitoraggio dei messaggi
Monitorare i messaggi inviati all'app IoT Central dai dispositivi. L'output include tutte le intestazioni e le annotazioni.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Visualizzare le proprietà del dispositivo
Visualizzare le proprietà del dispositivo di lettura e lettura/scrittura correnti per un determinato dispositivo.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Passaggi successivi

Per gli sviluppatori di dispositivi, un passaggio successivo suggerito consiste nel leggere informazioni sulla [connettività dei dispositivi in Azure IOT Central](./concepts-get-connected.md).
