---
title: Monitorare la connettività dei dispositivi con Azure IoT Central Explorer
description: Monitorare i messaggi dei dispositivi e osservare le modifiche del dispositivo gemello usando l'interfaccia della riga di comando di IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 09209c21fe1b2b115c1ba6d6e00fcd0ee59a9393
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365417"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

Usare l'estensione IoT dell'interfaccia della riga di comando di Azure per visualizzare i messaggi inviati dai dispositivi a IoT Central e osservare le modifiche nel dispositivo gemello. È possibile usare questo strumento per eseguire il debug e osservare la connettività dei dispositivi e diagnosticare i problemi dei messaggi del dispositivo che non raggiungono il cloud o i dispositivi che non rispondono alle modifiche gemelle.

[Visita le informazioni di riferimento sulle estensioni dell'interfaccia della riga di comando di Azure per altri dettagli](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Prerequisiti

+ L'interfaccia della riga di comando di Azure è installata e versione 2.0.7 o successiva. Controllare la versione dell'interfaccia `az --version`della riga di comando di Azure eseguendo . Informazioni su come installare e aggiornare dalla [documentazione dell'interfaccia della riga](https://docs.microsoft.com/cli/azure/install-azure-cli) di comando di AzureLearn how to install and update from the Azure CLI docs
+ Un account aziendale o dell'istituto di istruzione in Azure, aggiunto come utente in un'applicazione IoT Central.A work or school account in Azure, added as a user in an IoT Central application.

## <a name="install-the-iot-central-extension"></a>Installare l'estensione IoT Central

Eseguire il seguente comando dalla riga di comando per l'installazione:

```azurecli
az extension add --name azure-iot
```

Controllare la versione dell'estensione eseguendo:

```azurecli
az --version
```

L'estensione azure-iot dovrebbe essere 0.8.1 o superiore. In caso contrario, eseguire:If it is not, run:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Uso dell'estensione

Nelle sezioni seguenti vengono descritti i comandi e `az iot central`le opzioni comuni che è possibile utilizzare quando si esegue . Per visualizzare il set completo di `--help` `az iot central` comandi e opzioni, passare a o uno dei relativi sottocomandi.

### <a name="login"></a>Login

Iniziare accedendo all'interfaccia della riga di comando di Azure.Start by signing into the Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Ottenere l'ID applicazione dell'app IoT CentralGet the Application ID of your IoT Central app
In **Amministrazione/Impostazioni applicazione**copiare l'ID **applicazione**. Questo valore viene utilizzato nei passaggi successivi.

### <a name="monitor-messages"></a>Monitoraggio dei messaggi
Monitora i messaggi inviati all'app IoT Central dai tuoi dispositivi. L'output include tutte le intestazioni e le annotazioni.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Visualizzare le proprietà del dispositivo
Visualizzare le proprietà correnti del dispositivo di lettura e lettura/scrittura per un determinato dispositivo.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come usare Esplora centrale IoT, il passaggio successivo consigliato consiste nell'esplorare [la gestione dei dispositivi IoT Central](howto-manage-devices.md).
