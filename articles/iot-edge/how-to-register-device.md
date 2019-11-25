---
title: Register a new Azure IoT Edge device | Microsoft Docs
description: Usare l'estensione IoT per l'interfaccia della riga di comando di Azure per registrare un nuovo dispositivo IoT Edge e recuperare la stringa di connessione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/21/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 793ddcb9f218248c396e10f23201dfe905545ceb
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456844"
---
# <a name="register-an-azure-iot-edge-device"></a>Register an Azure IoT Edge device

Before you can use your IoT devices with Azure IoT Edge, you must register them with your IoT hub. Once a device is registered, you can retrieve a connection string to set up your device for IoT Edge workloads.

You have the choice of registering by using one of the following tools:

* [Azure portal](https://portal.azure.com) provides a graphical user interface to create, view, and manage Azure resources.
* [Visual Studio Code](https://code.visualstudio.com/) is a source-code editor. Azure IoT extensions make it easy to manage IoT resources from the same tool where you're developing IoT solutions.
* [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is a command-line tool for managing Azure resources. Its reusable commands are helpful for automating tasks.

## <a name="register-in-the-azure-portal"></a>Register in the Azure portal

You can perform all registration tasks in the Azure portal.

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisites for the Azure portal

A free or standard [IoT hub](../iot-hub/iot-hub-create-through-portal.md) in your Azure subscription.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Create an IoT Edge device in the Azure portal

In your IoT Hub in the Azure portal, IoT Edge devices are created and managed separately from IOT devices that are not edge enabled.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
2. In the left pane, select **IoT Edge** from the menu.
3. Selezionare **Aggiungi un dispositivo IoT Edge**.
4. Specificare un ID descrittivo del dispositivo. Use the default settings to auto-generate authentication keys and connect the new device to your hub.
5. Selezionare **Salva**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>View IoT Edge devices in the Azure portal

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Retrieve the connection string in the Azure portal

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. From the **IoT Edge** page in the portal, click on the device ID from the list of IoT Edge devices.
2. Copiare il valore di **Stringa di connessione (chiave primaria)** o **Stringa di connessione (chiave secondaria)** .

## <a name="register-with-visual-studio-code"></a>Register with Visual Studio Code

Esistono diversi modi per eseguire la maggior parte delle operazioni in VS Code. This article uses the Explorer, but you can also use the Command Palette to run the steps.

### <a name="prerequisites-for-visual-studio-code"></a>Prerequisites for Visual Studio Code

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

You can use the Azure IoT extensions for Visual Studio Code to perform operations with your IoT Hub. For these operations to work, you need to sign in to your Azure account and select your IoT Hub.

1. In Visual Studio Code aprire la **finestra di esplorazione**.
1. At the bottom of the Explorer, expand the **Azure IoT Hub** section.

   ![Espandere la sezione Azure IoT Hub Devices (Dispositivi dell'Hub IoT di Azure).](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Click on the **...** in the **Azure IoT Hub** section header. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).
1. If you aren't signed in to your Azure account, follow the prompts to do so.
1. Selezionare la sottoscrizione di Azure.
1. Selezionare l'hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Create an IoT Edge device with Visual Studio Code

1. Nella finestra di esplorazione di VS Code espandere la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure).
1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Selezionare **Create IoT Edge Device** (Crea dispositivo IoT Edge).
1. Nella casella di testo che si apre specificare un ID per il dispositivo.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>View IoT Edge devices with Visual Studio Code

All the devices that connect to your IoT hub are listed in the **Azure IoT Hub** section of the Visual Studio Code Explorer. IoT Edge devices are distinguishable from non-Edge devices with a different icon, and the fact that the **$edgeAgent** and **$edgeHub** modules are deployed to each IoT Edge device.

![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Retrieve the connection string with Visual Studio Code

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Right-click on the ID of your device in the **Azure IoT Hub** section.
1. Selezionare **Copy Device Connection String** (Copia stringa di connessione dispositivo).

   La stringa di connessione verrà copiata negli Appunti.

È anche possibile selezionare **Get Device Info** (Ottieni info dispositivo) dal menu di scelta rapida per visualizzare nella finestra di output tutte le informazioni relative al dispositivo, inclusa la stringa di connessione.

## <a name="register-with-the-azure-cli"></a>Register with the Azure CLI

The [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is an open-source cross platform command-line tool for managing Azure resources such as IoT Edge. Consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio Device Provisioning e gli hub collegati predefiniti. La nuova estensione IoT arricchisce l'interfaccia della riga di comando di Azure con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisites for the Azure CLI

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure.
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. La versione dell'interfaccia della riga di comando di Azure deve essere 2.0.24 o successiva. Usare `az --version` per la convalida. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.
* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Create an IoT Edge device with the Azure CLI

Use the [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) command to create a new device identity in your IoT hub. ad esempio:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Questo comando include tre parametri:

* **device-id**: specificare un nome descrittivo univoco per l'hub IoT.
* **hub-name**: specificare il nome dell'hub IoT.
* **edge-enabled**: questo parametro dichiara che il dispositivo è destinato all'uso con IoT Edge.

   ![az iot hub device-identity create output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>View IoT Edge devices with the Azure CLI

Use the [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) command to view all devices in your IoT hub. ad esempio:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Retrieve the connection string with the Azure CLI

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT. Use the [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) command to return the connection string for a single device:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Per il parametro `device-id` viene fatta distinzione tra maiuscole e minuscole. Non copiare le virgolette alle estremità della stringa di connessione.

## <a name="next-steps"></a>Passaggi successivi

Now that you have a device identity registered in your IoT hub, you're ready to install the IoT Edge runtime on your devices. Install the runtime according to the device's operating system:

* [Install Azure IoT Edge on Windows](how-to-install-iot-edge-windows.md)
* [Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)
