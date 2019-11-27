---
title: Registrare un nuovo dispositivo Azure IoT Edge | Microsoft Docs
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
# <a name="register-an-azure-iot-edge-device"></a>Registrare un dispositivo Azure IoT Edge

Prima di poter usare i dispositivi Internet delle cose con Azure IoT Edge, è necessario registrarli con l'hub Internet delle cose. Una volta registrato un dispositivo, è possibile recuperare una stringa di connessione per configurare il dispositivo per i carichi di lavoro IoT Edge.

È possibile scegliere di eseguire la registrazione usando uno degli strumenti seguenti:

* [Portale di Azure](https://portal.azure.com) offre un'interfaccia utente grafica per la creazione, la visualizzazione e la gestione delle risorse di Azure.
* [Visual Studio Code](https://code.visualstudio.com/) è un editor del codice sorgente. Azure Internet Extensions consente di gestire facilmente le risorse di Internet delle cose dallo stesso strumento in cui si stanno sviluppando soluzioni Internet.
* [CLI di Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) è uno strumento da riga di comando per la gestione delle risorse di Azure. I comandi riutilizzabili sono utili per automatizzare le attività.

## <a name="register-in-the-azure-portal"></a>Eseguire la registrazione nel portale di Azure

È possibile eseguire tutte le attività di registrazione nel portale di Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisiti per la portale di Azure

Un [Hub](../iot-hub/iot-hub-create-through-portal.md) di Internet delle cose gratuito o standard nella sottoscrizione di Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Creare un dispositivo IoT Edge nel portale di Azure

Nell'hub Internet delle cose nel portale di Azure IoT Edge i dispositivi vengono creati e gestiti separatamente dai dispositivi Internet che non sono abilitati per Edge.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
2. Nel riquadro sinistro selezionare **IOT Edge** dal menu.
3. Selezionare **Aggiungi un dispositivo IoT Edge**.
4. Specificare un ID descrittivo del dispositivo. Usare le impostazioni predefinite per generare automaticamente le chiavi di autenticazione e connettere il nuovo dispositivo all'hub.
5. Selezionare **Salva**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Visualizzare i dispositivi IoT Edge nella portale di Azure

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recuperare la stringa di connessione nel portale di Azure

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Dalla pagina **IOT Edge** nel portale, fare clic sull'ID del dispositivo nell'elenco dei dispositivi IOT Edge.
2. Copiare il valore di **Stringa di connessione (chiave primaria)** o **Stringa di connessione (chiave secondaria)** .

## <a name="register-with-visual-studio-code"></a>Registra con Visual Studio Code

Esistono diversi modi per eseguire la maggior parte delle operazioni in VS Code. Questo articolo usa Esplora risorse, ma è anche possibile usare il riquadro comandi per eseguire la procedura.

### <a name="prerequisites-for-visual-studio-code"></a>Prerequisiti per Visual Studio Code

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

È possibile usare le estensioni Azure per la Visual Studio Code per eseguire operazioni con l'hub Internet delle cose. Per il corretto funzionamento di queste operazioni, è necessario accedere al proprio account Azure e selezionare l'hub Internet.

1. In Visual Studio Code aprire la **finestra di esplorazione**.
1. Nella parte inferiore della finestra di esplorazione espandere la sezione **Hub Azure** .

   ![Espandere la sezione Azure IoT Hub Devices (Dispositivi dell'Hub IoT di Azure).](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Fare clic su **..** . nell'intestazione della sezione **Hub Azure** . Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).
1. Se non è stato effettuato l'accesso all'account Azure, seguire le istruzioni per eseguire questa operazione.
1. Selezionare la sottoscrizione di Azure.
1. Selezionare l'hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Creare un dispositivo IoT Edge con Visual Studio Code

1. Nella finestra di esplorazione di VS Code espandere la sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure).
1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Azure IoT Hub Devices** (Dispositivi hub IoT di Azure). Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione o fare clic su di essa.
1. Selezionare **Create IoT Edge Device** (Crea dispositivo IoT Edge).
1. Nella casella di testo che si apre specificare un ID per il dispositivo.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

Nella schermata di output viene visualizzato il risultato del comando. Vengono stampate le informazioni relative al dispositivo, che includono il valore di **deviceId** fornito dall'utente e il valore di **connectionString** utilizzabile per connettere il dispositivo fisico all'hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visualizzare dispositivi IoT Edge con Visual Studio Code

Tutti i dispositivi che si connettono all'hub Internet sono elencati nella sezione **Hub Azure** Internet Explorer del Visual Studio Code Explorer. I dispositivi IoT Edge sono distinguibili dai dispositivi non perimetrali con un'icona diversa e il fatto che i moduli **$edgeAgent** e **$edgeHub** vengano distribuiti in ogni dispositivo IOT Edge.

![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperare la stringa di connessione con Visual Studio Code

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Fare clic con il pulsante destro del mouse sull'ID del dispositivo nella sezione **Hub Azure** .
1. Selezionare **Copy Device Connection String** (Copia stringa di connessione dispositivo).

   La stringa di connessione verrà copiata negli Appunti.

È anche possibile selezionare **Get Device Info** (Ottieni info dispositivo) dal menu di scelta rapida per visualizzare nella finestra di output tutte le informazioni relative al dispositivo, inclusa la stringa di connessione.

## <a name="register-with-the-azure-cli"></a>Eseguire la registrazione nell'interfaccia della riga di comando di Azure

L' [interfaccia](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) della riga di comando di Azure è uno strumento da riga di comando multipiattaforma open source per la gestione delle risorse di Azure, ad esempio IOT Edge. Consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio di provisioning di dispositivi e gli hub collegati predefiniti. La nuova estensione IoT arricchisce l'interfaccia della riga di comando di Azure con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di Azure

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure.
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. La versione dell'interfaccia della riga di comando di Azure deve essere 2.0.24 o successiva. Usare il comando `az --version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.
* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Creare un dispositivo di IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [AZ all Hub Device-Identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) per creare una nuova identità del dispositivo nell'hub Internet. Ad esempio:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Questo comando include tre parametri:

* **device-id**: specificare un nome descrittivo univoco per l'hub IoT.
* **hub-name**: specificare il nome dell'hub IoT.
* **edge-enabled**: questo parametro dichiara che il dispositivo è destinato all'uso con IoT Edge.

   ![az iot hub device-identity create output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visualizzare dispositivi IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [AZ all Hub Device-Identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) per visualizzare tutti i dispositivi nell'hub Internet. Ad esempio:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperare la stringa di connessione con l'interfaccia della riga di comando di Azure

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT. Usare il comando [AZ all Hub Device-Identity Show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) per restituire la stringa di connessione per un singolo dispositivo:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Per il parametro `device-id` viene fatta distinzione tra maiuscole e minuscole. Non copiare le virgolette alle estremità della stringa di connessione.

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di un'identità del dispositivo registrata nell'hub Internet, si è pronti per installare il runtime di IoT Edge nei dispositivi. Installare il runtime in base al sistema operativo del dispositivo:

* [Installare Azure IoT Edge in Windows](how-to-install-iot-edge-windows.md)
* [Installare il runtime di Azure IoT Edge in Linux](how-to-install-iot-edge-linux.md)
