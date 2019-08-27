---
title: Generare una stringa di connessione del dispositivo per IoT Central di Azure | Microsoft Docs
description: Come sviluppatore di dispositivi, come si genera una stringa di connessione per il dispositivo che deve connettersi a un'applicazione IoT Central?
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019786"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Generare una stringa di connessione del dispositivo per connettersi a un'applicazione IoT Central di Azure

Questo articolo descrive come lo sviluppatore del dispositivo deve generare una stringa di connessione per il dispositivo che deve connettersi a un'applicazione IoT Central. La procedura descritta in questo articolo illustra come connettere rapidamente un singolo dispositivo usando una firma di accesso condiviso (SAS). Questo approccio è utile quando si sperimentano IoT Central o i dispositivi di testing. Per approcci alternativi da usare in un ambiente di produzione, vedere [connettività dei dispositivi in Azure IOT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Un'applicazione Azure IoT Central. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
- Un computer di sviluppo in cui è installato [node. js](https://nodejs.org/) versione 8.0.0 o successiva. Per controllare la versione, è possibile eseguire `node --version` nella riga di comando. Node.js è disponibile per un'ampia gamma di sistemi operativi.

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

I passaggi seguenti descrivono come ottenere le informazioni necessarie per generare una stringa di connessione SAS per un dispositivo:

1. Nella **Device Explorer**trovare il dispositivo reale che si vuole connettere all'applicazione:

    ![Selezionare un dispositivo reale](media/howto-generate-connection-string/real-devices.png)

1. Nella pagina **dispositivo** selezionare **Connetti**:

    ![Selezionare Connetti](media/howto-generate-connection-string/connect.png)

1. Prendere nota dei dettagli della connessione, dell' **ID ambito**, dell' **ID dispositivo**e della **chiave primaria del dispositivo**da usare nei passaggi seguenti:

    ![Dettagli connessione](media/howto-generate-connection-string/device-connect.png)

    È possibile copiare i valori da questa pagina per salvarli.

## <a name="generate-the-connection-string"></a>Generare la stringa di connessione

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata generata una stringa di connessione per un dispositivo reale per connettersi all'applicazione IoT Central di Azure, ecco i passaggi successivi suggeriti:

* [Preparare e connettere un dispositivo DevKit (C)](howto-connect-devkit.md)
* [Preparare e connettere un'applicazione Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Preparare e connettere un'applicazione Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Preparare e connettere un dispositivo Windows 10 IoT Core (C#)](howto-connect-windowsiotcore.md)
* [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs.md)