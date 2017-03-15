---
title: Come installare un server di destinazione master Linux per il failover da Azure a locale | Microsoft Docs
description: "Prima di eseguire la riprotezione di una macchina virtuale Linux, è necessario disporre di un server di destinazione master Linux. Di seguito viene descritto come installarlo."
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
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 8ac18526e6781f189444233a191aa1d6c5b863ff
ms.lasthandoff: 02/22/2017


---
# <a name="how-to-install-linux-master-target-server"></a>Come installare un server di destinazione master Linux
Dopo aver eseguito il failover delle macchine virtuali, è possibile eseguire il failback in locale della macchina virtuale. Per il failback, è prima necessario fare in modo che la macchina virtuale sia in stato protetto eseguendo la riprotezione della macchina virtuale da Azure a locale. A tale scopo, è necessario un server di destinazione master che riceva il traffico in locale. Se quella protetta è una macchina virtuale Windows, è necessario un server di destinazione master Windows. Per una macchina virtuale Linux, è necessario eseguire la riprotezione con un server di destinazione master Linux. Per informazioni su come creare e installare server di destinazione master Linux, vedere la procedura riportata di seguito.

## <a name="overview"></a>Panoramica
Questo articolo contiene informazioni e istruzioni per l'installazione di un server di destinazione master Linux.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Prerequisiti

* Per scegliere correttamente l'host in cui distribuire il server di destinazione master, determinare se il failback verrà eseguito in una macchina virtuale locale esistente o in una macchina virtuale nuova, perché quella locale è stata eliminata. 
    * Per una macchina virtuale esistente, l'host del server di destinazione master deve avere accesso agli archivi dati della macchina virtuale.
    * Se la macchina virtuale non esiste in locale, la macchina virtuale di failback viene creata nello stesso host del server di destinazione master. Per l'installazione del server di destinazione master è possibile scegliere qualsiasi host ESXi.
* Il server deve trovarsi in una rete in grado di comunicare con il server di elaborazione e il server di configurazione.
* La versione del server di destinazione master deve essere minore o pari a quella dei server di elaborazione e configurazione. Ad esempio, se la versione del server di configurazione è 9.4, quella del server di destinazione master potrà essere 9.4 o 9.3, ma non 9.5.
* Il server di destinazione master può essere solamente una macchina virtuale VMware, non una macchina virtuale fisica.


## <a name="steps-to-deploy-the-master-target-server"></a>Procedura per la distribuzione del server di destinazione master

### <a name="install-centos-66-minimal"></a>Installare CentOS 6.6 Minimal

Seguire i passaggi indicati di seguito per installare il sistema operativo CentOS 6.6 a 64 bit.

1. Tra i collegamenti seguenti, scegliere il mirror più vicino per scaricare un'immagine ISO di CentOS 6.6 Minimal a 64 bit.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    Inserire l'ISO di CentOS 6.6 Minimal a 64 bit in un'unità DVD e avviare il sistema.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. Selezionare **Skip** (Ignora) per ignorare il processo di test del supporto.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. Viene visualizzata la schermata iniziale dell'installazione. Fare clic qui sul pulsante **Next** (Avanti).

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. Selezionare **English** (Inglese) come lingua preferita e fare clic su **Next** (Avanti) per continuare.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. Selezionare **US English** (Inglese USA) per il layout di tastiera. Fare clic su **Next** (Avanti) per continuare l'installazione.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. Selezionare il tipo di dispositivi in cui verrà eseguita l'installazione. Selezionare **Basic storage Devices** (Dispositivi di archiviazione di base). Fare clic su **Next** (Avanti) per continuare l'installazione.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. Viene visualizzato un messaggio di avviso che indica che i dati esistenti nel disco rigido verranno eliminati. Assicurarsi che il disco rigido non contenga dati importanti e fare clic su **Yes, discard any data** (Sì, elimina tutti i dati).

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. Immettere il nome host per il server nella casella di testo **Hostname** (Nome host). Fare clic su **Configure Network** (Configura rete). Nella finestra **Network Connection** (Connessione di rete), selezionare l'interfaccia di rete. Fare clic sul pulsante **Edit** (Modifica) per configurare le impostazioni IPV4.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. Viene visualizzata la finestra **Editing System eth0** (Modifica del sistema eth0). Selezionare la casella di controllo **Connect automatically** (Connetti automaticamente). Nella scheda "IPv4 Settings" (Impostazioni IPv4), scegliere il metodo **Manual** (Manuale) e fare clic sul pulsante **Add** (Aggiungi). Specificare i dettagli relativi all'indirizzo IP statico, alla netmask, al gateway e al server DNS. Fare clic su **Apply** (Applica) per salvare i dettagli.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. Selezionare il fuso orario dalla casella combinata e fare clic su **Next** (Avanti) per continuare.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. Immettere la **password radice** e confermarla, quindi fare clic su **Next** (Avanti) per continuare.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. Selezionare **Create Custom Layout** (Crea layout personalizzato) come modalità di partizione e fare clic su **Next** (Avanti) per continuare.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. Selezionare la partizione **Free** (Gratuita) e fare clic su **Create** (Crea) per creare le partizioni **/**, **/var/crash** e **/home** con **ext4** come tipo di file system. Creare una **partizione di swapping** con **swap** come tipo di file system. Per allocare le dimensioni della partizione, seguire la relativa formula di allocazione come indicato nella tabella seguente.

    > [!NOTE]
    > Il sistema del server di destinazione master Linux non deve usare la macchina virtuale Linux per gli spazi di archiviazione radice o di conservazione. Linux MT è configurato per impostazione predefinita in modo da evitare l'individuazione di partizioni e dischi LVM.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. Dopo aver creato la partizione, fare clic su **Next** (Avanti) per continuare.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. Se vengono rilevati dispositivi preesistenti, viene visualizzato un messaggio di avviso che chiede di formattarli. Fare clic su **Format** (Formatta) per formattare il disco rigido con la tabella di partizioni più recente.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. Fare clic su **Write changes to disk** (Scrivi modifiche su disco) per applicare le modifiche della partizione nel disco.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. Selezionare l'opzione **Install boot loader** (Installa bootloader) e fare clic su **Next** (Avanti) per installare il bootloader nella partizione radice.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. Viene avviato il processo di installazione. È possibile monitorare l'avanzamento dell'installazione.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. Al termine dell'installazione viene visualizzata la schermata seguente. Fare clic su **Riavvia**

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Passaggi successivi all'installazione
Successivamente, preparare il computer per la configurazione come server di destinazione master.

Per ottenere l'ID SCSI per ogni disco rigido SCSI in una macchina virtuale Linux, è necessario abilitare il parametro "disk.EnableUUID = TRUE ".

Per attivare questo parametro, attenersi alla procedura seguente:

a. Arrestare la macchina virtuale.

b. Fare clic con il pulsante destro del mouse sulla voce della macchina virtuale nel pannello sinistro, quindi selezionare **Modifica impostazioni**.

c. Scegliere la scheda **Options (Opzioni)** .

d. Selezionare l'opzione **Advanced&gt;General item** (Avanzate>Elemento generale) sulla sinistra e fare clic sui **parametri di configurazione** visualizzati a destra.

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

L’opzione relativa ai "parametri di configurazione" è disattivata quando il computer è in esecuzione. Per rendere attiva questa scheda, arrestare il computer.

e. Esiste già una riga con il valore **disk.EnableUUID**?

f. Se il valore è presente ed è impostato su False, modificarlo in True. I valori True e False non fanno distinzione tra maiuscole e minuscole.

g. Se il valore è presente ed è impostato su True, fare clic su Cancel (Annulla).
    
h. Se il valore non esiste, fare clic su **Add Row**(Aggiungi riga).

i. Aggiungere disk.EnableUUID nella colonna Name (Nome) e impostarne il valore su True.

   * Se è presente e se il valore è impostato su False, modificare il valore in True (valori True e False sono sensibili in maiuscole).

   * Se il valore esiste ed è impostato su True, fare clic su Annulla e testare il comando SCSI all'interno del sistema operativo guest dopo il suo avvio.

f. Se non esiste, fare clic su **Add Row**(Aggiungi riga).

  Aggiungere il disk.EnableUUID nella colonna del nome.

  Impostare il relativo valore come TRUE



![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>Scaricare e installare i pacchetti aggiuntivi

> [!NOTE]
> Verificare la connettività Internet del sistema prima di scaricare e installare pacchetti aggiuntivi. In caso contrario sarà necessario identificare i pacchetti RPM e installarli manualmente.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

Con il comando sopra verranno scaricati i 15 pacchetti menzionati di seguito dal repository di CentOS 6.6  e installarli. Se non si ha accesso a Internet, scaricare il pacchetto RPM seguente.


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
 

***ReiserFS, se usato in Suse11SP3. ReiserFS non è tuttavia il file system predefinito in Suse11SP3.***

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
Questa operazione è necessaria per abilitare i pacchetti Multipath sul server MT.

### <a name="get-the-installer-for-setup"></a>Ottenere il programma di installazione per l'installazione

Se si ha accesso a Internet dal server di destinazione master, è possibile scaricare il programma di installazione tramite la procedura seguente. In alternativa, è possibile copiare il programma di installazione dal server di elaborazione e installarlo.

#### <a name="download-the-mt-installation-packages"></a>Scaricare i pacchetti di installazione di MT

Scaricare da qui i bit di installazione più recenti per la destinazione master Linux: [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc).

Per scaricarli da sistema Linux, digitare 

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Assicurarsi di scaricare e decomprimere il programma di installazione solo nella home directory. Se viene decompresso nel percorso /usr/Locale, l'installazione avrà esito negativo.


#### <a name="access-the-installer-from-the-process-server"></a>Accedere al programma di installazione dal server di elaborazione

Passare al server di elaborazione e alla directory C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

Copiare il file del programma di installazione necessario dal server di elaborazione e salvarlo come latestlinuxmobsvc.tar.gz nella home directory.


### <a name="apply-custom-configuration-changes"></a>Applicare le modifiche di configurazione personalizzate

Per applicare le modifiche di configurazione personalizzate, attenersi alla procedura seguente:


1. Eseguire il seguente comando per decomprimere  il file binario.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
2. Eseguire il seguente comando per concedere l'autorizzazione.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Eseguire il comando per eseguire lo script seguente.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Eseguire lo script solo una volta nel server. Arrestare il server. Riavviare il server dopo l'aggiunta di un disco come indicato nei passaggi successivi.

### <a name="add-retention-disk-to-linux-mt-vm"></a>Aggiungere un disco di conservazione a una macchina virtuale del server di destinazione master Linux 

Eseguire la procedura riportata di seguito per creare un disco di conservazione.

1. Collegare un nuovo disco da **1 TB** alla macchina virtuale del server di destinazione master Linux e **avviare** il computer.

2. Usare il comando **multipath -ll** per conoscere l'id Multipath del disco di conservazione.

    ```
    multipath -ll
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formattare l'unità e creare un file system nella nuova unità. 
    
    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Dopo aver creato il file system, montare il disco di conservazione.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Infine, creare la voce fstab per montare l'unità di conservazione a ogni avvio.
    ```
    vi /etc/fstab 
    ```
    Premere il tasto [INS] per iniziare a modificare il file. Creare una nuova riga e inserirvi il testo seguente. Modificare l'ID percorsi multipli del disco in base all'ID percorsi multipli evidenziato dal comando precedente.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Premere [ESC] e digitare :wq, che sta per scrivi ed esci, per chiudere la finestra dell'editor.

### <a name="install-master-target"></a>Installare la destinazione master

> [!NOTE]
> La versione del server di destinazione master deve essere minore o pari a quella dei server di elaborazione e configurazione. Se la versione del server master di destinazione è superiore, la riprotezione avrà esito positivo, ma la replica avrà esito negativo.
 

> [!NOTE]
> Prima di installare il server master di destinazione, assicurarsi che il file /etc/hosts nella macchina virtuale contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
 
1. Copiare la passphrase da **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** nel server di configurazione e salvarla nel file passphrase.txt, nella stessa directory locale, eseguendo questo comando.

    ```
    echo <passphrase> >passphrase.txt
    ```
    Esempio: echo itUx70I47uxDuUVY >passphrase.txt

2. Prendere nota dell'indirizzo IP del server di configurazione, perché sarà necessario nel passaggio successivo.

3. Eseguire questo comando per installare il server di destinazione master e registrarlo con il server di configurazione.
    
    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    Esempio: ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    Attendere che l'esecuzione dello script sia terminata. Se il server di destinazione master è stato registrato correttamente, viene elencato nella pagina Infrastruttura di Site Recovery nel portale.

#### <a name="installing-master-target-using-interactive-install"></a>Installazione interattiva del server di destinazione master

1. Eseguire questo comando per installare il server di destinazione master. Selezionare il ruolo agente "Master Target".

    ```
    ./install
    ```

2. Selezionare il percorso predefinito per l'installazione. Premere [INVIO] per continuare.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. Selezionare le impostazioni globali da configurare

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. Specificare gli indirizzi IP del server CS

5. Specificare 443 per la porta del server CS.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. Copiare la passphrase CS da C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase nel server di configurazione e specificarla nella casella della passphrase. Verrà visualizzata come vuota anche dopo aver eseguito il copia e incolla.

7. Passare a [Quit] (Esci) nel menu.

8. Attendere il completamento di installazione e registrazione.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Installare gli strumenti VMware nel server di destinazione master

È necessario installare gli strumenti VMware per consentire al server master di destinazione di rilevare gli archivi dati. Se non sono installati gli strumenti, la schermata di riprotezione non elencherà gli archivi dati. È necessario riavviare il computer dopo l'installazione degli strumenti VMware.

## <a name="next-steps"></a>Passaggi successivi
Al termine dell'installazione e della registrazione del server di destinazione master, quest'ultimo viene visualizzato nella relativa sezione della pagina Infrastruttura di Site Recovery, nella panoramica del server di configurazione.

È ora possibile procedere con la [riprotezione](site-recovery-how-to-reprotect.md), seguita dal failback.

## <a name="common-issues"></a>Problemi comuni

* Assicurarsi che Storage vMotion non sia abilitato in alcun componente di gestione, ad esempio il server di destinazione master. Se il server di destinazione master viene spostato dopo una riprotezione, non è possibile scollegare il file VMDK e il failback avrà esito negativo.
* Il server di destinazione master non deve avere snapshot nella macchina virtuale. Se sono presenti snapshot, il failback avrà esito negativo.
* Per via delle configurazioni personalizzate della scheda di interfaccia di rete presso alcuni clienti, l'interfaccia di rete viene disabilitata durante l'avvio e successivamente non è possibile inizializzare l'agente del server di destinazione master. Assicurarsi che le proprietà seguenti siano impostate correttamente.
    * Verificare le due proprietà nei file della scheda Ethernet /etc/sysconfig/network-scripts/ifcfg-eth*
        * BOOTPROTO=dhcp 
        * ONBOOT=yes


