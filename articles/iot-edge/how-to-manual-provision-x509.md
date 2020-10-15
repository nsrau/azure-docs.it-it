---
title: Eseguire il provisioning con certificati X. 509-Azure IoT Edge | Microsoft Docs
description: Dopo l'installazione, effettuare il provisioning di un dispositivo IoT Edge con i relativi certificati di identità del dispositivo ed eseguire l'autenticazione nell'hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: b1aa12bd73772b5d6332a36d749ec4d7d10d4026
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048186"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Configurare un dispositivo di Azure IoT Edge con l'autenticazione del certificato X. 509

Questo articolo illustra i passaggi per registrare un nuovo dispositivo IoT Edge nell'hub Internet e configurare il dispositivo per l'autenticazione con i certificati X. 509.

La procedura illustrata in questo articolo descrive un processo denominato provisioning manuale, in cui ogni dispositivo viene connesso manualmente all'hub delle cose. L'alternativa è il provisioning automatico tramite il servizio Device provisioning in hub Internet, che risulta utile quando si dispone di molti dispositivi di cui eseguire il provisioning.

<!--TODO: Add auto-provision info/links-->

Per il provisioning manuale, sono disponibili due opzioni per l'autenticazione dei dispositivi IoT Edge:

* **Chiave simmetrica**: quando si crea una nuova identità del dispositivo nell'hub Internet, il servizio crea due chiavi. Si inserisce una delle chiavi nel dispositivo e viene visualizzata la chiave nell'hub Internet durante l'autenticazione.

  Questo metodo di autenticazione è più veloce per iniziare, ma non come protetto.

* **X. 509 autofirmato**: si creano due certificati di identità x. 509 e li si inserisce nel dispositivo. Quando si crea una nuova identità del dispositivo nell'hub Internet, è necessario fornire le identificazioni personali di entrambi i certificati. Quando il dispositivo esegue l'autenticazione nell'hub Internet, presenta i certificati e l'hub delle cose può verificare che corrispondano alle identificazioni personali.

  Questo metodo di autenticazione è più sicuro e consigliato per gli scenari di produzione.

Questo articolo illustra il processo di registrazione e provisioning con l'autenticazione del certificato X. 509. Per informazioni su come configurare un dispositivo con chiavi simmetriche, vedere [configurare un dispositivo Azure IOT Edge con l'autenticazione con chiave simmetrica](how-to-manual-provision-symmetric-key.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di seguire la procedura descritta in questo articolo, è necessario disporre di un dispositivo con il runtime di IoT Edge installato. In caso contrario, seguire la procedura descritta in [installare o disinstallare il runtime di Azure IOT Edge](how-to-install-iot-edge.md).

Il provisioning manuale con certificati X. 509 richiede IoT Edge versione 1.0.10 o successiva.

## <a name="create-certificates-and-thumbprints"></a>Creare certificati e identificazioni personali



<!-- TODO -->

## <a name="register-a-new-device"></a>Registrare un nuovo dispositivo

Ogni dispositivo che si connette a un hub Internet ha un ID dispositivo usato per tenere traccia delle comunicazioni da cloud a dispositivo o da dispositivo a cloud. Un dispositivo viene configurato con le informazioni di connessione che includono il nome host dell'hub Internet, l'ID del dispositivo e le informazioni usate dal dispositivo per l'autenticazione nell'hub.

Per l'autenticazione del certificato X. 509, queste informazioni vengono fornite sotto forma di *identificazione personale* ottenuta dai certificati di identità del dispositivo. Queste identificazioni personali vengono assegnate all'hub delle cose al momento della registrazione del dispositivo, in modo che il servizio possa riconoscere il dispositivo quando si connette.

È possibile usare diversi strumenti per registrare un nuovo dispositivo IoT Edge nell'hub Internet e caricare le relative identificazioni personali del certificato. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisiti per il portale di Azure

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito o standard nella sottoscrizione di Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Creare un dispositivo IoT Edge nel portale di Azure

Nell'hub IoT sul portale di Azure, i dispositivi IoT Edge vengono creati e gestiti separatamente dai dispositivi IoT non abilitati per Edge.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

1. Nel riquadro sinistro selezionare **IOT Edge** dal menu e quindi selezionare **aggiungi un dispositivo IOT Edge**.

   ![Aggiungere un dispositivo IoT Edge dalla portale di Azure](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Nella pagina **Crea un dispositivo** specificare le informazioni seguenti:

   * Creare un ID dispositivo descrittivo. Prendere nota di questo ID dispositivo, che verrà usato nella sezione successiva.
   * Selezionare **X.509 autofirmato** come tipo di autenticazione.
   * Fornire le identificazioni personali del certificato di identità primaria e secondaria. I valori di identificazione personale sono 40 caratteri esadecimali per gli hash SHA-1 o i caratteri esadecimali 64 per gli hash SHA-256.

1. Selezionare **Salva**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Visualizzare i dispositivi IoT Edge nel portale di Azure

Tutti i dispositivi abilitati per Edge che si connettono all'hub IoT sono elencati nella pagina **IoT Edge**.

![Visualizzare tutti i dispositivi IoT Edge nell'hub IoT](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisiti per l'interfaccia della riga di comando di Azure

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) nella sottoscrizione di Azure.
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) nell'ambiente in uso. La versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o successiva. Usare il comando `az --version` per verificare. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack.
* [Estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Creare un dispositivo IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) per creare una nuova identità del dispositivo nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Questo comando include diversi parametri:

* `--device-id``-d`in alternativa, specificare un nome descrittivo univoco per l'hub Internet. Prendere nota di questo ID dispositivo, che verrà usato nella sezione successiva.
* `hub-name` in alternativa `-n` , specificare il nome dell'hub Internet delle cose.
* `--edge-enabled` oppure `--ee` : dichiarare che il dispositivo è un dispositivo IOT Edge.
* `--auth-method``--am`in alternativa, dichiarare il tipo di autorizzazione che verrà usato dal dispositivo. In questo caso, vengono usate le identificazioni personali del certificato X. 509.
* `--primary-thumbprint` o `--ptp` : fornire un'identificazione personale del certificato X. 509 da utilizzare come chiave primaria.
* `--secondary-thumbprint` o `--stp` : fornire un'identificazione personale del certificato X. 509 da usare come chiave secondaria.

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visualizzare i dispositivi IoT Edge con l'interfaccia della riga di comando di Azure

Usare il comando [az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) per visualizzare tutti i dispositivi nell'hub IoT. Ad esempio:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Aggiungere il flag `--edge-enabled` o `--ee` per elencare solo i dispositivi IOT Edge nell'hub.

Per qualsiasi dispositivo registrato come dispositivo IoT Edge, la proprietà **capabilities.iotEdge** sarà impostata su **true**.

--- 

## <a name="configure-an-iot-edge-device"></a>Configurare un dispositivo IoT Edge

Quando il dispositivo di IoT Edge ha un'identità nell'hub Internet, è necessario configurare il dispositivo con la relativa identità cloud e i relativi certificati di identità.

In un dispositivo Linux è possibile fornire queste informazioni modificando un file config. yaml. In un dispositivo Windows, è possibile fornire queste informazioni eseguendo uno script di PowerShell.

# <a name="linux"></a>[Linux](#tab/linux)

1. Nel dispositivo IoT Edge aprire il file di configurazione.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Trovare la sezione configurazioni di provisioning del file. 

1. Impostare come commento la **configurazione del provisioning manuale usando una sezione della stringa di connessione** .

1. Rimuovere il commento dalla **configurazione del provisioning manuale usando una sezione relativa al certificato di identità X. 509** . Verificare che il **provisioning:** la riga non includa spazi vuoti precedenti e che gli elementi nidificati vengano rientrati da due spazi.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. Aggiornare i campi seguenti:

   * **iothub_hostname**: nome host dell'hub Internet delle cose a cui si connetterà il dispositivo. Ad esempio, `{IoT hub name}.azure-devices.net`
   * **DEVICE_ID**: ID specificato durante la registrazione del dispositivo.
   * **identity_cert**: URI di un certificato di identità nel dispositivo. Ad esempio, `file:///path/identity_certificate.pem`
   * **identity_pk**: URI del file di chiave privata per il certificato di identità fornito. Ad esempio, `file:///path/identity_key.pem`

1. Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

1. Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. Nel dispositivo IoT Edge eseguire PowerShell come amministratore.

2. Usare il comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) per configurare il runtime di IOT Edge nel computer.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Se si usano contenitori Linux, aggiungere il `-ContainerOs` parametro al flag. Essere coerenti con l'opzione del contenitore selezionata con il `Deploy-IoTEdge` comando eseguito in precedenza.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Se è stato scaricato lo script IoTEdgeSecurityDaemon.ps1 nel dispositivo per l'installazione offline o specifica della versione, assicurarsi di fare riferimento alla copia locale dello script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando richiesto, specificare le informazioni seguenti:

   * **IotHubHostName**: nome host dell'hub Internet delle cose a cui si connetterà il dispositivo. Ad esempio, `{IoT hub name}.azure-devices.net`
   * **DeviceID**: ID specificato durante la registrazione del dispositivo.
   * **X509IdentityCertificate**: percorso assoluto di un certificato di identità nel dispositivo. Ad esempio, `C:\path\identity_certificate.pem`
   * **X509IdentityPrivateKey**: percorso assoluto del file di chiave privata per il certificato di identità fornito. Ad esempio, `C:\path\identity_key.pem`

Quando si esegue il provisioning di un dispositivo manualmente, è possibile usare parametri aggiuntivi per modificare il processo, tra cui:

* Indirizzare il traffico attraverso un server proxy
* Dichiarare un'immagine del contenitore edgeAgent specifica e fornire le credenziali se si trova in un registro privato

Per ulteriori informazioni su questi parametri aggiuntivi, vedere [script di PowerShell per IOT Edge in Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Passaggi successivi

Continuare a [distribuire i moduli IOT Edge](how-to-deploy-modules-portal.md) per informazioni su come distribuire i moduli nel dispositivo.