---
title: Effettuare il provisioning del dispositivo con un TPM virtuale in una macchina virtuale Linux - Azure IoT Edge
description: Usare un TPM simulato in una macchina virtuale Linux per testare il servizio Device Provisioning di Azure per Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511060"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Creare ed eseguire il provisioning di un dispositivo IoT Edge con un TPM virtuale in una macchina virtuale LinuxCreate and provision an IoT Edge device with a virtual TPM on a Linux virtual machine

È possibile eseguire automaticamente il provisioning dei dispositivi Azure IoT Edge usando il [servizio Device Provisioning.](../iot-dps/index.yml) Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

Questo articolo illustra come testare il provisioning automatico in un dispositivo IoT Edge simulato con i passaggi seguenti:This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* Creare una macchina virtuale (VM) Linux in Hyper-V con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

> [!TIP]
> In questo articolo viene descritto come testare il provisioning DPS utilizzando un simulatore TPM, ma gran parte di esso si applica all'hardware TPM fisico, ad esempio [infine, OPTIGA&trade; TPM,](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)un dispositivo Azure Certified for IoT.
>
> Se si usa un dispositivo fisico, è possibile passare alla sezione Recuperare informazioni di [provisioning da un dispositivo fisico](#retrieve-provisioning-information-from-a-physical-device) in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo Windows con [Hyper-V abilitato](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Questo articolo usa Windows 10 in cui è in esecuzione una macchina virtuale Ubuntu Server.
* Un hub IoT attivo.
* Se si usa un TPM simulato, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 o versione successiva con il carico di lavoro ['Sviluppo desktop con C ''.](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)

> [!NOTE]
> Il TPM 2.0 è necessario quando si utilizza l'attestazione TPM con DPS e può essere utilizzato solo per creare registrazioni singole, non di gruppo.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Creare una macchina virtuale Linux con un TPM virtuale

In questa sezione viene creata una nuova macchina virtuale Linux in Hyper-V. You configured this virtual machine with a simulated TPM so that you can use it for testing how automatic provisioning works with IoT Edge.

### <a name="create-a-virtual-switch"></a>Creare un commutatore virtuale

Un commutatore virtuale consente alla macchina virtuale di connettersi a una rete fisica.

1. Aprire la console di gestione di Hyper-V nel computer Windows.

2. Nel menu **Azioni** selezionare **Gestione commutatori virtuali**.

3. Scegliere un commutatore virtuale **esterno**, quindi selezionare **Crea commutatore virtuale**.

4. Assegnare un nome al nuovo commutatore virtuale, ad esempio **EdgeSwitch**. Verificare che il tipo di connessione sia impostato su **Rete esterna**, quindi selezionare **Ok**.

5. Un elemento pop-up avvisa l'utente che la connettività di rete potrebbe essere interrotta. Selezionare **Yes** (Sì) per continuare.

Se si verificano errori in fase di creazione del nuovo commutatore virtuale, assicurarsi che nessun altro commutatore stia usando l'adattatore Ethernet e che nessun altro commutatore usi lo stesso nome.

### <a name="create-virtual-machine"></a>Creare macchina virtuale

1. Scaricare un file di immagine del disco da usare per la macchina virtuale e salvarlo in locale. Esempio: [Ubuntu Server](https://www.ubuntu.com/download/server).

2. Nella console di gestione di Hyper-V selezionare **nuovamente Nuova** > **macchina virtuale** dal menu **Azioni.**

3. Completare la **Creazione guidata macchina virtuale** con le configurazioni specifiche seguenti:

   1. **Impostazione generazione**: selezionare **Generazione 2**. Nelle macchine virtuali di generazione 2 è abilitata la virtualizzazione annidata, necessaria per eseguire Edge IoT in una macchina virtuale.
   2. **Configura rete**: impostare il valore di **Connessione** sul commutatore virtuale creato nella sezione precedente.
   3. **Opzioni di installazione**: selezionare **Installa un sistema operativo da un file immagine di avvio** e individuare il file di immagine del disco che è stato salvato in locale.

4. Selezionare **Fine** nella procedura guidata per creare la macchina virtuale.

La creazione della macchina virtuale può richiedere alcuni minuti.

### <a name="enable-virtual-tpm"></a>Abilitare il TPM virtuale

Dopo aver creato la macchina virtuale, aprire le relative impostazioni per abilitare il modulo TPM (Virtual Trusted Platform Module) che consente di eseguire il provisioning automatico del dispositivo.

1. Selezionare la macchina virtuale, quindi aprire le relative **impostazioni**.

2. Passare a **Sicurezza**.

3. Deselezionare l'opzione **Abilita avvio protetto**.

4. Selezionare **Abilita Trusted Platform Module**.

5. Fare clic su **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Avviare la macchina virtuale e raccogliere i dati del modulo TPM

Nella macchina virtuale compilare uno strumento che è possibile utilizzare per recuperare **l'ID di registrazione** e la chiave di **approvazione**del dispositivo.

1. Avviare la macchina virtuale e connettersi a essa.

1. Seguire le istruzioni all'interno della macchina virtuale per completare il processo di installazione e riavviare la macchina.

1. Accedere alla macchina virtuale, quindi seguire i passaggi descritti in [Configurare un ambiente](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) di sviluppo Linux per installare e creare l'SDK del dispositivo Azure IoT per C.Sign in to your VM, then follow the steps in Set up a Linux development environment to install and build the Azure IoT device SDK for C.

   >[!TIP]
   >Nel corso di questo articolo, si copierà e incolladalla macchina virtuale, che non è facile tramite l'applicazione di connessione Hyper-V Manager. È possibile connettersi alla macchina virtuale tramite la console di `ifconfig`gestione di Hyper-V una volta per recuperare il relativo indirizzo IP: . Quindi, è possibile utilizzare l'indirizzo IP `ssh <username>@<ipaddress>`per connettersi tramite SSH: .

1. Eseguire i comandi seguenti per compilare lo strumento SDK che recupera le informazioni sul provisioning del dispositivo dal simulatore TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Da una finestra di `azure-iot-sdk-c` comando, passare alla directory ed eseguire il simulatore TPM. che è in ascolto di un socket sulle porte 2321 e 2322. Non chiudere questa finestra di comando; sarà necessario mantenere questo simulatore in esecuzione.

   Dalla `azure-iot-sdk-c` directory, eseguire il comando seguente per avviare il simulatore:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Utilizzando Visual Studio, aprire la `cmake` soluzione `azure_iot_sdks.sln`generata nella directory denominata e compilarla utilizzando il comando **Compila soluzione** dal menu **Compila** .

1. Nel riquadro **Esplora soluzioni** di Visual Studio passare alla cartella **Provision\_Tools**. Fare clic con il pulsante destro del mouse sul progetto **tpm_device_provision** e scegliere **Imposta come progetto di avvio**.

1. Eseguire la soluzione utilizzando uno dei comandi **Start** del menu **Debug.** Nella finestra di output vengono visualizzati **l'ID di registrazione** del simulatore TPM e la chiave di **verifica,** che è necessario copiare per l'utilizzo in un secondo momento quando si crea una singola registrazione per il dispositivo in È possibile chiudere questa finestra (con ID registrazione e chiave di approvazione), ma lasciare la finestra del simulatore TPM in esecuzione.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Recuperare informazioni di provisioning da un dispositivo fisicoRetrieve provisioning information from a physical device

Nel dispositivo creare uno strumento che è possibile usare per recuperare le informazioni di provisioning del dispositivo.

1. Seguire i passaggi descritti in [Configurare un ambiente](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) di sviluppo Linux per installare e creare l'SDK del dispositivo Azure IoT per C.Follow the steps in Set up a Linux development environment to install and build the Azure IoT device SDK for C.

1. Eseguire i comandi seguenti per compilare lo strumento SDK che recupera le informazioni sul provisioning del dispositivo dal dispositivo TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copiare i valori per **ID registrazione** e Chiave **di approvazione**. Usare questi valori per creare una registrazione singola per il dispositivo nel servizio Device Provisioning.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="create-a-dps-enrollment"></a>Creare una registrazione nel servizio Device Provisioning

Recuperare le informazioni di provisioning dalla macchina virtuale e usarle per creare una registrazione singola nel servizio Device Provisioning.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md).

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio di provisioning dei dispositivi dell'hub IoT.In the Azure portal , navigate to your instance of IoT Hub Device Provisioning Service.

2. In **le impostazioni** selezionare **Gestisci registrazioni**.

3. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   1. In **Meccanismo** selezionare **TPM**.

   2. Specificare la **chiave di approvazione** e **l'ID di registrazione** copiati dalla macchina virtuale.

      > [!TIP]
      > Se si utilizza un dispositivo TPM fisico, è necessario determinare la **chiave di verifica,** che è univoca per ogni chip TPM e viene ottenuta dal produttore del chip TPM associato. È possibile derivare un **ID di registrazione** univoco per il dispositivo TPM, ad esempio creando un hash SHA-256 della chiave di approvazione.

   3. Selezionare True per dichiarare che questa macchina virtuale è un dispositivo Perimetralle IoT.Select **True** to declare that this virtual machine is an IoT Edge device.

   4. Scegliere l'**hub IoT** collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   5. Se si desidera, specificare un ID per il dispositivo. È possibile usare gli ID dispositivo per identificare come destinazione un singolo dispositivo per la distribuzione di moduli. Se non fornisci un ID dispositivo, viene utilizzato l'ID di registrazione.

   6. Aggiungere un valore di tag allo **stato dispositivo gemello iniziale**, se si desidera. È possibile usare tag per identificare come destinazione gruppi di dispositivi per la distribuzione di moduli. Ad esempio:

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

Ora che esiste una registrazione per questo dispositivo, il runtime di IoT Edge può eseguire automaticamente il provisioning del dispositivo durante l'installazione.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale. Installare il runtime IoT Edge nella macchina virtuale.

Prima di iniziare l'articolo per il tipo di dispositivo in uso, assicurarsi di conoscere il valore di **Ambito ID** del servizio Device Provisioning e il valore di **ID registrazione** del dispositivo. Se è installato il server di esempio Ubuntu, usare le istruzioni per **x64**. Assicurarsi di configurare il runtime IoT Edge per il provisioning automatico, non manuale.

[Installare il runtime di Azure IoT Edge in LinuxInstall the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)

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

È possibile verificare che sia stata utilizzata la singola registrazione creata in Device Provisioning Service. Passare all'istanza del servizio Device Provisioning nel portale di Azure.Navigate to your Device Provisioning Service instance in the Azure portal. Aprire i dettagli della registrazione per la singola registrazione creata. Si noti che lo stato della registrazione è **assegnato** e l'ID dispositivo è elencato.

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come distribuire e monitorare i [moduli di IoT Edge su larga scala usando il portale](how-to-deploy-monitor.md) di Azure o l'interfaccia della riga di comando di [Azure.](how-to-deploy-monitor-cli.md)
