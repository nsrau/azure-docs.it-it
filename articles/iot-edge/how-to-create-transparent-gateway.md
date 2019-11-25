---
title: Creare un dispositivo gateway trasparente - Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo Azure IoT Edge per creare un gateway trasparente in grado di elaborare informazioni da dispositivi downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d0ac7fa3a1dbb1c91da5b9919bc2c62de74213b5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456790"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurare un dispositivo IoT Edge come gateway trasparente

This article provides detailed instructions for configuring an IoT Edge device to function as a transparent gateway for other devices to communicate with IoT Hub. In questo articolo il termine *gateway IoT Edge* si riferisce a un dispositivo IoT Edge usato come gateway trasparente. For more information, see [How an IoT Edge device can be used as a gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Al momento:
> * I dispositivi abilitati per Edge non possono connettersi ai gateway IoT Edge. 
> * I dispositivi downstream non possono usare il caricamento dei file.

There are three general steps to set up a successful transparent gateway connection. This article covers the first step:

1. **The gateway device needs to be able to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination.**
2. The downstream device needs to have a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. The downstream device needs to be able to securely connect to its gateway device. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).


For a device to function as a gateway, it needs to be able to securely connect to its downstream devices. Azure IoT Edge permette di usare un'infrastruttura a chiave pubblica (PKI) per configurare connessioni sicure tra dispositivi. Nel caso illustrato si consente la connessione di un dispositivo downstream a un dispositivo IoT Edge che funge da gateway trasparente. To maintain reasonable security, the downstream device should confirm the identity of the gateway device. This identity check prevents your devices from connecting to potentially malicious gateways.

A downstream device in a transparent gateway scenario can be any application or platform that has an identity created with the [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloud service. In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Per scopi pratici, un dispositivo downstream può anche essere un'applicazione in esecuzione nel dispositivo gateway IoT Edge stesso. However, an IoT Edge device cannot be downstream of an IoT Edge gateway. 

È possibile creare qualsiasi infrastruttura di certificati che abilita la relazione di trust necessaria per la topologia dispositivo-gateway. In this article, we assume the same certificate setup that you would use to enable [X.509 CA security](../iot-hub/iot-hub-x509ca-overview.md) in IoT Hub, which involves an X.509 CA certificate associated to a specific IoT hub (the IoT hub root CA), a series of certificates signed with this CA, and a CA for the IoT Edge device.

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>The term "root CA" used throughout this article refers to the topmost authority public certificate of the PKI certificate chain, and not necessarily the certificate root of a syndicated certificate authority. In many cases, it is actually an intermediate CA public certificate. 

The gateway presents its IoT Edge device CA certificate to the downstream device during the initiation of the connection. The downstream device checks to make sure the IoT Edge device CA certificate is signed by the root CA certificate. This process allows the downstream device to confirm that the gateway comes from a trusted source.

The following steps walk you through the process of creating the certificates and installing them in the right places on the gateway. È possibile usare qualsiasi computer per generare i certificati e quindi copiarli nel dispositivo IoT Edge. 

## <a name="prerequisites"></a>Prerequisiti

* A development machine to create certificates. 
* Un dispositivo Azure IoT Edge da configurare come gateway. Use the IoT Edge installation steps for one of the following operating systems:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Generare certificati con Windows

Use the steps in this section to generate test certificates on Windows. You can use a Windows machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

I certificati generati in questa sezione devono essere usati solo a scopo di test. 

### <a name="install-openssl"></a>Installare OpenSSL

Installare OpenSSL per Windows nel computer usato per generare i certificati. If you already have OpenSSL installed on your Windows device, you may skip this step, but ensure that openssl.exe is available in your PATH environment variable. 

There are several ways to install OpenSSL, including:

* **Easier:** Download and install any [third-party OpenSSL binaries](https://wiki.openssl.org/index.php/Binaries), for example, from [OpenSSL on SourceForge](https://sourceforge.net/projects/openssl/). Aggiungere il percorso completo di openssl.exe alla variabile di ambiente PATH. 
   
* **Metodo consigliato:** scaricare il codice sorgente di OpenSSL source code e creare i file binari nel computer personalmente o tramite [vcpkg](https://github.com/Microsoft/vcpkg). Nelle istruzioni elencate di seguito viene usato vcpkg per scaricare il codice sorgente, compilare e installare OpenSSL nel computer Windows con passaggi molto semplici.

   1. Passare alla directory in cui installare vcpkg, Questa directory è denominata *\<VCPKGDIR>* . Seguire le istruzioni per scaricare e installare [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Once vcpkg is installed, run the following command from a powershell prompt to install the OpenSSL package for Windows x64. Per completare l'installazione sono in genere necessari circa cinque minuti.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Aggiungere `<VCPKGDIR>\installed\x64-windows\tools\openssl` alla variabile di ambiente PATH in modo da rendere il file openssl.exe disponibile per la chiamata.

### <a name="prepare-creation-scripts"></a>Preparare gli script di creazione

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. Aprire una finestra di PowerShell in modalità amministratore. 

2. Clonare il repository git che contiene gli script per generare certificati non di produzione. Questi script consentono di creare i certificati necessari per configurare un gateway trasparente. Usare il comando `git clone` o [scaricare il file ZIP](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Passare alla directory che si vuole usare, Throughout this article, we'll call this directory *\<WRKDIR>* . All certificates and keys will be created in this working directory.

4. Copy the configuration and script files from the cloned repo into your working directory. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   If you downloaded the repo as a ZIP, then the folder name is `iotedge-master` and the rest of the path is the same. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Abilitare PowerShell per l'esecuzione degli script.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Bring the functions used by the scripts into PowerShell's global namespace.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   The PowerShell window will display a warning that the certificates generated by this script are only for testing purposes, and should not be used in production scenarios.

8. Verify that OpenSSL has been installed correctly and make sure that there won't be name collisions with existing certificates. In caso di problemi, lo script dovrebbe descrivere come correggerli nel sistema in uso.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Creare i certificati

In questa sezione verranno creati tre certificati, che verranno quindi connessi in una catena. L'inserimento dei certificati in una catena permette di installarli facilmente nel dispositivo gateway IoT Edge e in tutti i dispositivi downstream.  

1. Create the root CA certificate and have it sign one intermediate certificate. The certificates are all placed in your working directory.

   ```powershell
   New-CACertsCertChain rsa
   ```

   This script command creates several certificate and key files, but we're going to refer to one in particular later in this article:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   This script command creates several certificate and key files, including two that we're going to refer to later in this article:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

Now that you have the certificates, skip ahead to [Install certificates on the gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generare certificati con Linux

Use the steps in this section to generate test certificates on Linux. You can use a Linux machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

I certificati generati in questa sezione devono essere usati solo a scopo di test. 

### <a name="prepare-creation-scripts"></a>Preparare gli script di creazione

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. Clonare il repository git che contiene gli script per generare certificati non di produzione. Questi script consentono di creare i certificati necessari per configurare un gateway trasparente. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Passare alla directory che si vuole usare, We'll refer to this directory throughout the article as *\<WRKDIR>* . All certificate and key files will be created in this directory.
  
3. Copy the config and script files from the cloned IoT Edge repo into your working directory.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>Creare i certificati

In questa sezione verranno creati tre certificati, che verranno quindi connessi in una catena. L'inserimento dei certificati in un file di catena permette di installarli facilmente nel dispositivo gateway IoT Edge e in tutti i dispositivi downstream.  

1. Create the root CA certificate and one intermediate certificate. Questi certificati vengono inseriti in *\<WRKDIR*.

   If you've already created root and intermediate certificates in this working directory, don't run this script again. Rerunning this script will overwrite the existing certificates. Instead, proceed to the next step. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   The script creates several certificates and keys. Make note of one, which we'll refer to in the next section:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   The script creates several certificates and keys. Make note of two, which we'll refer to in the next section: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

## <a name="install-certificates-on-the-gateway"></a>Installare i certificati nel gateway

Dopo aver creato una catena di certificati, è necessario installarla nel dispositivo gateway IoT Edge e configurare il runtime IoT Edge perché faccia riferimento ai nuovi certificati. 

1. Copiare i file seguenti da *\<WRKDIR>* . Salvarli in una posizione qualsiasi nel dispositivo IoT Edge. La directory di destinazione nel dispositivo IoT Edge è denominata *\<CERTDIR>* . 

   * Certificato della CA del dispositivo: `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Chiave privata della CA del dispositivo: `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Root CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate files.  If you generated the certificates on the IoT Edge device itself, you can skip this step and use the path to the working directory.

2. Aprire il file di configurazione del daemon di sicurezza di IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Set the **certificate** properties in the config.yaml file to the full path to the certificate and key files on the IoT Edge device. Remove the `#` character before the certificate properties to uncomment the four lines. Remember that indents in yaml are two spaces.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. On Linux devices, make sure that the user **iotedge** has read permissions for the directory holding the certificates. 

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuire Hub Edge nel gateway

When you first install IoT Edge on a device, only one system module starts automatically: the IoT Edge agent. Perché il dispositivo possa operare come gateway, sono necessari entrambi i moduli di sistema. If you haven't deployed any modules to your gateway device before, create an initial deployment for your device to start the second system module, the IoT Edge hub. The deployment will look empty because you don't add any modules in the wizard, but it will make sure that both system modules are running. 

È possibile controllare quali moduli sono in esecuzione in un dispositivo con il comando `iotedge list`. If the list only returns the module **edgeAgent** without **edgeHub**, use the following steps:

1. Nel portale di Azure passare all'hub IoT.

2. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge da usare come gateway.

3. Selezionare **Set Modules** (Configura i moduli).

4. Selezionare **Avanti**.

5. Nel passaggio **Specifica route** deve essere indicata una route predefinita che invia tutti i messaggi da tutti i moduli all'hub IoT. In caso contrario, aggiungere il codice seguente e quindi selezionare **Avanti**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Nel passaggio **Rivedi modello** selezionare **Invia**.

## <a name="open-ports-on-gateway-device"></a>Open ports on gateway device

Standard IoT Edge devices don't need any inbound connectivity to function, because all communication with IoT Hub is done through outbound connections. Gateway devices are different because they need to receive messages from their downstream devices. If a firewall is between the downstream devices and the gateway device, then communication needs to be possible through the firewall as well.

For a gateway scenario to work, at least one of the IoT Edge hub's supported protocols must be open for inbound traffic from downstream devices. The supported protocols are MQTT, AMQP, and HTTPS. 

| Porta | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Instradare i messaggi da dispositivi downstream
Il runtime IoT Edge può instradare i messaggi inviati dai dispositivi downstream come i messaggi inviati dai moduli. This feature allows you to perform analytics in a module running on the gateway before sending any data to the cloud. 

Attualmente, il modo per instradare i messaggi inviati dai dispositivi downstream consiste nel differenziarli dai messaggi inviati dai moduli. I messaggi inviati da tutti i moduli contengono una proprietà di sistema denominata **connectionModuleId** ma non i messaggi inviati dai dispositivi downstream. È possibile utilizzare la clausola WHERE della route da escludere eventuali messaggi che contengono tale proprietà di sistema. 

The below route is an example that would send messages from any downstream device to a module named `ai_insights`, and then from `ai_insights` to IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Per altre informazioni sul routing dei messaggi, vedere [distribuire moduli e stabilire le route](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Enable extended offline operation

Starting with the [v1.0.4 release](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) of the IoT Edge runtime, the gateway device and downstream devices connecting to it can be configured for extended offline operation. 

With this capability, local modules or downstream devices can re-authenticate with the IoT Edge device as needed and communicate with each other using messages and methods even when disconnected from the IoT hub. Per altre informazioni, vedere [Informazioni sulle funzionalità per periodi offline prolungati per i dispositivi IoT Edge, i moduli e i dispositivi figlio](offline-capabilities.md).

To enable extended offline capabilities, you establish a parent-child relationship between an IoT Edge gateway device and downstream devices that will connect to it. Those steps are explained in more detail in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un dispositivo IoT Edge che opera come gateway trasparente, è necessario configurare i dispositivi downstream in modo da ritenere attendibile il gateway e inviarvi messaggi. Continue on to [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md) for the next steps in setting up your transparent gateway scenario. 
