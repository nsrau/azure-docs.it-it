---
title: Registrare un nuovo dispositivo Azure IoT Edge Documenti Microsoft
description: Usare l'estensione IoT per l'interfaccia della riga di comando di Azure per registrare un nuovo dispositivo IoT Edge e recuperare la stringa di connessione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235715"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrare un dispositivo Azure IoT EdgeRegister an Azure IoT Edge device

Prima di poter usare i dispositivi IoT con Azure IoT Edge, è necessario registrarli con l'hub IoT.Before you can use your IoT devices with Azure IoT Edge, you must register them with your IoT hub. Dopo aver registrato un dispositivo, è possibile recuperare una stringa di connessione per configurare il dispositivo per i carichi di lavoro di IoT Edge.Once a device is registered, you can retrieve a connection string to set up your device for IoT Edge workloads.

È possibile scegliere di registrarsi utilizzando uno dei seguenti strumenti:

* [Registrare un dispositivo nel portale](#register-in-the-azure-portal) di Azure se si preferisce un'interfaccia utente grafica per creare, visualizzare e gestire le risorse di Azure.Register a device in the Azure portal if you prefer a graphical user interface to create, view, and manage Azure resources.
* [Registrare un dispositivo con Visual Studio Code](#register-with-visual-studio-code) se si preferisce gestire le risorse IoT di Azure nella stessa posizione in cui si sviluppano le soluzioni IoT.Register a device with Visual Studio Code if you prefer to manage Azure IoT resources in the same place where you develop your IoT solutions.
* [Registrare un dispositivo con l'interfaccia della riga di](#register-with-the-azure-cli) comando di Azure se si preferiscono gli strumenti della riga di comando per la gestione delle risorse di Azure o si intende automatizzare le attività.

## <a name="register-in-the-azure-portal"></a>Eseguire la registrazione nel portale di AzureRegister in the Azure portal

È possibile eseguire tutte le attività di registrazione nel portale di Azure.You can perform all registration tasks in the Azure portal.

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisiti per il portale di AzurePrerequisites for the Azure portal

Un hub [IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito o standard nella sottoscrizione di Azure.A free or standard IoT hub in your Azure subscription.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Creare un dispositivo IoT Edge nel portale di AzureCreate an IoT Edge device in the Azure portal

Nell'hub IoT nel portale di Azure, i dispositivi IoT Edge vengono creati e gestiti separatamente dai dispositivi IOT non abilitati per i bordi.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
2. Nel riquadro sinistro selezionare **Edge IoT** dal menu.
3. Selezionare **Aggiungi un dispositivo IoT Edge**.
4. Specificare un ID descrittivo del dispositivo. Usare le impostazioni predefinite per generare automaticamente le chiavi di autenticazione e connettere il nuovo dispositivo all'hub.
5. Selezionare **Salva**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Visualizzare i dispositivi IoT Edge nel portale di AzureView IoT Edge devices in the Azure portal

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recuperare la stringa di connessione nel portale di AzureRetrieve the connection string in the Azure portal

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Nella pagina **IoT Edge** del portale fare clic sull'ID del dispositivo nell'elenco dei dispositivi IoT Edge.
2. Copiare il valore di **Stringa di connessione primaria** o Stringa di connessione **secondaria**.

## <a name="register-with-visual-studio-code"></a>Registrati con codice di Visual Studio

Esistono diversi modi per eseguire la maggior parte delle operazioni in VS Code. In questo articolo viene utilizzata la finestra di esplorazione, ma è anche possibile utilizzare la tavolozza dei comandi per eseguire la procedura.

### <a name="prerequisites-for-visual-studio-code"></a>Prerequisiti per il codice di Visual StudioPrerequisites for Visual Studio Code

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* Strumenti IoT di Azure per il codice di Visual [StudioAzure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) for Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

È possibile usare le estensioni IoT di Azure per Visual Studio Code per eseguire operazioni con l'hub IoT.You can use the Azure IoT extensions for Visual Studio Code to perform operations with your IoT Hub. Affinché queste operazioni funzionino, è necessario accedere all'account Azure e selezionare l'hub IoT.For these operations to work, you need to sign in to your Azure account and select your IoT Hub.

1. In Visual Studio Code aprire la **finestra di esplorazione**.
1. Nella parte inferiore di Esplora risorse espandere la sezione Hub IoT di Azure.At the bottom of the Explorer, expand the **Azure IoT Hub** section.

   ![Espandere la sezione Azure IoT Hub Devices (Dispositivi dell'Hub IoT di Azure).](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Fare clic su ... nell'intestazione della sezione Hub IoT di Azure.Click on **the ...** in the **Azure IoT Hub** section header. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).
1. Se non è stato effettuato l'accesso all'account Azure, seguire le istruzioni visualizzate.
1. Selezionare la sottoscrizione di Azure.
1. Selezionare l'hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Creare un dispositivo IoT Edge con il codice di Visual StudioCreate an IoT Edge device with Visual Studio Code

1. In Esplora codice VS espandere la sezione Dispositivi hub IoT di Azure.In the VS Code Explorer, expand the **Azure IoT Hub Devices** section.
1. Fare clic sui puntini di sospensione (**...**) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Selezionare **Create IoT Edge Device** (Crea dispositivo IoT Edge).
1. Nella casella di testo che si apre specificare un ID per il dispositivo.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visualizzare i dispositivi IoT Edge con il codice di Visual StudioView IoT Edge devices with Visual Studio Code

Tutti i dispositivi che si connettono all'hub IoT sono elencati nella sezione **Hub IoT** di Azure di Visual Studio Code Explorer. I dispositivi IoT Edge sono distinguibili dai dispositivi non perimetrali con un'icona diversa e dal fatto che i moduli **$edgeAgent** e **$edgeHub** vengono distribuiti in ogni dispositivo IoT Edge.

![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperare la stringa di connessione con visual Studio Code

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Fare clic con il pulsante destro del mouse sull'ID del dispositivo nella sezione Hub IoT di Azure.Right-click on the ID of your device in the **Azure IoT Hub** section.
1. Selezionare **Copy Device Connection String** (Copia stringa di connessione dispositivo).

   La stringa di connessione verrà copiata negli Appunti.

È anche possibile selezionare **Get Device Info** (Ottieni info dispositivo) dal menu di scelta rapida per visualizzare nella finestra di output tutte le informazioni relative al dispositivo, inclusa la stringa di connessione.

## <a name="register-with-the-azure-cli"></a>Eseguire la registrazione con l'interfaccia della riga di comando di AzureRegister with the Azure

[L'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) di Azure è un'interfaccia di comando open source per la gestione delle risorse di Azure, ad esempio IoT Edge.The Azure CLI is an open-source cross platform command-line tool for managing Azure resources such as IoT Edge. Consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio di provisioning di dispositivi e gli hub collegati predefiniti. L'estensione IoT arricchisce l'interfaccia della riga di comando di Azure con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di AzurePrerequisites for the Azure CLI

* Un hub IoT nella sottoscrizione di Azure.An [IoT hub](../iot-hub/iot-hub-create-using-cli.md) in your Azure subscription.
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. Come minimo, la versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o superiore. Usare il comando `az --version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.
* [L'estensione IoT per l'interfaccia della riga di comando](https://github.com/Azure/azure-iot-cli-extension)di Azure.

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Creare un dispositivo IoT Edge con l'interfaccia della riga di comando di AzureCreate an IoT Edge device with the Azure CLI

Usare il comando az iot hub device-identity create per creare una nuova identità del dispositivo nell'hub IoT.Use the [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) command to create a new device identity in your IoT hub. Ad esempio:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Questo comando include tre parametri:

* **device-id**: specificare un nome descrittivo univoco per l'hub IoT.
* **hub-name**: specificare il nome dell'hub IoT.
* **edge-enabled**: questo parametro dichiara che il dispositivo è destinato all'uso con IoT Edge.

   ![az iot hub device-identity create output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visualizzare i dispositivi IoT Edge con l'interfaccia della riga di comando di AzureView IoT Edge devices with the Azure CLI

Usare il comando az iot hub device-identity list per visualizzare tutti i dispositivi nell'hub IoT.Use the [az iot hub device-identity list command](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) to view all devices in your IoT hub. Ad esempio:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperare la stringa di connessione con l'interfaccia della riga di comando di AzureRetrieve the connection string with the Azure CLI

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT. Usare il comando [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) per restituire la stringa di connessione per un singolo dispositivo:Use the az iot hub device-identity show-connection-string command to return the connection string for a single device:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Per il parametro `device-id` viene fatta distinzione tra maiuscole e minuscole. Non copiare le virgolette alle estremità della stringa di connessione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di un'identità di dispositivo registrata nell'hub IoT, è possibile installare il runtime IoT Edge nei dispositivi. Installare il runtime in base al sistema operativo del dispositivo:

* [Installare Azure IoT Edge in Windows](how-to-install-iot-edge-windows.md)
* [Installare il runtime di Azure IoT Edge in LinuxInstall the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)
