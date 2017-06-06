---
title: Come installare un server di destinazione master Linux per il failover da Azure a locale | Documentazione Microsoft
description: "Prima di eseguire la riprotezione di una macchina virtuale Linux, è necessario dotarsi di un server di destinazione master Linux. Di seguito viene descritto come installarlo."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/05/2017
ms.author: ruturajd
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 60102ebb43efc8710f102450df5b98edcb1d4b39
ms.contentlocale: it-it
ms.lasthandoff: 05/08/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Come installare un server di destinazione master Linux
Dopo aver eseguito il failover delle macchine virtuali, è possibile eseguirne il failback nel sito locale. Per eseguire il failback, è necessario riproteggere la macchina virtuale da Azure al sito locale. A tale scopo, è necessario un server di destinazione master locale che riceva il traffico. Se quella protetta è una macchina virtuale Windows, è necessario un server di destinazione master Windows. Per una macchina virtuale Linux è necessario un server di destinazione master Linux. Per informazioni su come creare e installare server di destinazione master Linux, vedere la procedura riportata di seguito.

## <a name="overview"></a>Panoramica
Questo articolo contiene informazioni e istruzioni per l'installazione di un server di destinazione master Linux.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Prerequisiti

* Per scegliere correttamente l'host in cui distribuire il server di destinazione master, determinare se il failback verrà eseguito in una macchina virtuale locale esistente o in una macchina virtuale nuova, perché quella locale è stata eliminata.
    * Se viene eseguito in una macchina virtuale esistente, l'host del server di destinazione master deve poter accedere agli archivi dati della macchina virtuale.
    * Se la macchina virtuale locale non esiste, la macchina virtuale di failback viene creata nello stesso host del server di destinazione master. Per l'installazione del server di destinazione master è possibile scegliere qualsiasi host ESXi.
* Il server deve trovarsi in una rete in grado di comunicare con il server di elaborazione e il server di configurazione.
* La versione del server di destinazione master deve essere uguale o precedente a quella del server di elaborazione e del server di configurazione. Ad esempio, se la versione del server di configurazione è 9.4, la versione del server di destinazione master può essere 9.4 o 9.3 ma non 9.5.
* Il server di destinazione master può essere solo una macchina virtuale VMware e non un server fisico.

## <a name="master-target-sizing-guideline"></a>Linee guida sul ridimensionamento della destinazione master

La destinazione master deve essere creata seguendo queste linee guida sul ridimensionamento
    * RAM: almeno 6 GB
    * Dimensioni del disco del sistema operativo: almeno 100 GB (per installare CentOS6.6)
    * Dimensioni disco aggiuntive per l'unità di conservazione: 1 TB
    * Core CPU: almeno 4 core


## <a name="steps-to-deploy-the-master-target-server"></a>Passaggi per distribuire il server di destinazione master

### <a name="install-centos-66-minimal"></a>Installare CentOS 6.6 Minimal

Per installare il sistema operativo a 64 bit CentOS 6.6, attenersi alla procedura seguente:

1. Tra i collegamenti seguenti, scegliere il mirror più vicino per scaricare un'immagine ISO di CentOS 6.6 Minimal a 64 bit.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    Inserire l'ISO di CentOS 6.6 Minimal a 64 bit in un'unità DVD e avviare il sistema.

    ![Finestra di dialogo di benvenuto di CentoOS 6.6](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. Selezionare **Skip** (Ignora) per ignorare il processo di test del supporto.

    ![Opzione Skip (Ignora) per ignorare il processo di test del supporto](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. Nella schermata iniziale fare clic sul pulsante **Avanti** .

    ![Pulsante Avanti nella schermata iniziale di installazione](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. Selezionare **English** (Inglese) come lingua preferita e fare clic su **Avanti** per continuare.

    ![Selezionare una lingua](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. Selezionare **US English** (Inglese USA) per il layout di tastiera e quindi fare clic su **Avanti**.

    ![Selezionare il layout di tastiera inglese](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. Selezionare **Basic storage Devices** (Dispositivi di archiviazione di base), quindi fare clic su **Avanti**.

    ![Selezionare un dispositivo di archiviazione](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. Il messaggio di avviso visualizzato indica che i dati esistenti nel disco rigido verranno eliminati. Assicurarsi che il disco rigido non contenga dati importanti e fare clic su **Yes, discard any data** (Sì, elimina tutti i dati).

    ![Avviso sull'eliminazione di dati se si procede](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. Immettere il nome host del server nella casella **Hostname** (Nome host) e quindi fare clic su **Configure Network**(Configura rete). Nella finestra di dialogo **Network Connection** (Connessione di rete) selezionare l'interfaccia di rete e quindi fare clic sul pulsante **Edit** (Modifica) per configurare IPV4Settings.

    ![Selezionare un nome host e configurare IPV4](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. Nella finestra di dialogo **Editing System eth0** verificare la casella di controllo **Connect automatically** (Connetti automaticamente). Nella scheda **IPv4 Settings** (Impostazioni IPv4) scegliere **Manual** (Manuale) per **Method** (Metodo) e quindi fare clic sul pulsante **Add** (Aggiungi). Specificare i dettagli relativi all'indirizzo **IP statico**, alla **netmask**, al **gateway** e al **server DNS**. Fare clic su **Apply** (Applica) per salvare i dettagli.

    ![Impostazioni per la configurazione della rete](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. Selezionare il fuso orario e quindi fare clic su **Next** (Avanti).

    ![Selezionare il fuso orario](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. Immettere la **password radice** e confermarla, quindi fare clic su **Next** (Avanti) per continuare.

    ![Aggiungere una password](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. Selezionare **Create Custom Layout** (Creare layout personalizzato), quindi fare clic su **Next** (Avanti).

    ![Selezionare un tipo di installazione](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. Selezionare la partizione **Free** (Gratuita) e fare clic su **Create** (Crea) per creare le partizioni **/**, **/var/crash** e **/home** con **ext4** come tipo di file system. Creare una **partizione di swapping** con **swap** come tipo di file system. Per allocare le dimensioni della partizione, seguire la relativa formula di allocazione come indicato nella tabella seguente.

    > [!NOTE]
    > Il server di destinazione master Linux non deve usare LVM per gli spazi di archiviazione radice o di conservazione. Per impostazione predefinita, il server di destinazione master Linux è configurato in modo da evitare l'individuazione di partizioni e dischi LVM.

    ![Tabella dei nomi e dimensioni delle partizioni e tipi di file system](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. Dopo aver creato la partizione, fare clic su **Next** (Avanti).

    ![Finestra di dialogo che mostra i valori selezionati per le partizioni](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. Se vengono rilevati dispositivi preesistenti, viene visualizzato un messaggio di avviso che chiede di formattarli. Fare clic su **Format** (Formatta) per formattare il disco rigido con la tabella delle partizioni più recente.

    ![Fare clic sul pulsante Format (Formatta) per formattare il disco](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. Fare clic su **Write changes to disk** (Scrivi modifiche su disco) per applicare le modifiche della partizione nel disco.

    ![Fare clic su Write changes to disk (Scrivi modifiche su disco)](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. Selezionare l'opzione **Install boot loader** (Installa bootloader) e fare clic su **Next** (Avanti) per installare il bootloader nella partizione radice.

    ![Installare il bootloader nella partizione radice](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. Viene avviato il processo di installazione. È possibile monitorarne lo stato.

    ![Finestra di dialogo che mostra l'avanzamento dell'installazione](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. Al termine dell'installazione viene visualizzata la schermata seguente. Fare clic su **Riavvia**.

    ![Schermata che mostra la riuscita dell'installazione](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Passaggi successivi all'installazione
Preparare il computer per la configurazione come server di destinazione master.

Per ottenere l'ID per ogni disco rigido SCSI in una macchina virtuale Linux, è necessario abilitare il parametro **disk.EnableUUID = TRUE**.

Per abilitare il parametro, attenersi alla procedura seguente:

1. Arrestare la macchina virtuale.

2. Fare clic con il pulsante destro del mouse sulla voce della macchina virtuale nel riquadro a sinistra e quindi scegliere **Edit Settings**(Modifica impostazioni).

3. Scegliere la scheda **Options (Opzioni)** .

4. Selezionare l’opzione **Advanced&gt;General** (Avanzate>Elemento) nel riquadro a sinistra e fare clic sui **parametri di configurazione** visualizzati a destra.

    ![Scheda Opzioni](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    L'opzione **Configuration Parameters** (Parametri di configurazione) è disponibile solo quando il computer è in esecuzione. Per attivare la scheda, arrestare la macchina virtuale.

5. Controllare se esiste già una riga con il valore **disk.EnableUUID**.

    - Se il valore esiste ed è impostato su **False**, modificarlo in **True** (i valori non fanno distinzione tra maiuscole e minuscole).

    - Se il valore è presente ed è impostato su **True**, fare clic su **Cancel** (Annulla).

    - Se il valore non esiste, fare clic su **Add Row**(Aggiungi riga).

    - Aggiungere **disk.EnableUUID** nella colonna **Name** (Nome) e impostarne il valore su **TRUE**.

    ![Controllare se esiste già una riga con il valore disk.EnableUUID](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-additional-packages"></a>Scaricare e installare i pacchetti aggiuntivi

> [!NOTE]
> Verificare di disporre della connettività Internet per scaricare e installare pacchetti aggiuntivi. Senza la connettività Internet, è necessario trovare manualmente i pacchetti RPM e installarli.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

Il comando precedente scarica i 15 pacchetti seguenti dal repository di CentOS 6.6 e li installa. Se non si ha accesso a Internet, scaricare i pacchetti RPM seguente:


bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm


#### <a name="install-additional-packages-for-specific-operating-systems"></a>Installare pacchetti aggiuntivi per sistemi operativi specifici

> [!NOTE]
> Se le macchine di origine protette usano il file system Reiser o XFS per il dispositivo di avvio o radice, è necessario scaricare e installare i pacchetti aggiuntivi seguenti nel server di destinazione master Linux prima della protezione.


***ReiserFS, se usato in Suse11SP3. ReiserFS non è il file system predefinito in Suse11SP3***

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL, CentOS 7 o versione successiva)***

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath
```
Questa operazione è necessaria per abilitare i pacchetti Multipath sul server di destinazione master.

### <a name="get-the-installer-for-setup"></a>Ottenere il programma di installazione per l'installazione

Se il server di destinazione master dispone della connettività Internet, è possibile attenersi alla procedura seguente per scaricare il programma di installazione. In caso contrario, copiare il programma di installazione dal server di elaborazione e installarlo.

#### <a name="download-the-master-target-installation-packages"></a>Scaricare i pacchetti di installazione del server di destinazione master

[Scaricare il file di installazione più recente del server di destinazione master Linux](https://aka.ms/latestlinuxmobsvc).

Per scaricarli usando Linux, digitare:

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Verificare di scaricare e decomprimere il programma di installazione nella home directory. Se il file viene decompresso nel percorso /usr/Locale, l'installazione avrà esito negativo.


#### <a name="access-the-installer-from-the-process-server"></a>Accedere al programma di installazione dal server di elaborazione

1. Passare al server di elaborazione e alla directory C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

2. Copiare il file del programma di installazione necessario dal server di elaborazione e salvarlo come latestlinuxmobsvc.tar.gz nella home directory.


### <a name="apply-custom-configuration-changes"></a>Applicare le modifiche di configurazione personalizzate

Per applicare le modifiche di configurazione personalizzate, attenersi alla procedura seguente:


1. Eseguire il seguente comando per decomprimere il file binario.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![Schermata del comando eseguito](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Eseguire il comando seguente per fornire le autorizzazioni.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Eseguire il comando seguente per eseguire lo script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Eseguire lo script solo una volta sul server. Arrestare il server. Riavviare il server dopo l'aggiunta di un disco come indicato nei passaggi successivi.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Aggiungere un disco di conservazione alla macchina virtuale del server di destinazione master Linux

Per creare un disco di conservazione, attenersi alla procedura seguente:

1. Collegare un nuovo disco da **1 TB** alla macchina virtuale del server di destinazione master Linux e **avviare** il computer.

2. Usare il comando **multipath -ll** per conoscere l'ID a percorsi multipli del disco di conservazione.

    ```
    multipath -ll
    ```

    ![ID a percorsi multipli del disco di conservazione](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formattare l'unità e creare un file system nella nuova unità.

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![Creazione di un file system nell'unità](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Dopo aver creato il file system, montare il disco di conservazione.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![Montaggio del disco di conservazione](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Creare la voce **fstab** per montare l'unità di conservazione a ogni avvio.
    ```
    vi /etc/fstab
    ```
    Premere il tasto **INS** per iniziare a modificare il file. Creare una nuova riga e inserirvi il testo seguente. Modificare l'ID a percorsi multipli disco in base all'ID a percorsi multipli evidenziato dal comando precedente.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Premere **ESC** e digitare **:wq**, che sta per scrivi ed esci, per chiudere la finestra dell'editor.

### <a name="install-the-master-target"></a>Installare il server di destinazione master

> [!IMPORTANT]
> La versione del server di destinazione master deve essere uguale o precedente a quella del server di elaborazione e del server di configurazione. Se la versione del server master di destinazione è superiore, la riprotezione avrà esito positivo, ma la replica avrà esito negativo.


> [!NOTE]
> Prima di installare il server master di destinazione, assicurarsi che il file /etc/hosts nella macchina virtuale contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.

1. Copiare la passphrase da C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase nel server di configurazione e salvarla nel file passphrase.txt, nella stessa directory locale, eseguendo il comando seguente.

    ```
    echo <passphrase> >passphrase.txt
    ```
    Esempio: echo itUx70I47uxDuUVY >passphrase.txt

2. Prendere nota dell'indirizzo IP del server di configurazione, perché sarà necessario nel passaggio successivo.

3. Eseguire il comando seguente per installare il server di destinazione master e registrarlo con il server di configurazione.

    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    Esempio: ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    Attendere il termine dello script. Se il server di destinazione master è stato registrato correttamente, viene elencato nella pagina Infrastruttura di Site Recovery nel portale.

#### <a name="install-the-master-target-by-using-interactive-install"></a>Installazione interattiva del server di destinazione master

1. Eseguire il comando seguente per installare il server di destinazione master. Selezionare il ruolo agente **Master Target**.

    ```
    ./install
    ```

2. Selezionare il percorso predefinito per l'installazione. Premere **INVIO** per continuare.

    ![Scelta di un percorso predefinito per l'installazione del server di destinazione master](./media/site-recovery-how-to-install-linux-master-target/image17.png)


3. Selezionare le impostazioni **globali** da configurare.

    ![Configurazione delle impostazioni globali](./media/site-recovery-how-to-install-linux-master-target/image18.png)

4. Specificare gli indirizzi IP del server di configurazione.

5. Specificare la porta del server di configurazione come porta 443.

    ![Specificare l'indirizzo IP e porta del server di configurazione](./media/site-recovery-how-to-install-linux-master-target/image19.png)

6. Copiare la passphrase del server di configurazione da C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase nel server di configurazione e specificarla nella casella della **passphrase**. La casella risulterà vuota anche dopo aver incollato il testo.

7. Passare a **Quit** (Esci) nel menu.

8. Attendere il completamento dell'installazione e della registrazione.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Installare gli strumenti VMware nel server master di destinazione

È necessario installare gli strumenti VMware per consentire al server di destinazione master di rilevare gli archivi dati. Se non sono installati gli strumenti, la schermata di riprotezione non elencherà gli archivi dati. È necessario riavviare il computer dopo l'installazione degli strumenti VMware.

## <a name="next-steps"></a>Passaggi successivi
Al termine dell'installazione e della registrazione del server, quest'ultimo viene visualizzato nella sezione del **server di destinazione master** della pagina **Infrastruttura di Site Recovery**, nella panoramica del server di configurazione.

È ora possibile procedere con la [riprotezione](site-recovery-how-to-reprotect.md), seguita dal failback.

## <a name="common-issues"></a>Problemi comuni

* Verificare che Storage vMotion non sia abilitato in alcun componente di gestione, ad esempio il server di destinazione master. Se il server di destinazione master viene spostato dopo una riprotezione con esito positivo, non è possibile scollegare i dischi della macchina virtuale (VMDK) e il failback avrà esito negativo.
* Il server di destinazione master non deve includere snapshot nella macchina virtuale. Se sono presenti snapshot, il failback avrà esito negativo.
* Per via delle configurazioni personalizzate della scheda di interfaccia di rete presso alcuni clienti, l'interfaccia di rete viene disabilitata durante l'avvio e non è possibile inizializzare l'agente del server di destinazione master. Verificare che le proprietà seguenti siano impostate correttamente. Verificare le proprietà nei file della scheda Ethernet /etc/sysconfig/network-scripts/ifcfg-eth*.
    * BOOTPROTO=dhcp
    * ONBOOT=yes

