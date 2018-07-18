---
title: Registrare un nuovo dispositivo Azure IoT Edge (VS Code) | Microsoft Docs
description: Usare Visual Studio Code per creare un nuovo dispositivo IoT Edge nell'hub IoT di Azure
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7902461f58df1b4fe0c3ed3b577f668fe8be4cc2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034961"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Registrare un nuovo dispositivo Azure IoT Edge da Visual Studio Code

Per poter usare i dispositivi IoT con Azure IoT Edge, è necessario registrarli con l'hub IoT. Dopo la registrazione di un dispositivo, si riceve una stringa di connessione che può essere usata per configurare il dispositivo per i carichi di lavoro Edge. 

Questo articolo illustra come registrare un nuovo dispositivo IoT Edge usando Visual Studio Code (VS Code). Esistono diversi modi per eseguire la maggior parte delle operazioni in VS Code. In questo articolo viene usata la finestra di esplorazione, ma è anche possibile usare il riquadro comandi per eseguire la maggior parte dei passaggi. 

## <a name="prerequisites"></a>Prerequisiti

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Estensione Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) per Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

Per eseguire operazioni con l'hub IoT, è possibile usare le estensioni Azure IoT per Visual Studio Code. Per il corretto funzionamento di queste operazioni è necessario accedere all'account Azure e selezionare l'hub IoT in cui si sta lavorando.

1. In Visual Studio Code aprire la **finestra di esplorazione**.

2. Nella parte inferiore della finestra di esplorazione espandere la sezione **Azure IoT Hub dispositivi** (Dispositivi hub IoT di Azure). 

   ![Espandere i dispositivi dell'hub IoT di Azure](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. Fare clic sui puntini di sospensione (**...**) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione. 

4. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).

5. Se ancora non lo si è fatto, eseguire l'accesso all'account di Azure seguendo le istruzioni. 

6. Selezionare la sottoscrizione di Azure. 

7. Selezionare l'hub IoT. 

## <a name="create-a-device"></a>Creare un dispositivo

1. Nella finestra di esplorazione di VS Code espandere la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). 

2. Fare clic sui puntini di sospensione (**...**) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione. 

3. Selezionare **Create IoT Edge Device** (Crea dispositivo IoT Edge). 

4. Nella casella di testo che si apre specificare un ID per il dispositivo. 

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT. 

## <a name="view-all-devices"></a>Visualizzare tutti i dispositivi

Tutti i dispositivi connessi all'hub IoT sono elencati nella sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure) della finestra di esplorazione di Visual Studio Code. I dispositivi IoT Edge si distinguono dai dispositivi non Edge per la presenza di un'icona di tipo diverso e per il fatto che possono essere espansi in modo da mostrare i moduli distribuiti in ogni dispositivo. 

   ![Visualizzare dispositivi in VS Code](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Recuperare la stringa di connessione

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Fare clic con il pulsante destro del mouse sull'ID del dispositivo nella sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). 
2. Selezionare **Copy Device Connection String** (Copia stringa di connessione dispositivo).

   La stringa di connessione verrà copiata negli Appunti. 

È anche possibile selezionare **Get Device Info** (Ottieni info dispositivo) dal menu di scelta rapida per visualizzare nella finestra di output tutte le informazioni relative al dispositivo, inclusa la stringa di connessione. 


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire moduli in un dispositivo con Visual Studio Code](how-to-deploy-modules-vscode.md).
