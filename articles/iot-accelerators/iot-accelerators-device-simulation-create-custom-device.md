---
title: Creare un dispositivo simulato personalizzato - Azure | Microsoft Docs
description: In questa esercitazione si usa Simulazione dispositivi per creare un dispositivo simulato personalizzato da usare nelle simulazioni.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 302b863e7ad7d6df286adf53342356f279ab92d2
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50756789"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Esercitazione: Creare un dispositivo simulato personalizzato

In questa esercitazione si usa Simulazione dispositivi per creare un dispositivo simulato personalizzato da usare nelle simulazioni. Per iniziare a usare Simulazione dispositivi, è possibile usare uno dei dispositivi simulati di esempio inclusi. È anche possibile creare dispositivi simulati personalizzati come descritto in questo articolo. Per altre opzioni di personalizzazione, vedere [Creare un modello di dispositivo avanzato](iot-accelerators-device-simulation-advanced-device.md).

In questa esercitazione:

>[!div class="checklist"]
> * Visualizzare un elenco di modelli di dispositivi simulati
> * Creare un dispositivo simulato personalizzato
> * Clonare un modello di dispositivo
> * Eliminare un modello di dispositivo

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita di Simulazione dispositivi nella sottoscrizione di Azure.

Se la soluzione Simulazione dispositivi non è stata ancora distribuita, è necessario completare la guida introduttiva [Distribuire ed eseguire una soluzione di simulazione dei dispositivi in Azure](quickstart-device-simulation-deploy.md).

## <a name="open-device-simulation"></a>Aprire Simulazione dispositivi

Per eseguire Simulazione dispositivi nel browser, passare innanzitutto al sito [Acceleratori di soluzione Microsoft Azure IoT](https://www.azureiotsolutions.com).

Potrebbe essere necessario accedere con le credenziali della sottoscrizione di Azure.

Fare quindi clic su **Avvio** nel riquadro per la soluzione Simulazione dispositivi distribuita nella guida introduttiva [Distribuire ed eseguire una soluzione di simulazione dei dispositivi in Azure](quickstart-device-simulation-deploy.md).

## <a name="view-your-device-models"></a>Visualizzare i modelli di dispositivo

Selezionare **Device models** (Modelli di dispositivo) nella barra dei menu. Nella pagina **Device models** (Modelli di dispositivo) vengono elencati tutti i modelli di dispositivo disponibili in questa istanza di Simulazione dispositivi:

![Modelli di dispositivo](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Creare un modello di dispositivo

Fare clic su **+ Add Device Models** (+ Aggiungi modelli di dispositivo) nell'angolo in alto a destra della pagina:

![Aggiungere un modello di dispositivo](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

In questa esercitazione viene creato un frigorifero simulato che invia i dati relativi a temperatura e umidità.

Compilare il modulo con le informazioni seguenti:

| Impostazione             | Valore                                                |
| ------------------- | ---------------------------------------------------- |
| Device model name (Nome modello di dispositivo)   | Refrigerator                                         |
| Descrizione modello   | Frigorifero con sensori di temperatura e umidità |
| Version             | 1.0                                                  |

> [!NOTE]
> Il nome del modello di dispositivo deve essere univoco.

Fare clic su **+ Add data point** (+ Aggiungi punto dati) per aggiungere i punti dati relativi a temperatura e umidità con i valori seguenti:

| Data Point (Punto dati)          | Comportamento        | Min Value (Valore minimo) | Max Value (Valore massimo) | Unità |
| ------------------- | --------------- | --------- | --------- | ---- |
| Temperatura         | Random (Casuale)          | -50       | 100       | F    |
| Umidità            | Random (Casuale)          | 0         | 100       | %    |

Fare clic su **Save** (Salva) per salvare il modello di dispositivo.

![Creare il modello di dispositivo](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

Il frigorifero è ora incluso nell'elenco dei modelli di dispositivo. Potrebbe essere necessario fare clic su **Next** (Avanti) per passare a un'altra pagina per visualizzare il frigorifero.

## <a name="clone-a-device-model"></a>Clonare un modello di dispositivo

La clonazione di un modello di dispositivo consente di creare una copia di un modello di dispositivo esistente. È quindi possibile modificare tale copia per soddisfare esigenze specifiche. La clonazione fa risparmiare tempo quando è necessario creare modelli di dispositivi simili.

Per clonare un modello di dispositivo, selezionare la casella accanto al modello e quindi fare clic su **Clone** (Clona) nella barra delle azioni:

![Eliminare un modello di dispositivo](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Eliminare un modello di dispositivo

È possibile eliminare qualsiasi modello di dispositivo personalizzato. Per eliminare un modello di dispositivo, selezionare la casella accanto al modello e quindi fare clic su **Delete** (Elimina) nella barra delle azioni:

![Eliminare un modello di dispositivo](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come creare, clonare ed eliminare modelli di dispositivi personalizzati. Per altre informazioni sui modelli di dispositivi, vedere l'articolo seguente:

> [!div class="nextstepaction"]
> [Creare un modello di dispositivo avanzato](iot-accelerators-device-simulation-advanced-device.md)