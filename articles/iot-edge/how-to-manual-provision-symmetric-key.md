---
title: Eseguire il provisioning con chiavi simmetriche-Azure IoT Edge | Microsoft Docs
description: Dopo l'installazione, effettuare il provisioning di un dispositivo IoT Edge con chiavi simmetriche usando la relativa stringa di connessione ed eseguire l'autenticazione nell'hub.
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 9e288bcbebe4118bfc8cfa7cff46c79d7075555a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979719"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>Configurare un dispositivo di Azure IoT Edge con l'autenticazione con chiave simmetrica

Questo articolo illustra i passaggi per registrare un nuovo dispositivo IoT Edge nell'hub Internet e configurare il dispositivo per l'autenticazione con chiavi simmetriche.

La procedura illustrata in questo articolo descrive un processo denominato provisioning manuale, in cui ogni dispositivo viene connesso manualmente all'hub delle cose. L'alternativa è il provisioning automatico tramite il servizio Device provisioning in hub Internet, che risulta utile quando si dispone di molti dispositivi di cui eseguire il provisioning.

<!--TODO: Add auto-provision info/links-->

Per il provisioning manuale, sono disponibili due opzioni per l'autenticazione dei dispositivi IoT Edge:

* **Chiave simmetrica**: quando si crea una nuova identità del dispositivo nell'hub Internet, il servizio crea due chiavi. Si inserisce una delle chiavi nel dispositivo e viene visualizzata la chiave nell'hub Internet durante l'autenticazione.

  Questo metodo di autenticazione è più veloce per iniziare, ma non come protetto.

* **X. 509 autofirmato**: si creano due certificati di identità x. 509 e li si inserisce nel dispositivo. Quando si crea una nuova identità del dispositivo nell'hub Internet, è necessario fornire le identificazioni personali di entrambi i certificati. Quando il dispositivo esegue l'autenticazione nell'hub Internet, presenta i certificati e l'hub delle cose può verificare che corrispondano alle identificazioni personali.

  Questo metodo di autenticazione è più sicuro e consigliato per gli scenari di produzione.

Questo articolo illustra il processo di registrazione e provisioning con l'autenticazione con chiave simmetrica. Per informazioni su come configurare un dispositivo con certificati X. 509, vedere [configurare un dispositivo Azure IOT Edge con l'autenticazione del certificato x. 509](how-to-manual-provision-x509.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di seguire la procedura descritta in questo articolo, è necessario disporre di un dispositivo con il runtime di IoT Edge installato. In caso contrario, seguire la procedura descritta in [installare o disinstallare il runtime di Azure IOT Edge](how-to-install-iot-edge.md).

## <a name="register-a-new-device"></a>Registrare un nuovo dispositivo

Ogni dispositivo che si connette a un hub Internet ha un ID dispositivo usato per tenere traccia delle comunicazioni da cloud a dispositivo o da dispositivo a cloud. Un dispositivo viene configurato con le relative informazioni di connessione, che include il nome host dell'hub Internet, l'ID del dispositivo e le informazioni usate dal dispositivo per l'autenticazione nell'hub.

Per l'autenticazione con chiave simmetrica, queste informazioni vengono raccolte in una *stringa di connessione* che è possibile recuperare dall'hub Internet e quindi posizionarsi sul dispositivo IOT Edge.

È possibile usare diversi strumenti per registrare un nuovo dispositivo IoT Edge nell'hub Internet e recuperare la relativa stringa di connessione, a seconda delle proprie preferenze.

# <a name="portal"></a>[Portale](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisiti per il portale di Azure

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito o standard nella sottoscrizione di Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Creare un dispositivo IoT Edge nel portale di Azure

Nell'hub IoT sul portale di Azure, i dispositivi IoT Edge vengono creati e gestiti separatamente dai dispositivi IoT non abilitati per Edge.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

1. Nel riquadro sinistro selezionare **IOT Edge** dal menu e quindi selezionare **aggiungi un dispositivo IOT Edge**.

   ![Aggiungere un dispositivo IoT Edge dalla portale di Azure](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Nella pagina **Crea un dispositivo** specificare le informazioni seguenti:

   * Creare un ID dispositivo descrittivo.
   * Selezionare **Chiave simmetrica** come tipo di autenticazione.
   * Usare le impostazioni predefinite per la generazione automatica delle chiavi di autenticazione e la connessione del nuovo dispositivo all'hub.

1. Selezionare **Salva**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Visualizzare i dispositivi IoT Edge nel portale di Azure

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

![Usare il portale di Azure per visualizzare tutti i dispositivi IoT Edge nell'hub Internet delle cose](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recuperare la stringa di connessione nel portale di Azure

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Dalla pagina **IoT Edge** nel portale, fare clic sull'ID del dispositivo nell'elenco dei dispositivi IoT Edge.
2. Copiare il valore **Stringa di connessione primaria** o **Stringa di connessione secondaria**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Prerequisiti per Visual Studio Code

* Un [Hub](../iot-hub/iot-hub-create-through-portal.md) delle cose gratuito o standard nella sottoscrizione di Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

Per eseguire operazioni con l'hub IoT è possibile usare le estensioni Azure IoT per Visual Studio Code. Per il corretto funzionamento di queste operazioni è necessario accedere all'account Azure e selezionare l'hub IoT.

1. In Visual Studio Code aprire la **finestra di esplorazione**.
1. Nella parte inferiore di Explorer, espandere la sezione **Hub IoT di Azure**.

   ![Espandere la sezione Azure IoT Hub Devices (Dispositivi dell'Hub IoT di Azure).](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

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

![Usare VS Code per visualizzare tutti i dispositivi IoT Edge nell'hub Internet delle cose](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperare la stringa di connessione con Visual Studio Code

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT.

1. Fare clic con il pulsante destro del mouse sull'ID del dispositivo nella sezione **Hub IoT di Azure**.
1. Selezionare **Copy Device Connection String** (Copia stringa di connessione dispositivo).

   La stringa di connessione verrà copiata negli Appunti.

È anche possibile selezionare **Get Device Info** (Ottieni info dispositivo) dal menu di scelta rapida per visualizzare nella finestra di output tutte le informazioni relative al dispositivo, inclusa la stringa di connessione.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di Azure

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure.
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) nell'ambiente in uso. Come minimo, la versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o successiva. Usare il comando `az --version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.
* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Creare un dispositivo IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) per creare una nuova identità del dispositivo nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Questo comando include tre parametri:

* `--device-id``-d`in alternativa, specificare un nome descrittivo univoco per l'hub Internet.
* `hub-name` in alternativa `-n` , specificare il nome dell'hub Internet delle cose.
* `--edge-enabled` oppure `--ee` : dichiarare che il dispositivo è un dispositivo IOT Edge.

   ![az iot hub device-identity create output](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visualizzare i dispositivi IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) per visualizzare tutti i dispositivi nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperare la stringa di connessione con l'interfaccia della riga di comando di Azure

Quando si è pronti per configurare il dispositivo, è necessaria la stringa di connessione che collega il dispositivo fisico alla relativa identità nell'hub IoT. Per ottenere la stringa di connessione del dispositivo, usare il comando [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string):

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Per il parametro `device-id` viene fatta distinzione tra maiuscole e minuscole. Non copiare le virgolette alle estremità della stringa di connessione.

---

## <a name="provision-an-iot-edge-device"></a>Effettuare il provisioning di un dispositivo IoT Edge

Quando il dispositivo IoT Edge ha un'identità nell'hub Internet e una stringa di connessione che può usare per l'autenticazione, è necessario effettuare il provisioning del dispositivo con queste informazioni.

In un dispositivo Linux è possibile specificare la stringa di connessione modificando un file config. yaml. In un dispositivo Windows specificare la stringa di connessione eseguendo uno script di PowerShell.

# <a name="linux"></a>[Linux](#tab/linux)

Nel dispositivo IoT Edge aprire il file di configurazione.

```bash
sudo nano /etc/iotedge/config.yaml
```

Trovare le configurazioni del provisioning del file e rimuovere il commento dalla **configurazione del provisioning manuale usando una sezione della stringa di connessione** . 

Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge. Assicurarsi che tutte le altre sezioni di provisioning siano impostate come commento. Verificare che il **provisioning:** la riga non includa spazi vuoti precedenti e che gli elementi nidificati vengano rientrati da due spazi.

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

Per incollare il contenuto degli Appunti in nano `Shift+Right Click` o premere `Shift+Insert` .

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. Nel dispositivo IoT Edge eseguire PowerShell come amministratore.

2. Usare il comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) per configurare il runtime di IOT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Se si usano contenitori Linux, aggiungere il `-ContainerOs` parametro al flag. Essere coerenti con l'opzione del contenitore selezionata con il `Deploy-IoTEdge` comando eseguito in precedenza.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Se è stato scaricato lo script IoTEdgeSecurityDaemon.ps1 nel dispositivo per l'installazione offline o specifica della versione, assicurarsi di fare riferimento alla copia locale dello script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando richiesto, specificare la stringa di connessione del dispositivo recuperata nella sezione precedente. La stringa di connessione del dispositivo associa il dispositivo fisico a un ID dispositivo nell'hub e fornisce informazioni di autenticazione.

   La stringa di connessione del dispositivo ha il formato seguente e non deve includere le virgolette: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Quando si esegue il provisioning di un dispositivo manualmente, è possibile usare parametri aggiuntivi per modificare il processo, tra cui:

* Indirizzare il traffico attraverso un server proxy
* Dichiarare un'immagine del contenitore edgeAgent specifica e fornire le credenziali se si trova in un registro privato

Per ulteriori informazioni su questi parametri aggiuntivi, vedere [script di PowerShell per IOT Edge in Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Passaggi successivi

Continuare a [distribuire i moduli IOT Edge](how-to-deploy-modules-portal.md) per informazioni su come distribuire i moduli nel dispositivo.
