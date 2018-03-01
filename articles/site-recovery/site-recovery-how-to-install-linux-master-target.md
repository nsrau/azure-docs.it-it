---
title: Come installare un server di destinazione master Linux per il failover da Azure a locale | Microsoft Docs
description: "Prima di eseguire la riprotezione di una macchina virtuale Linux, è necessario disporre di un server di destinazione master Linux. Di seguito viene descritto come installarlo."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 11/22/2017
ms.author: rajanaki
ms.openlocfilehash: 11f9385c1082011ee690f48f2579b6f3b156d125
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="install-a-linux-master-target-server"></a>Installare un server di destinazione master Linux
Dopo avere effettuato il failover delle macchine virtuali in Azure, è possibile eseguirne il failback nel sito locale. Per eseguire il failback, è necessario riproteggere la macchina virtuale da Azure al sito locale. A tale scopo, è necessario un server di destinazione master locale che riceva il traffico. 

Se quella protetta è una macchina virtuale Windows, è necessario un server di destinazione master Windows. Per una macchina virtuale Linux è necessario un server di destinazione master Linux. Per informazioni su come creare e installare server di destinazione master Linux, vedere la procedura riportata di seguito.

> [!IMPORTANT]
> A partire dalla versione 9.10.0 del server di destinazione master, il server di destinazione master più recente può essere installato solo in un server Ubuntu 16.04. Le nuove installazioni non sono consentite nei server CentOS6.6. È comunque possibile continuare ad aggiornare i server di destinazione master precedenti con la versione 9.10.0.

## <a name="overview"></a>Panoramica
Questo articolo contiene istruzioni per l'installazione di un server di destinazione master Linux.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>prerequisiti

* Per scegliere l'host in cui distribuire il server di destinazione master, determinare se il failback verrà eseguito in una macchina virtuale locale esistente o in una nuova. 
    * Se viene eseguito in una macchina virtuale esistente, l'host del server di destinazione master deve poter accedere agli archivi dati della macchina virtuale.
    * Se la macchina virtuale locale non esiste (in caso di ripristino del percorso alternativo), la macchina virtuale di failback viene creata nello stesso host del server di destinazione master. Per l'installazione del server di destinazione master è possibile scegliere qualsiasi host ESXi.
* Il server di destinazione master deve trovarsi in una rete in grado di comunicare con il server di elaborazione e il server di configurazione.
* La versione del server di destinazione master deve essere uguale o precedente a quella del server di elaborazione e del server di configurazione. Ad esempio, se la versione del server di configurazione è 9.4, la versione del server di destinazione master può essere 9.4 o 9.3 ma non 9.5.
* Il server di destinazione master può essere solo una macchina virtuale VMware e non un server fisico.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Linee guida per il ridimensionamento per la creazione del server di destinazione master

Creare il server di destinazione master in base alle linee guida per il ridimensionamento seguenti:
- **RAM**: almeno 6 GB
- **Dimensioni disco sistema operativo**: almeno 100 GB (per installare CentOS6.6)
- **Dimensioni disco aggiuntive per l'unità di conservazione**: 1 TB
- **Core CPU**: almeno 4 core

Sono supportati i kernel Ubuntu seguenti.


|Serie di kernel  |Supporta fino a  |
|---------|---------|
|4.4      |4.4.0-81-generico         |
|4.8      |4.8.0-56-generico         |
|4.10     |4.10.0-24-generico        |


## <a name="deploy-the-master-target-server"></a>Distribuire il server di destinazione master

### <a name="install-ubuntu-16042-minimal"></a>Installare Ubuntu 16.04.2 Minimal

Attenersi ai passaggi seguenti per installare il sistema operativo a 64 bit di Ubuntu 16.04.2.

1.   Andare al [collegamento per il download](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64), scegliere il mirror più vicino e scaricare un file ISO di Ubuntu 16.04.2 Minimal a 64 bit.
Mantenere l'ISO di Ubuntu 16.04.2 Minimal a 64 bit nell'unità DVD e avviare il sistema.

1.  Selezionare **English** (Inglese) come lingua preferita e premere **Invio**.
    
    ![Selezionare una lingua](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)
1. Selezionare **Install Ubuntu Server** (Installa server Ubuntu) e premere **Invio**.

    ![Selezionare Installa server Ubuntu](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

1.  Selezionare **English** (Inglese) come lingua preferita e premere **Invio**.

    ![Selezionare English (Inglese) come lingua preferita](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

1. Selezionare l'opzione appropriata nell'elenco di opzioni **Time Zone** (Fuso orario) e premere **Invio**.

    ![Selezionare il fuso orario corretto](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

1. Selezionare **No** (opzione predefinita) e premere **Invio**.

     ![Configurare la tastiera](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)
1. Selezionare **English (US)** (Inglese Stati Uniti) come paese di origine per la tastiera e premere **Invio**.

1. Selezionare **English (US)** (Inglese Stati Uniti) come layout per la tastiera e premere **Invio**.

1. Immettere il nome host del server nella casella **Hostname** (Nome host) e quindi fare clic su **Continue** (Continua).

1. Per creare un account utente, immettere il nome utente e quindi selezionare **Continue** (Continua).

      ![Crea un account utente](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

1. Immettere la password per il nuovo account utente e quindi selezionare **Continue** (Continua).

1.  Confermare la password per il nuovo utente e quindi selezionare **Continue** (Continua).

    ![Confermare le password](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

1.  Nella selezione successiva per la crittografia della home directory selezionare **No** (opzione predefinita) e premere **Invio**.

1. Se il fuso orario visualizzato è corretto, selezionare **Yes** (Sì) (opzione predefinita) e premere **Invio**. Per riconfigurare il fuso orario, selezionare **No**.

1. Tra le opzioni per il metodo di partizionamento selezionare **Guided - use entire disk** (Guidato - Usare l'intero disco) e premere **Invio**.

     ![Selezionare l'opzione per il metodo partizionamento](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

1.  Selezionare il disco appropriato tra le opzioni **Select disk to partition** (Selezionare il disco da partizionare) e premere **Invio**.

    ![Selezionare il disco](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

1.  Selezionare **Yes** (Sì) per scrivere le modifiche su disco e premere **Invio**.

1.  Nella selezione per la configurazione del proxy selezionare l'opzione predefinita, selezionare **Continue** (Continua) e premere **Invio**.

     ![Selezionare l'opzione predefinita](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

1.  Selezionare l'opzione **No automatic updates** (Aggiornamenti automatici non consentiti) nella selezione per la gestione degli aggiornamenti del sistema e premere **Invio**.

     ![Selezionare la modalità di gestione degli aggiornamenti](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

    > [!WARNING]
    > Dato che il server di destinazione master per Azure Site Recovery richiede una versione molto specifica di Ubuntu, è necessario assicurarsi che gli aggiornamenti del kernel siano disabilitati per la macchina virtuale. Se sono abilitati, eventuali aggiornamenti regolari causeranno malfunzionamenti del server di destinazione master. Assicurarsi di selezionare l'opzione **No automatic updates** (Aggiornamenti automatici non consentiti).

1.  Selezionare le opzioni predefinite. Per usare connessioni openSSH per SSH, selezionare l'opzione **OpenSSH server** (Server OpenSSH) e selezionare **Continue** (Continua).

    ![Selezionare il software](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

1. Nella selezione per l'installazione del caricatore di avvio GRUB selezionare **Yes** (Sì) e premere **Invio**.

1. Selezionare il dispositivo appropriato per l'installazione del caricatore di avvio (preferibilmente **/dev/sda**) e premere **Invio**.

1. Selezionare **Continue** (Continua) e premere **Invio** per completare l'installazione.

    ![Completare l'installazione](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

1. Al termine dell'installazione, accedere alla macchina virtuale con le nuove credenziali utente. (Fare riferimento al **Passaggio 10** per ulteriori informazioni.)

1. Eseguire i passaggi descritti nella schermata seguente per impostare la password dell'utente ROOT. Quindi, accedere come utente ROOT.

    ![Impostare la password dell'utente ROOT](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Configurare il computer come server di destinazione master

Per ottenere l'ID per ogni disco rigido SCSI in una macchina virtuale Linux, il parametro **disk.EnableUUID = TRUE** deve essere abilitato. Per abilitare il parametro, attenersi alla procedura seguente:

1. Arrestare la macchina virtuale.

2. Fare clic con il pulsante destro del mouse sulla voce della macchina virtuale nel riquadro a sinistra e quindi scegliere **Edit Settings**(Modifica impostazioni).

3. Scegliere la scheda **Options** (Opzioni).

4. Nel riquadro a sinistra, selezionare **Advanced** > **General** (Avanzate - Generale, quindi selezionare il pulsante **Configuration Parameters** (Parametri di configurazione) nella parte inferiore destra della schermata.

    ![Scheda Opzioni](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    L'opzione **Configuration Parameters** (Parametri di configurazione) è disponibile solo quando il computer è in esecuzione. Per attivare la scheda, arrestare la macchina virtuale.

5. Controllare se esiste già una riga con il valore **disk.EnableUUID**.

    - Se il valore esiste ed è impostato su **False**, modificarlo in **True**. (I valori non fanno distinzione tra maiuscole e minuscole.)

    - Se il valore è presente ed è impostato su **True**, selezionare **Cancel** (Annulla).

    - Se il valore non esiste, selezionare **Add Row**(Aggiungi riga).

    - Nella colonna del nome, aggiungere **disk.EnableUUID**, quindi impostare il valore su **TRUE**.

    ![Controllare se esiste già una riga con il valore disk.EnableUUID](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="disable-kernel-upgrades"></a>Disabilitare gli aggiornamenti del kernel

Dato che il server di destinazione master per Azure Site Recovery richiede una versione specifica di Ubuntu, verificare che gli aggiornamenti del kernel siano disabilitati per la macchina virtuale. Se gli aggiornamenti del kernel sono abilitati, possono causare malfunzionamenti del server di destinazione master.

#### <a name="download-and-install-additional-packages"></a>Scaricare e installare i pacchetti aggiuntivi

> [!NOTE]
> Verificare di disporre della connettività Internet per scaricare e installare pacchetti aggiuntivi. Se non si dispone di connettività Internet, è necessario trovare manualmente i pacchetti RPM e installarli.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Ottenere il programma di installazione per l'installazione

Se il server di destinazione master dispone della connettività Internet, è possibile attenersi alla procedura seguente per scaricare il programma di installazione. In caso contrario, copiare il programma di installazione dal server di elaborazione e installarlo.

#### <a name="download-the-master-target-installation-packages"></a>Scaricare i pacchetti di installazione del server di destinazione master

[Scaricare i bit di installazione più recenti del server di destinazione master Linux](https://aka.ms/latestlinuxmobsvc).

Per scaricarli usando Linux, digitare:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Verificare di scaricare e decomprimere il programma di installazione nella home directory. Se il file viene decompresso nel percorso **/usr/Locale**, l'installazione avrà esito negativo.


#### <a name="access-the-installer-from-the-process-server"></a>Accedere al programma di installazione dal server di elaborazione

1. Sul server di elaborazione passare alla directory **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Copiare il file del programma di installazione necessario dal server di elaborazione e salvarlo come **latestlinuxmobsvc.tar.gz** nella home directory.


### <a name="apply-custom-configuration-changes"></a>Applicare le modifiche di configurazione personalizzate

Per applicare le modifiche di configurazione personalizzate, attenersi alla procedura seguente:


1. Eseguire il seguente comando per decomprimere il file binario.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Schermata del comando da eseguire](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Eseguire il comando seguente per fornire le autorizzazioni.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Eseguire il comando seguente per eseguire lo script.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Eseguire lo script solo una volta sul server. Quindi arrestare il server. Riavviare il server dopo avere aggiunto un disco, come indicato nella sezione successiva.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Aggiungere un disco di conservazione alla macchina virtuale del server di destinazione master Linux

Per creare un disco di conservazione, attenersi alla procedura seguente:

1. Collegare un nuovo disco da 1 TB alla macchina virtuale del server di destinazione master Linux e avviare il computer.

2. Usare il comando **multipath -ll** per conoscere l'ID a percorsi multipli del disco di conservazione.
    
     `multipath -ll`

        ![The multipath ID of the retention disk](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formattare l'unità e creare un file system nella nuova unità.

    
    `mkfs.ext4 /dev/mapper/<Retention disk's multipath id>`
    
    ![Creazione di un file system nell'unità](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Dopo aver creato il file system, montare il disco di conservazione.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```
    ![Montaggio del disco di conservazione](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Creare la voce **fstab** per montare l'unità di conservazione a ogni avvio del sistema.
    
    `vi /etc/fstab`
    
    Selezionare **Inserisci** per iniziare a modificare il file. Creare una nuova riga e inserirvi il testo seguente. Modificare l'ID a percorsi multipli disco in base all'ID a percorsi multipli evidenziato dal comando precedente.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Premere **Esc** e digitare **:wq**, che sta per scrivi ed esci, per chiudere la finestra dell'editor.

### <a name="install-the-master-target"></a>Installare il server di destinazione master

> [!IMPORTANT]
> La versione del server di destinazione master deve essere uguale o precedente a quella del server di elaborazione e del server di configurazione. Se la versione del server master di destinazione è superiore, la riprotezione avrà esito positivo, ma la replica avrà esito negativo.


> [!NOTE]
> Prima di installare il server di destinazione master, assicurarsi che il file **/etc/hosts** nella macchina virtuale contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.

1. Copiare la passphrase CS da **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** nel server di configurazione. Quindi salvarla come **passphrase.txt** nella stessa directory locale eseguendo il comando seguente:

    `echo <passphrase> >passphrase.txt`

    Esempio: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Prendere nota dell'indirizzo IP del server di configurazione. Eseguire il comando seguente per installare il server di destinazione master e registrarlo con il server di configurazione.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Esempio: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Attendere il termine dello script. Se il server di destinazione master viene registrato correttamente, viene elencato nella pagina **Infrastruttura di Site Recovery** nel portale.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Installazione interattiva del server di destinazione master

1. Eseguire il comando seguente per installare il server di destinazione master. Per il ruolo agente selezionare **Master Target**.

    ```
    ./install
    ```

2. Selezionare il percorso predefinito per l'installazione, quindi premere **Invio** per continuare.

    ![Scelta di un percorso predefinito per l'installazione del server di destinazione master](./media/site-recovery-how-to-install-linux-master-target/image17.png)

Dopo aver completato l'installazione, registrare il server di configurazione tramite la riga di comando.

1. Annotare l'indirizzo IP del server di configurazione. perché sarà necessario nel passaggio successivo.

2. Eseguire il comando seguente per installare il server di destinazione master e registrarlo con il server di configurazione.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Esempio: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Attendere il termine dello script. Se il server di destinazione master viene registrato correttamente, viene elencato nella pagina **Infrastruttura di Site Recovery** del portale.


### <a name="install-vmware-tools-on-the-master-target-server"></a>Installare gli strumenti VMware nel server di destinazione master

È necessario installare gli strumenti VMware per consentire al server di destinazione master di rilevare gli archivi dati. Se non sono installati gli strumenti, la schermata di riprotezione non viene elencata negli archivi dati. Dopo l'installazione degli strumenti VMware è necessario riavviare il computer.

### <a name="upgrade-the-master-target-server"></a>Aggiornare il server di destinazione master

Eseguire il programma di installazione. Tale programma rileva automaticamente che l'agente è installato nella destinazione master. Selezionare **Y** per eseguire l'aggiornamento.  Al termine dell'installazione, controllare la versione del server di destinazione master installata usando il comando seguente:

`cat /usr/local/.vx_version`


È possibile vedere che il campo **Version** (Versione) indica il numero di versione del server di destinazione master.

## <a name="common-issues"></a>Problemi comuni

* Verificare che Storage vMotion non sia abilitato in alcun componente di gestione, ad esempio il server di destinazione master. Se il server di destinazione master viene spostato dopo una riprotezione con esito positivo, non è possibile scollegare i dischi della macchina virtuale (VMDK). In questo caso, il failback avrà esito negativo.

* Il server di destinazione master non deve includere snapshot nella macchina virtuale. Se sono presenti snapshot, il failback avrà esito negativo.

* A causa di alcune configurazioni personalizzate della scheda di interfaccia di rete, l'interfaccia di rete viene disabilitata durante l'avvio e non è possibile inizializzare l'agente del server di destinazione master. Verificare che le proprietà seguenti siano impostate correttamente. Verificare le proprietà nei file della scheda Ethernet /etc/sysconfig/network-scripts/ifcfg-eth*.
    * BOOTPROTO=dhcp
    * ONBOOT=yes


## <a name="next-steps"></a>Passaggi successivi
Al termine dell'installazione e della registrazione del server di destinazione master, quest'ultimo viene visualizzato nella sezione **Destinazione master** della pagina **Infrastruttura di Site Recovery** nelle informazioni generali sul server di configurazione.

È ora possibile procedere con la [riprotezione](site-recovery-how-to-reprotect.md), seguita dal failback.

