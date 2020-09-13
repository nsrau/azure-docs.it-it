---
title: Connettere un dispositivo Azure Sphere in Azure IoT Central | Microsoft Docs
description: Informazioni su come connettere un dispositivo Azure Sphere (DevKit) a un'applicazione Azure IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.custom: device-developer
ms.openlocfilehash: 34b767a2cc48c94fdb5c2db032321b9254bf4ce2
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017694"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo Azure Sphere all'applicazione Azure IoT Central

*Questo articolo si applica agli sviluppatori di dispositivi.*

Questo articolo descrive come connettere un dispositivo Azure Sphere (DevKit) a un'applicazione Azure IoT Central.

Azure Sphere è una piattaforma applicativa protetta e di alto livello con funzionalità di comunicazione e di protezione integrate per i dispositivi connessi a Internet. Include un'unità microcontroller (MCU) crossover protetta e connessa, un sistema operativo personalizzato basato su Linux di alto livello e un servizio di sicurezza basato sul cloud che garantisce una sicurezza continua e rinnovabile. Per altre informazioni, vedere [Informazioni su Azure Sphere](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere).

I [kit di sviluppo di Azure Sphere](https://azure.microsoft.com/services/azure-sphere/get-started/) forniscono tutto il necessario per avviare la creazione di prototipi e lo sviluppo di applicazioni di Azure Sphere. Azure IoT Central con Azure Sphere consente uno stack end-to-end per una soluzione IoT. Azure Sphere fornisce il supporto per il dispositivo e IoT Central come piattaforma di applicazioni IoT gestite senza codice.

Questa procedura dettagliata consente di:

- Creare un dispositivo Azure Sphere in IoT Central usando il modello di dispositivo Azure Sphere DevKit dalla libreria.
- Preparare il dispositivo Azure Sphere DevKit per Azure IoT.
- Connettere Azure Sphere DevKit ad Azure IoT Central.
- Visualizzare i dati di telemetria dal dispositivo in IoT Central.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessarie le risorse seguenti:

- Un'applicazione Azure IoT Central.
- Visual Studio 2019, versione 16.4 o successive.
- Un [kit di sviluppo di Azure Sphere per MT3620 da Seeed Studios](https://docs.microsoft.com/azure-sphere/hardware/mt3620-reference-board-design).

> [!NOTE]
> Se non si dispone di un dispositivo fisico, dopo il primo passaggio, passare all'ultima sezione per provare un dispositivo simulato.

## <a name="create-the-device-in-iot-central"></a>Creare il dispositivo in IoT Central

Per creare un dispositivo Azure Sphere in IoT Central:

1. Nell'applicazione Azure IoT Central selezionare la scheda **Modelli di dispositivo** e **+ Nuovo**. Nella sezione **Use a featured device template** (Usa un modello di dispositivo in primo piano) selezionare **Azure Sphere Sample Device** (Dispositivo di esempio Azure Sphere).

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Modello di dispositivo per Azure Sphere DevKit":::

1. Nel modello di dispositivo modificare la visualizzazione denominata **Overview** (Panoramica) per visualizzare **Temperature** (Temperatura) e **Button Press** (Pressione pulsante).

1. Selezionare il tipo di visualizzazione **Modifica dei dati del dispositivo**, per aggiungere un'altra visualizzazione che mostri la proprietà di lettura/scrittura **Status LED** (LED di stato). Trascinare la proprietà **Status LED** (LED di stato) sul rettangolo vuoto punteggiato sul lato destro del modulo. Selezionare **Salva**.

## <a name="prepare-the-device"></a>Preparare il dispositivo

Prima di poter connettere il dispositivo Azure Sphere DevKit a IoT Central, è necessario [configurare il dispositivo e l'ambiente di sviluppo](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT).

## <a name="connect-the-device"></a>Connettere il dispositivo

Per consentire all'esempio di connettersi a IoT Central, è necessario [configurare un'applicazione Azure IoT Central e quindi modificare il manifesto dell'applicazione di esempio](https://aka.ms/iotcentral-sphere-git-readme).

## <a name="view-the-telemetry-from-the-device"></a>Visualizzare i dati di telemetria dal dispositivo

Quando il dispositivo è connesso a IoT Central, è possibile visualizzare i dati di telemetria nel dashboard.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Dashboard per Azure Sphere DevKit":::

## <a name="create-a-simulated-device"></a>Creare un dispositivo simulato

Se non si dispone di un dispositivo Azure Sphere DevKit fisico, è possibile creare un dispositivo simulato per provare l'applicazione Azure IoT Central.

Per creare un dispositivo simulato:

- Selezionare **Dispositivi > Azure IoT Sphere**.
- Selezionare **+ Nuovo**.
- Immettere un **ID dispositivo** univoco e un **nome descrittivo del dispositivo**.
- Abilitare l'impostazione **Simulato**.
- Selezionare **Create** (Crea).

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:

- Leggere [Connettività dei dispositivi in Azure IoT Central](./concepts-get-connected.md)
- Vedere [Monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure](./howto-monitor-devices-azure-cli.md)
