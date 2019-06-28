---
title: Eseguire automaticamente il provisioning di dispositivi Linux con DPS - Azure IoT Edge | Microsoft Docs
description: Usare un TPM simulato in una macchina virtuale Linux per testare il servizio Device Provisioning di Azure per Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f75ad287b5f026dea7ba0152b009a60572929148
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329633"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Creare ed effettuare il provisioning di un dispositivo IoT Edge con un TPM virtuale in una macchina virtuale Linux

Dispositivi Azure IoT Edge possono eseguire automaticamente il provisioning usando il [il servizio Device Provisioning](../iot-dps/index.yml). Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare. 

Questo articolo illustra come testare provisioning automatico in un dispositivo IoT Edge simulato con i passaggi seguenti: 

* Creare una macchina virtuale (VM) Linux in Hyper-V con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

I passaggi descritti in questo articolo sono studiati per finalità di test.

## <a name="prerequisites"></a>prerequisiti

* Un computer di sviluppo Windows con [Hyper-V abilitato](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Questo articolo usa Windows 10 in cui è in esecuzione una macchina virtuale Ubuntu Server. 
* Un hub IoT attivo. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Creare una macchina virtuale Linux con un TPM virtuale

In questa sezione si crea una nuova macchina virtuale Linux in Hyper-V. Questa macchina virtuale è configurata con un TPM simulato in modo che è possibile usarlo per il test di funzionamento del provisioning automatico con IoT Edge. 

### <a name="create-a-virtual-switch"></a>Creare un commutatore virtuale

Un commutatore virtuale consente alla macchina virtuale di connettersi a una rete fisica.

1. Aprire Gestione Hyper-V nel computer Windows. 

2. Nel menu **Azioni** selezionare **Gestione commutatori virtuali**. 

3. Scegliere un commutatore virtuale **esterno**, quindi selezionare **Crea commutatore virtuale**. 

4. Assegnare un nome al nuovo commutatore virtuale, ad esempio **EdgeSwitch**. Verificare che il tipo di connessione sia impostato su **Rete esterna**, quindi selezionare **Ok**.

5. Un elemento pop-up avvisa l'utente che la connettività di rete potrebbe essere interrotta. Selezionare **Yes** (Sì) per continuare. 

Se si verificano errori in fase di creazione del nuovo commutatore virtuale, assicurarsi che nessun altro commutatore stia usando l'adattatore Ethernet e che nessun altro commutatore usi lo stesso nome. 

### <a name="create-virtual-machine"></a>Crea macchina virtuale

1. Scaricare un file di immagine del disco da usare per la macchina virtuale e salvarlo in locale. Esempio: [Ubuntu Server](https://www.ubuntu.com/download/server). 

2. Gestione di Hyper-V, selezionare di nuovo **New** > **macchina virtuale** nel **azioni** menu.

3. Completare la **Creazione guidata macchina virtuale** con le configurazioni specifiche seguenti:

   1. **Impostazione generazione**: Selezionare **Generazione 2**. Macchine virtuali di generazione 2 sono annidati virtualizzazione abilitata, che è necessario per l'esecuzione di IoT Edge in una macchina virtuale.
   2. **Configura rete**: impostare il valore di **Connessione** sul commutatore virtuale creato nella sezione precedente. 
   3. **Opzioni di installazione**: selezionare **Installa un sistema operativo da un file immagine di avvio** e individuare il file di immagine del disco che è stato salvato in locale.

4. Selezionare **fine** della procedura guidata per creare la macchina virtuale.

La creazione della macchina virtuale può richiedere alcuni minuti. 

### <a name="enable-virtual-tpm"></a>Abilitare il TPM virtuale

Dopo aver creata la macchina virtuale, aprire le impostazioni per abilitare il modulo virtuale attendibili della piattaforma (TPM) che consente di autoprovision del dispositivo. 

1. Selezionare la macchina virtuale, quindi aprire il **impostazioni**.

2. Passare a **Sicurezza**. 

3. Deselezionare l'opzione **Abilita avvio protetto**.

4. Selezionare **Abilita Trusted Platform Module**. 

5. Fare clic su **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Avviare la macchina virtuale e raccogliere i dati del modulo TPM

Nella macchina virtuale compilare uno strumento C SDK che è possibile usare per recuperare i valori di **ID registrazione** e **Chiave di approvazione** del dispositivo. 

1. Avviare la macchina virtuale e connettersi a esso.

2. Seguire le istruzioni all'interno della macchina virtuale per completare il processo di installazione e riavviare il computer. 

3. Accedere alla macchina virtuale, quindi seguire i passaggi descritti in [configurare un ambiente di sviluppo Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) per installare e creare il dispositivo Azure IoT SDK per C. 

   >[!TIP]
   >Nel corso di questo articolo, sarà copiare e incollare dalla macchina virtuale, che non è facile tramite l'applicazione di connessione di gestione di Hyper-V. È possibile connettersi alla macchina virtuale tramite Hyper-V Manager una sola volta per recuperare il relativo indirizzo IP: `ifconfig`. Quindi, è possibile usare l'indirizzo IP per connettersi tramite SSH: `ssh <username>@<ipaddress>`.

4. Eseguire i comandi seguenti per creare uno strumento C SDK che consenta di recuperare le informazioni di provisioning del dispositivo. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```
   >[!TIP]
   >Se si sta testando con il simulatore TPM, è necessario inserire un parametro aggiuntivo `-Duse_tpm_simulator:BOOL=ON` per abilitarlo. Il comando completo sarà `cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON .. `.

5. Copiare i valori per **ID registrazione** e **Chiave di approvazione**. Usare questi valori per creare una registrazione singola per il dispositivo nel servizio Device Provisioning. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Creare una registrazione nel servizio Device Provisioning

Recuperare le informazioni di provisioning dalla macchina virtuale e usarle per creare una registrazione singola nel servizio Device Provisioning. 

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md). 


1. Nel [portale di Azure](https://portal.azure.com) passare all'istanza del servizio Device Provisioning in hub IoT. 

2. In **le impostazioni** selezionare **Gestisci registrazioni**. 

3. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   1. In **Meccanismo** selezionare **TPM**. 
   
   2. Fornire il **chiave di verifica dell'autenticità** e **ID registrazione** copiato dalla macchina virtuale.
   
   3. Selezionare **True** per dichiarare che la macchina virtuale è un dispositivo IoT Edge. 
   
   4. Scegliere l'**hub IoT** collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato uno di essi in base al criterio di allocazione selezionato. 
   
   5. Se si desidera, specificare un ID per il dispositivo. È possibile usare gli ID dispositivo per identificare come destinazione un singolo dispositivo per la distribuzione di moduli. Se non si specifica un ID dispositivo, viene utilizzato l'ID di registrazione.
   
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

Ora che è presente una registrazione per questo dispositivo, il runtime di IoT Edge può eseguire automaticamente il provisioning del dispositivo durante l'installazione. 

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale. Installare il runtime IoT Edge nella macchina virtuale. 

Prima di iniziare l'articolo per il tipo di dispositivo in uso, assicurarsi di conoscere il valore di **Ambito ID** del servizio Device Provisioning e il valore di **ID registrazione** del dispositivo. Se è installato il server di esempio Ubuntu, usare le istruzioni per **x64**. Assicurarsi di configurare il runtime IoT Edge per il provisioning automatico, non manuale. 

* [Installare il runtime di Azure IoT Edge in Linux (x64)](how-to-install-iot-edge-linux.md)
* [Installare il runtime di Azure IoT Edge in Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

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

8. Aprire il file di override del runtime IoT Edge. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Aggiungere il codice seguente per definire una variabile di ambiente del TPM.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

10. Salvare e chiudere il file.

11. Verificare che la sostituzione sia stata eseguita correttamente.

    ```bash
    sudo systemctl cat iotedge.service
    ```

    L'output positivo visualizza le variabili di servizio predefinite di **iotedge** e quindi visualizza la variabile di ambiente impostata in **override.conf**. 

12. Ricaricare le impostazioni.

    ```bash
    sudo systemctl daemon-reload
    ```

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

È possibile verificare che sia stata usata la singola registrazione creato nel servizio Device Provisioning. Passare all'istanza del servizio Device Provisioning nel portale di Azure. Aprire i dettagli della registrazione per la registrazione singola che è stato creato. Si noti che lo stato della registrazione viene **assegnati** e il dispositivo è elencato l'ID. 

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come [Distribuire e monitorare i moduli IoT Edge su larga scala tramite il portale di Azure](how-to-deploy-monitor.md) o usando l'[interfaccia della riga di comando di Azure](how-to-deploy-monitor-cli.md).
