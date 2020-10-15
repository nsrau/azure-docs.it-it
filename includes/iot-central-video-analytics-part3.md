---
title: includere file
description: includere file
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876665"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Creare il dispositivo gateway Azure IoT Edge

L'applicazione Analisi video - rilevamento movimento e oggetti include un modello di dispositivo **LVA Edge Object Detector** e un modello di dispositivo **LVA Edge Motion Detection**. In questa sezione si creerà un modello di dispositivo gateway usando il manifesto della distribuzione e si aggiungerà il dispositivo gateway all'applicazione IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Creare un modello di dispositivo per LVA Edge Gateway

Per importare il manifesto della distribuzione e creare il modello di dispositivo **LVA Edge Gateway**:

1. Nell'applicazione IoT Central passare a **Modelli di dispositivo** e selezionare **+ Nuovo**.

1. Nella pagina **Selezionare il tipo di modello** selezionare il riquadro **Azure IoT Edge**. Selezionare quindi **Next: Personalizza**.

1. Nella pagina **Carica un manifesto della distribuzione di Azure IoT Edge** immettere *LVA Edge Gateway* come nome del modello e selezionare **Dispositivo gateway con dispositivi downstream**.

    Per il momento, non cercare il manifesto della distribuzione. Se lo si fa, la distribuzione guidata si aspetta un'interfaccia per ogni modulo, mentre è sufficiente esporre l'interfaccia per **LvaEdgeGatewayModule**. Il manifesto verrà caricato in un passaggio successivo.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Non caricare il manifesto della distribuzione":::

    Selezionare **Avanti: Review** (Avanti: Rivedi).

1. Nella pagina **Rivedi** selezionare **Crea**.

### <a name="import-the-device-capability-model"></a>Importare il modello di funzionalità di dispositivo

Il modello di dispositivo deve includere un modello di funzionalità di dispositivo. Nella pagina **LVA Edge Gateway** selezionare il riquadro **Importa modello di funzionalità**. Passare alla cartella *lva-configuration* creata in precedenza e selezionare il file *LvaEdgeGatewayDcm.json*.

Il modello di dispositivo **LVA Edge Gateway** include ora **LVA Edge Gateway Module** e tre interfacce: **Device information**, **LVA Edge Gateway Settings** e **LVA Edge Gateway Interface**.
