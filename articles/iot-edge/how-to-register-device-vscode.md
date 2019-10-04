---
title: Registrare un nuovo dispositivo da Visual Studio Code - Azure IoT Edge | Microsoft Docs
description: Usare Visual Studio Code per creare un nuovo dispositivo IoT Edge nell'hub IoT di Azure e recuperare la stringa di connessione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8fce104d48acc3a562599c65eb15cb0a66657b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495298"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Registrare un nuovo dispositivo Azure IoT Edge da Visual Studio Code

Per poter usare i dispositivi IoT con Azure IoT Edge, è necessario registrarli nell'hub IoT. Dopo avere registrato un dispositivo, si riceve una stringa di connessione che può essere utilizzata per configurare il dispositivo per i carichi di lavoro di IoT Edge.

Questo articolo illustra come registrare un nuovo dispositivo IoT Edge usando Visual Studio Code (VS Code). Esistono diversi modi per eseguire la maggior parte delle operazioni in VS Code. Questo articolo si usa Esplora risorse, ma è anche possibile usare il riquadro comandi per eseguire i passaggi.

## <a name="prerequisites"></a>Prerequisiti

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

Per eseguire operazioni con l'hub IoT, è possibile usare le estensioni Azure IoT per Visual Studio Code. Per il funzionamento di queste operazioni, è necessario accedere al proprio account Azure e selezionare l'hub IoT.

1. In Visual Studio Code aprire la **finestra di esplorazione**.

1. Nella parte inferiore della finestra di esplorazione, espandere la **IoT Hub di Azure** sezione.

   ![Espandere la sezione Azure IoT Hub Devices (Dispositivi dell'Hub IoT di Azure).](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

1. Fare clic su di **...**  nella **IoT Hub di Azure** intestazione di sezione. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.

1. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).

1. Se non sono connessi al proprio account Azure, seguire le istruzioni per eseguire questa operazione.

1. Selezionare la sottoscrizione di Azure.

1. Selezionare l'hub IoT.

## <a name="create-a-device"></a>Creare un dispositivo

1. Nella finestra di esplorazione di VS Code espandere la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure).

1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.

1. Selezionare **Create IoT Edge Device** (Crea dispositivo IoT Edge).

1. Nella casella di testo che si apre specificare un ID per il dispositivo.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

## <a name="view-all-devices"></a>Visualizzare tutti i dispositivi

Vengono elencati tutti i dispositivi che si connettono all'hub IoT nel **IoT Hub di Azure** sezione di soluzioni di Visual Studio Code. Dispositivi IoT Edge si distinguono dai dispositivi non appartengono ai bordi con un'icona diversa e il fatto che il **$edgeAgent** e **$edgeHub** moduli vengono distribuiti in ogni dispositivo IoT Edge.

   ![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Recuperare la stringa di connessione

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Pulsante destro del mouse sull'ID del dispositivo nel **IoT Hub di Azure** sezione.

1. Selezionare **Copy Device Connection String** (Copia stringa di connessione dispositivo).

   La stringa di connessione verrà copiata negli Appunti.

È anche possibile selezionare **Get Device Info** (Ottieni info dispositivo) dal menu di scelta rapida per visualizzare nella finestra di output tutte le informazioni relative al dispositivo, inclusa la stringa di connessione.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire moduli in un dispositivo con Visual Studio Code](how-to-deploy-modules-vscode.md).
