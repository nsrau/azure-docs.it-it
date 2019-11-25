---
title: Automatically provision Linux devices with DPS - Azure IoT Edge | Microsoft Docs
description: Usare un TPM simulato in una macchina virtuale Linux per testare il servizio Device Provisioning di Azure per Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 292ae570d4f2ddd0c09e667860ee2ba01b9fc6b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457180"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Create and provision an IoT Edge device with a virtual TPM on a Linux virtual machine

Azure IoT Edge devices can be automatically provisioned using the [Device Provisioning Service](../iot-dps/index.yml). Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* Creare una macchina virtuale (VM) Linux in Hyper-V con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS and can only be used to create individual, not group, enrollments.

> [!TIP]
> This article describes how to test DPS provisioning using a TPM simulator, but much of it applies to physical TPM hardware such as the [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), an Azure Certified for IoT device.
>
> If you're using a physical device, you can skip ahead to the [Retrieve provisioning information from a physical device](#retrieve-provisioning-information-from-a-physical-device) section in this article.

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo Windows con [Hyper-V abilitato](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Questo articolo usa Windows 10 in cui è in esecuzione una macchina virtuale Ubuntu Server.
* Un hub IoT attivo.
* If using a simulated TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 or later with the ['Desktop development with C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) workload enabled.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Creare una macchina virtuale Linux con un TPM virtuale

In this section, you create a new Linux virtual machine on Hyper-V. You configured this virtual machine with a simulated TPM so that you can use it for testing how automatic provisioning works with IoT Edge. 

### <a name="create-a-virtual-switch"></a>Creare un commutatore virtuale

Un commutatore virtuale consente alla macchina virtuale di connettersi a una rete fisica.

1. Open Hyper-V Manager on your Windows machine. 

2. Nel menu **Azioni** selezionare **Gestione commutatori virtuali**. 

3. Scegliere un commutatore virtuale **esterno**, quindi selezionare **Crea commutatore virtuale**. 

4. Assegnare un nome al nuovo commutatore virtuale, ad esempio **EdgeSwitch**. Verificare che il tipo di connessione sia impostato su **Rete esterna**, quindi selezionare **Ok**.

5. Un elemento pop-up avvisa l'utente che la connettività di rete potrebbe essere interrotta. Selezionare **Yes** (Sì) per continuare. 

Se si verificano errori in fase di creazione del nuovo commutatore virtuale, assicurarsi che nessun altro commutatore stia usando l'adattatore Ethernet e che nessun altro commutatore usi lo stesso nome. 

### <a name="create-virtual-machine"></a>Crea macchina virtuale

1. Scaricare un file di immagine del disco da usare per la macchina virtuale e salvarlo in locale. Esempio: [Ubuntu Server](https://www.ubuntu.com/download/server). 

2. In Hyper-V Manager again, select **New** > **Virtual Machine** in the **Actions** menu.

3. Completare la **Creazione guidata macchina virtuale** con le configurazioni specifiche seguenti:

   1. **Impostazione generazione**: selezionare **Generazione 2**. Generation 2 virtual machines have nested virtualization enabled, which is required to run IoT Edge on a virtual machine.
   2. **Configura rete**: impostare il valore di **Connessione** sul commutatore virtuale creato nella sezione precedente. 
   3. **Opzioni di installazione**: selezionare **Installa un sistema operativo da un file immagine di avvio** e individuare il file di immagine del disco che è stato salvato in locale.

4. Select **Finish** in the wizard to create the virtual machine.

La creazione della macchina virtuale può richiedere alcuni minuti. 

### <a name="enable-virtual-tpm"></a>Abilitare il TPM virtuale

Once your VM is created, open its settings to enable the virtual trusted platform module (TPM) that lets you auto-provision the device.

1. Select the virtual machine, then open its **Settings**.

2. Passare a **Sicurezza**. 

3. Deselezionare l'opzione **Abilita avvio protetto**.

4. Selezionare **Abilita Trusted Platform Module**. 

5. Fare clic su **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Avviare la macchina virtuale e raccogliere i dati del modulo TPM

In the virtual machine, build a tool that you can use to retrieve the device's **Registration ID** and **Endorsement key**.

1. Start your virtual machine and connect to it.

1. Follow the prompts within the virtual machine to finish the installation process and reboot the machine.

1. Sign in to your VM, then follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

   >[!TIP]
   >In the course of this article, you'll copy to and paste from the virtual machine, which is not easy through the Hyper-V Manager connection application. You may want to connect to the virtual machine through Hyper-V Manager once to retrieve its IP address: `ifconfig`. Then, you can use the IP address to connect through SSH: `ssh <username>@<ipaddress>`.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM simulator.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. From a command window, navigate to the `azure-iot-sdk-c` directory and run the TPM simulator. che è in ascolto di un socket sulle porte 2321 e 2322. Do not close this command window; you will need to keep this simulator running.

   From the `azure-iot-sdk-c` directory, run the following command to start the simulator:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Using Visual Studio, open the solution generated in the `cmake` directory named `azure_iot_sdks.sln`, and build it using the **Build solution** command on the **Build** menu.

1. Nel riquadro **Esplora soluzioni** di Visual Studio passare alla cartella **Provision\_Tools**. Fare clic con il pulsante destro del mouse sul progetto **tpm_device_provision** e scegliere **Imposta come progetto di avvio**.

1. Run the solution using either of the **Start** commands on the **Debug** menu. The output window displays the TPM simulator's **Registration ID** and the **Endorsement key**, which you should copy for use later when you create an individual enrollment for your device in You can close this window (with Registration ID and Endorsement key), but leave the TPM simulator window running.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Retrieve provisioning information from a physical device

On your device, build a tool that you can use to retrieve the device's provisioning information.

1. Follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM device.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copy the values for **Registration ID** and **Endorsement key**. Usare questi valori per creare una registrazione singola per il dispositivo nel servizio Device Provisioning.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Creare una registrazione nel servizio Device Provisioning

Recuperare le informazioni di provisioning dalla macchina virtuale e usarle per creare una registrazione singola nel servizio Device Provisioning. 

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md). 

1. In the [Azure portal](https://portal.azure.com), navigate to your instance of IoT Hub Device Provisioning Service. 

2. In **le impostazioni** selezionare **Gestisci registrazioni**. 

3. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   1. In **Meccanismo** selezionare **TPM**. 

   2. Provide the **Endorsement key** and **Registration ID** that you copied from your virtual machine.

      > [!TIP]
      > If you're using a physical TPM device, you need to determine the **Endorsement key**, which is unique to each TPM chip and is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.

   3. Select **True** to declare that this virtual machine is an IoT Edge device. 

   4. Scegliere l'**hub IoT** collegato a cui si vuole connettere il dispositivo. You can choose multiple hubs, and the device will be assigned to one of them according to the selected allocation policy. 

   5. Se si desidera, specificare un ID per il dispositivo. È possibile usare gli ID dispositivo per identificare come destinazione un singolo dispositivo per la distribuzione di moduli. If you don't provide a device ID, the registration ID is used.

   6. Aggiungere un valore di tag allo **stato dispositivo gemello iniziale**, se si desidera. È possibile usare tag per identificare come destinazione gruppi di dispositivi per la distribuzione di moduli. ad esempio: 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Selezionare **Salva**. 

Now that an enrollment exists for this device, the IoT Edge runtime can automatically provision the device during installation. 

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale. Installare il runtime IoT Edge nella macchina virtuale. 

Prima di iniziare l'articolo per il tipo di dispositivo in uso, assicurarsi di conoscere il valore di **Ambito ID** del servizio Device Provisioning e il valore di **ID registrazione** del dispositivo. Se è installato il server di esempio Ubuntu, usare le istruzioni per **x64**. Assicurarsi di configurare il runtime IoT Edge per il provisioning automatico, non manuale. 

[Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Concedere l'accesso IoT Edge al TPM

Affinché possa eseguire il provisioning automaticamente del dispositivo, il runtime IoT Edge necessita dell'accesso al TPM. 

È possibile concedere al modulo TPM l'accesso al runtime di IoT Edge eseguendo l'override delle impostazioni di sistema, in modo che il servizio **iotedge** disponga di privilegi a livello radice. Se non si vuole elevare i privilegi di servizio, è possibile usare la procedura seguente per concedere manualmente l'accesso al modulo TPM. 

1. Trovare il percorso file al modulo hardware TPM sul dispositivo e salvarlo come variabile locale. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Creare una nuova regola che consentirà l'accesso del runtime IoT Edge a tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Aprire il file delle regole. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copiare le informazioni di accesso seguenti nel file di regole. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Salvare e chiudere il file. 

6. Attivare il sistema udev per valutare la nuova regola. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Verificare che la regola sia stata applicata.

   ```bash
   ls -l /dev/tpm0
   ```

   L'output dell'operazione riuscita è simile al seguente:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se si riscontra che non state applicate le autorizzazioni corrette, provare a riavviare la macchina per aggiornare udev. 

## <a name="restart-the-iot-edge-runtime"></a>Riavviare il runtime IoT Edge

Riavviare il runtime IoT Edge in modo che accetti tutte le modifiche alla configurazione apportate nel dispositivo. 

   ```bash
   sudo systemctl restart iotedge
   ```

Verificare che il runtime IoT Edge sia in esecuzione. 

   ```bash
   sudo systemctl status iotedge
   ```

Se vengono visualizzati errori di provisioning, è possibile che le modifiche di configurazione non abbiano ancora avuto effetto. Provare a riavviare di nuovo il daemon IoT Edge. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
In alternativa, provare a riavviare la macchina virtuale per verificare se le modifiche diventano effettive al nuovo avvio. 

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se il runtime viene avviato correttamente, è possibile accedere all'hub IoT e verificare se è stato eseguito il provisioning automatico del nuovo dispositivo. Il dispositivo è ora è pronto per l'esecuzione di moduli IoT Edge. 

Verificare lo stato del daemon IoT Edge.

```cmd/sh
systemctl status iotedge
```

Esaminare i log del daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Elencare i moduli in esecuzione.

```cmd/sh
iotedge list
```

You can verify that the individual enrollment that you created in Device Provisioning Service was used. Navigate to your Device Provisioning Service instance in the Azure portal. Open the enrollment details for the individual enrollment that you created. Notice that the status of the enrollment is **assigned** and the device ID is listed. 

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come [Distribuire e monitorare i moduli IoT Edge su larga scala tramite il portale di Azure](how-to-deploy-monitor.md) o usando l'[interfaccia della riga di comando di Azure](how-to-deploy-monitor-cli.md).
