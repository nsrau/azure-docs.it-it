---
title: Registrare un nuovo dispositivo Azure IoT Edge | Microsoft Docs
description: Usare l'estensione IoT per l'interfaccia della riga di comando di Azure per registrare un nuovo dispositivo IoT Edge e recuperare la stringa di connessione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 85a5e2f6b28b9332e5ad3a38cdad3fb02bb3da87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450137"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrare un dispositivo Azure IoT Edge

Prima di poter usare dispositivi IoT con Azure IoT Edge, è necessario registrarli nell'hub IoT. Una volta registrato un dispositivo, è possibile recuperare una stringa di connessione per configurare il dispositivo per i carichi di lavoro IoT Edge.

È possibile scegliere di eseguire la registrazione usando uno degli strumenti seguenti:

* [Registrare un dispositivo nel portale di Azure](#register-in-the-azure-portal), se si preferisce un'interfaccia utente grafica per creare, visualizzare e gestire le risorse di Azure.
* [Registrare un dispositivo con Visual Studio Code](#register-with-visual-studio-code), se si preferisce gestire le risorse di Azure IoT nella stessa posizione in cui si sviluppano le soluzioni Internet.
* [Registrare un dispositivo con l'interfaccia della riga di comando di Azure](#register-with-the-azure-cli), se si preferisce usare strumenti della riga di comando per la gestione di risorse di Azure o si intende automatizzare alcune attività.

## <a name="register-in-the-azure-portal"></a>Eseguire la registrazione nel portale di Azure

È possibile eseguire tutte le attività di registrazione nel portale di Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisiti per il portale di Azure

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito o standard nella sottoscrizione di Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Creare un dispositivo IoT Edge nel portale di Azure

Nell'hub IoT sul portale di Azure, i dispositivi IoT Edge vengono creati e gestiti separatamente dai dispositivi IoT non abilitati per Edge.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
2. Nel riquadro a sinistra, selezionare **IoT Edge** dal menu.
3. Selezionare **Aggiungi un dispositivo IoT Edge**.
4. Specificare un ID descrittivo del dispositivo. Usare le impostazioni predefinite per la generazione automatica delle chiavi di autenticazione e la connessione del nuovo dispositivo all'hub.
5. Selezionare **Salva**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Visualizzare i dispositivi IoT Edge nel portale di Azure

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recuperare la stringa di connessione nel portale di Azure

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Dalla pagina **IoT Edge** nel portale, fare clic sull'ID del dispositivo nell'elenco dei dispositivi IoT Edge.
2. Copiare il valore **Stringa di connessione primaria** o **Stringa di connessione secondaria**.

## <a name="register-with-visual-studio-code"></a>Eseguire la registrazione con Visual Studio Code

Esistono diversi modi per eseguire la maggior parte delle operazioni in VS Code. In questo articolo viene usato Explorer, ma è anche possibile usare il riquadro comandi per eseguire i passaggi.

### <a name="prerequisites-for-visual-studio-code"></a>Prerequisiti per Visual Studio Code

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

Per eseguire operazioni con l'hub IoT è possibile usare le estensioni Azure IoT per Visual Studio Code. Per il corretto funzionamento di queste operazioni è necessario accedere all'account Azure e selezionare l'hub IoT.

1. In Visual Studio Code aprire la **finestra di esplorazione**.
1. Nella parte inferiore di Explorer, espandere la sezione **Hub IoT di Azure**.

   ![Espandere la sezione Azure IoT Hub Devices (Dispositivi dell'Hub IoT di Azure).](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Hub IoT di Azure**. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).
1. Se ancora non lo si è fatto, eseguire l'accesso all'account di Azure seguendo le istruzioni.
1. Selezionare la sottoscrizione di Azure.
1. Selezionare l'hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Creare un dispositivo IoT Edge con Visual Studio Code

1. Nella finestra di esplorazione di VS Code espandere la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure).
1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Selezionare **Create IoT Edge Device** (Crea dispositivo IoT Edge).
1. Nella casella di testo che si apre specificare un ID per il dispositivo.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visualizzare un dispositivo IoT Edge con Visual Studio Code

Tutti i dispositivi connessi all'hub IoT sono elencati nella sezione **Hub IoT di Azure** della finestra Explorer di Visual Studio Code. I dispositivi IoT Edge si distinguono dai dispositivi non Edge per la presenza di un'icona di tipo diverso e per la distribuzione dei moduli **$edgeAgent** e **$edgeHub** in ogni dispositivo IoT Edge.

![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperare la stringa di connessione con Visual Studio Code

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Fare clic con il pulsante destro del mouse sull'ID del dispositivo nella sezione **Hub IoT di Azure**.
1. Selezionare **Copy Device Connection String** (Copia stringa di connessione dispositivo).

   La stringa di connessione verrà copiata negli Appunti.

È anche possibile selezionare **Get Device Info** (Ottieni info dispositivo) dal menu di scelta rapida per visualizzare nella finestra di output tutte le informazioni relative al dispositivo, inclusa la stringa di connessione.

## <a name="register-with-the-azure-cli"></a>Eseguire la registrazione con l'interfaccia della riga di comando di Azure

L'[interfaccia della riga di comando di Azure](/cli/azure) è uno strumento da riga di comando multipiattaforma e open source per la gestione di risorse di Azure come IoT Edge. Consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio di provisioning di dispositivi e gli hub collegati predefiniti. L'estensione IoT arricchisce l'interfaccia della riga di comando di Azure con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di Azure

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure.
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) nell'ambiente in uso. La versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o successiva. Usare il comando `az --version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.
* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Creare un dispositivo IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) per creare una nuova identità del dispositivo nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Questo comando include tre parametri:

* **device-id**: specificare un nome descrittivo univoco per l'hub IoT.
* **hub-name**: specificare il nome dell'hub IoT.
* **edge-enabled**: questo parametro dichiara che il dispositivo è destinato all'uso con IoT Edge.

   ![az iot hub device-identity create output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visualizzare i dispositivi IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) per visualizzare tutti i dispositivi nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperare la stringa di connessione con l'interfaccia della riga di comando di Azure

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT. Usare il comando [AZ all Hub Device-Identity Connection-String Show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) per restituire la stringa di connessione per un singolo dispositivo:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

Per il parametro `device-id` viene fatta distinzione tra maiuscole e minuscole. Non copiare le virgolette alle estremità della stringa di connessione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di un'identità del dispositivo registrata nell'hub Internet, si è pronti per installare il runtime IoT Edge nei dispositivi. Installare il runtime in base al sistema operativo del dispositivo:

* [Installare Azure IoT Edge in Windows](how-to-install-iot-edge-windows.md)
* [Installare il runtime di Azure IoT Edge in Linux](how-to-install-iot-edge-linux.md)
