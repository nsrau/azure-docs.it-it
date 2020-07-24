---
title: Eseguire il provisioning di un dispositivo con un TPM virtuale in una VM Linux-Azure IoT Edge
description: Usare un TPM simulato in una macchina virtuale Linux per testare il servizio Device Provisioning di Azure per Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cb1511736b993032df8ca52fac01243f1feafdf4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092388"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Creare ed effettuare il provisioning di un dispositivo IoT Edge con un TPM in Linux

Questo articolo illustra come testare il provisioning automatico in un dispositivo IoT Edge Linux usando un Trusted Platform Module (TPM). È possibile effettuare automaticamente il provisioning di Azure IoT Edge dispositivi con il [servizio Device provisioning](../iot-dps/index.yml). Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

Le attività sono le seguenti:

1. Creare una macchina virtuale (VM) Linux in Hyper-V con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
1. Creare un'istanza del servizio Device Provisioning in hub IoT.
1. Creare una singola registrazione per il dispositivo.
1. Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

> [!TIP]
> Questo articolo descrive come testare il provisioning DPS usando un simulatore TPM, ma la maggior parte di esso si applica a hardware TPM fisico, ad esempio [Infineon OPTIGA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), un dispositivo Azure Certified for Internet.
>
> Se si usa un dispositivo fisico, è possibile passare alla sezione recuperare le [informazioni di provisioning da un dispositivo fisico](#retrieve-provisioning-information-from-a-physical-device) in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo Windows con [Hyper-V abilitato](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Questo articolo usa Windows 10 in cui è in esecuzione una macchina virtuale Ubuntu Server.
* Un hub IoT attivo.

> [!NOTE]
> Il TPM 2,0 è obbligatorio quando si usa l'attestazione TPM con DPS e può essere usato solo per creare registrazioni individuali, non di gruppo.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Creare una macchina virtuale Linux con un TPM virtuale

In questa sezione viene creata una nuova macchina virtuale Linux in Hyper-V. Questa macchina virtuale viene configurata con un TPM simulato per testare il funzionamento del provisioning automatico con IoT Edge.

### <a name="create-a-virtual-switch"></a>Creare un commutatore virtuale

Un commutatore virtuale consente alla macchina virtuale di connettersi a una rete fisica.

1. Aprire la console di gestione di Hyper-V nel computer Windows.

2. Nel menu **Azioni** selezionare **Gestione commutatori virtuali**.

3. Scegliere un commutatore virtuale **esterno**, quindi selezionare **Crea commutatore virtuale**.

4. Assegnare un nome al nuovo commutatore virtuale, ad esempio **EdgeSwitch**. Verificare che il tipo di connessione sia impostato su **Rete esterna**, quindi selezionare **Ok**.

5. Un elemento pop-up avvisa l'utente che la connettività di rete potrebbe essere interrotta. Selezionare **Sì** per continuare.

Se si verificano errori in fase di creazione del nuovo commutatore virtuale, assicurarsi che nessun altro commutatore stia usando l'adattatore Ethernet e che nessun altro commutatore usi lo stesso nome.

### <a name="create-virtual-machine"></a>Creare macchina virtuale

1. Scaricare un file di immagine del disco da usare per la macchina virtuale e salvarlo in locale. Ad esempio, [Ubuntu server 18,04](http://releases.ubuntu.com/18.04.4/). Per informazioni sui sistemi operativi supportati per i dispositivi IoT Edge, vedere [Azure IOT Edge sistemi supportati](support.md).

2. Nella console di gestione di Hyper-V **Action**selezionare  >  **nuova**azione  >  **macchina virtuale** dal menu **azioni** .

3. Completare la **Creazione guidata macchina virtuale** con le configurazioni specifiche seguenti:

   1. **Impostazione generazione**: selezionare **Generazione 2**. Per le macchine virtuali di seconda generazione è abilitata la virtualizzazione nidificata, necessaria per eseguire IoT Edge in una macchina virtuale.
   2. **Configura rete**: impostare il valore di **Connessione** sul commutatore virtuale creato nella sezione precedente.
   3. **Opzioni di installazione**: selezionare **Installa un sistema operativo da un file immagine di avvio** e individuare il file di immagine del disco che è stato salvato in locale.

4. Selezionare **fine** nella procedura guidata per creare la macchina virtuale.

La creazione della macchina virtuale può richiedere alcuni minuti.

### <a name="enable-virtual-tpm"></a>Abilitare il TPM virtuale

Una volta creata la macchina virtuale, aprire le impostazioni per abilitare il modulo TPM (Trusted Platform Module) virtuale che consente di effettuare il provisioning automatico del dispositivo.

1. Nella console di gestione di Hyper-V fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Impostazioni**.

2. Passare a **Sicurezza**.

3. Deselezionare l'opzione **Abilita avvio protetto**.

4. Selezionare **Abilita Trusted Platform Module**.

5. Fare clic su **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Avviare la macchina virtuale e raccogliere i dati del modulo TPM

Nella macchina virtuale creare uno strumento che è possibile usare per recuperare l' **ID di registrazione** e la **chiave**di verifica dell'autenticità del dispositivo.

1. Nella console di gestione di Hyper-V, avviare la macchina virtuale e connettersi.

1. Seguire le istruzioni all'interno della macchina virtuale per completare il processo di installazione e riavviare il computer.

1. Accedere alla macchina virtuale, quindi seguire la procedura descritta in [configurare un ambiente di sviluppo Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) per installare e compilare l'SDK per dispositivi Azure per dispositivi per C.

   >[!TIP]
   >Nel corso di questo articolo verranno copiati e incollati nella macchina virtuale, che non è facile tramite l'applicazione di connessione alla console di gestione di Hyper-V. Potrebbe essere necessario connettersi alla macchina virtuale tramite la console di gestione di Hyper-V per recuperare il relativo indirizzo IP. Eseguire prima `sudo apt install net-tools` e quindi `hostname -I` . Quindi, è possibile usare l'indirizzo IP per connettersi tramite SSH: `ssh <username>@<ipaddress>` .

1. Eseguire i comandi seguenti per compilare lo strumento SDK che recupera le informazioni sul provisioning del dispositivo dal TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. La finestra output Visualizza l' **ID registrazione** del dispositivo e la **chiave**di verifica dell'autenticità. Copiare questi valori per usarli in seguito quando si crea una registrazione singola per il dispositivo.

Dopo aver ottenuto l'ID registrazione e la chiave di verifica dell'autenticità, passare alla sezione [configurare il servizio Device provisioning in hub](#set-up-the-iot-hub-device-provisioning-service) Internet

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Recuperare le informazioni di provisioning da un dispositivo fisico

Se si usa un dispositivo di IoT Edge fisico anziché una VM, compilare uno strumento che è possibile usare per recuperare le informazioni sul provisioning del dispositivo.

1. Seguire i passaggi descritti in [configurare un ambiente di sviluppo Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) per installare e compilare l'SDK per dispositivi Azure per dispositivi per C.

1. Eseguire i comandi seguenti per compilare lo strumento SDK che recupera le informazioni sul provisioning dei dispositivi dal dispositivo TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copiare i valori per l' **ID registrazione** e la **chiave**di verifica dell'autenticità. Usare questi valori per creare una registrazione singola per il dispositivo nel servizio Device Provisioning.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="create-a-dps-enrollment"></a>Creare una registrazione nel servizio Device Provisioning

Recuperare le informazioni di provisioning dalla macchina virtuale e usarle per creare una registrazione singola nel servizio Device Provisioning.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-at-scale.md).

> [!TIP]
> Nell'interfaccia della riga di comando di Azure è possibile creare una [registrazione](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) e usare il flag **abilitato per Edge** per specificare che un dispositivo è un dispositivo IOT Edge.

1. Nella [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device provisioning in hub Internet.

2. In **le impostazioni** selezionare **Gestisci registrazioni**.

3. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   1. In **Meccanismo** selezionare **TPM**.

   2. Fornire la **chiave** di verifica dell'autenticità e l' **ID di registrazione** copiati dalla macchina virtuale.

      > [!TIP]
      > Se si usa un dispositivo TPM fisico, è necessario determinare la **chiave**di verifica dell'autenticità, che è univoca per ogni chip TPM ed è ottenuta dal produttore del chip TPM associato. È possibile derivare un **ID di registrazione** univoco per il dispositivo TPM, ad esempio creando un hash SHA-256 della chiave di verifica dell'autenticità.

   3. Se si desidera, specificare un ID per il dispositivo. Se non si specifica un ID dispositivo, viene usato l'ID di registrazione.

   4. Selezionare **true** per dichiarare che questa macchina virtuale è un dispositivo IOT Edge.

   5. Scegliere l'hub Internet degli altri collegamenti a cui si vuole connettere il dispositivo oppure selezionare **collega a nuovo hub**Internet. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di assegnazione selezionati.

   6. Aggiungere un valore di tag allo **stato dispositivo gemello iniziale**, se si desidera. È possibile usare tag per identificare come destinazione gruppi di dispositivi per la distribuzione di moduli. Per altre informazioni, vedere [distribuire moduli IOT Edge su larga scala](how-to-deploy-at-scale.md).

   7. Selezionare **Salva**.

Ora che è presente una registrazione per questo dispositivo, il runtime di IoT Edge può effettuare automaticamente il provisioning del dispositivo durante l'installazione.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale. Installare il runtime IoT Edge nella macchina virtuale.

Prima di iniziare l'articolo per il tipo di dispositivo in uso, assicurarsi di conoscere il valore di **Ambito ID** del servizio Device Provisioning e il valore di **ID registrazione** del dispositivo. Se è installato il server di esempio Ubuntu, usare le istruzioni per **x64**. Assicurarsi di configurare il runtime IoT Edge per il provisioning automatico, non manuale.

Quando si arriva al passaggio per configurare il daemon di sicurezza, assicurarsi e scegliere l' [opzione 2 provisioning automatico](how-to-install-iot-edge-linux.md#option-2-automatic-provisioning) e configurare per l'attestazione TPM.

[Installare il runtime di Azure IoT Edge in Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Concedere l'accesso IoT Edge al TPM

Il runtime di IoT Edge deve accedere al TPM per effettuare automaticamente il provisioning del dispositivo.

È possibile concedere l'accesso TPM al runtime di IoT Edge eseguendo l'override delle impostazioni di sistema in modo che il `iotedge` servizio disponga dei privilegi radice. Se non si vuole elevare i privilegi di servizio, è possibile usare la procedura seguente per concedere manualmente l'accesso al modulo TPM.

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
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
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

   L'output con esito positivo viene visualizzato come segue:

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

È possibile verificare che sia stata usata la registrazione singola creata nel servizio Device provisioning. Passare all'istanza del servizio Device provisioning nel portale di Azure. Aprire i dettagli di registrazione per la registrazione singola creata. Si noti che lo stato della registrazione viene **assegnato** e viene elencato l'ID del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione DPS consente di impostare l'ID del dispositivo e i tag del dispositivo gemello allo stesso tempo in cui si esegue il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come [distribuire e monitorare IOT Edge moduli su larga scala usando l'portale di Azure o l'interfaccia della](how-to-deploy-at-scale.md) riga di comando di [Azure](how-to-deploy-cli-at-scale.md).
