---
title: Come installare un server di destinazione master Linux | Microsoft Docs
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
ms.date: 12/20/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: d1a7ed7e182530f81a426a4383297a49505f65ea
ms.openlocfilehash: d76ea0fb27ecece4e8dcd06a2dde9a0794071884


---
# <a name="how-to-install-linux-master-target-server"></a>Come installare un server di destinazione master Linux
Il servizio Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR) orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una rapida panoramica, leggere [Che cos'è Azure Site Recovery?](site-recovery-overview.md)

## <a name="overview"></a>Panoramica
In questo articolo vengono fornite informazioni e istruzioni per il ripristino (failover e failback) di macchine virtuali e server fisici protetti con Site Recovery.

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Prerequisiti

1. Per scegliere correttamente l'host in cui distribuire il server di destinazione master, determinare se il failback verrà eseguito in locale in una macchina virtuale esistente o in una macchina virtuale nuova (per via dell'eliminazione di quella locale). 
    * Per una macchina virtuale esistente, l'host del server di destinazione master deve poter accedere agli archivi dati della macchina virtuale.
    * Se la macchina virtuale non esiste in locale, la macchina virtuale di failback verrà creata nello stesso host del server.
2. Il server deve trovarsi in una rete in grado di comunicare con il server di elaborazione e il server di configurazione.
3. La versione del server di destinazione master deve essere minore o pari a quella dei server di elaborazione e configurazione. Ad esempio, se la versione del server di configurazione è 9.4, quella del server di destinazione master potrà essere 9.4 o 9.3, ma non 9.5.
4. Il server di destinazione master può essere solamente una macchina virtuale VMware, non una macchina virtuale fisica.


## <a name="steps-to-deploy-the-master-target-server"></a>Passaggi per distribuire il server di destinazione master

### <a name="install-centos-66-minimal"></a>Installare CentOS 6.6 Minimal

Seguire i passaggi indicati di seguito per installare il sistema operativo CentOS 6.6 a 64 bit.

**Passaggio 1:** tra i collegamenti seguenti, scegliere il mirror più vicino per scaricare un'immagine ISO di CentOS 6.6 Minimal a 64 bit.

<http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

Inserire l'ISO di CentOS 6.6 Minimal a 64 bit in un'unità DVD e avviare il sistema.

![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

**Passaggio 2:** selezionare **Skip** (Ignora) per ignorare il processo di test del supporto.

![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

**Passaggio 3:** viene visualizzata la schermata iniziale dell'installazione. Fare clic qui sul pulsante **Next** (Avanti).

![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

**Passaggio 4:** selezionare **English** (Inglese) come lingua preferita e fare clic su **Next** (Avanti) per continuare.

![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

**Passaggio 5:** selezionare **US English** (Inglese USA) come un layout di tastiera. Fare clic su **Next** (Avanti) per continuare l'installazione.

![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

**Passaggio 6:** selezionare il tipo di dispositivi in cui verrà eseguita l'installazione. Selezionare **Basic storage Devices** (Dispositivi di archiviazione di base).

Fare clic su **Next** (Avanti) per continuare l'installazione.

![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

**Passaggio 7:** viene visualizzato un messaggio di avviso che indica che i dati esistenti nel disco rigido verranno eliminati. Assicurarsi che il disco rigido non contenga dati importanti e fare clic su **Yes, discard any data** (Sì, elimina tutti i dati).

![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

**Passaggio 8:** immettere il nome host per il server nella **casella di testo Hostname** (Nome host).
Fare clic su **Configure Network** (Configura rete).

Nella finestra **Network Connection** (Connessione di rete), selezionare l'interfaccia di rete. Fare clic sul pulsante **Edit** (Modifica) per configurare le impostazioni IPV4.

![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

**Passaggio 9:** viene visualizzata la finestra **Editing System etho**. Selezionare la casella di controllo **Connect automatically** (Connetti automaticamente). Nella scheda "IPv4 Settings" (Impostazioni IPv4), scegliere il metodo **Manual** (Manuale) e fare clic sul pulsante **Add** (Aggiungi). Fornire i dettagli dell'indirizzo IP statico, della subnet mask, del gateway e del server DNS. Fare clic su **Apply** (Applica) per salvare i dettagli.

![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

**Passaggio 10:** selezionare il fuso orario dalla casella combinata e fare clic su **Next** (Avanti) per continuare.

![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

**Passaggio 11:** immettere la **password radice** e confermarla, quindi fare clic su **Next** (Avanti) per continuare.

![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

**Passaggio 12:** selezionare **Create Custom Layout** (Crea layout personalizzato) come modalità di partizione e fare clic su **Next** (Avanti) per continuare.

![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

**Passaggio 13:** selezionare la partizione **Free** (Gratuita) e fare clic su **Create** (Crea) per creare le partizioni **/**,**/var/crash** e **/home** con **ext4** come tipo di file system. Creare una **partizione di swapping** con **swap** come tipo di file system. Per allocare le dimensioni della partizione, seguire la relativa formula di allocazione come indicato nella tabella seguente.

Nota: il Linux Master Target (MT) non deve utilizzare LVM per il spazi di archiviazione radice o di mantenimento. Linux MT è configurato per impostazione predefinita in modo da evitare l'individuazione di partizioni e dischi LVM.

![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

**Passaggio 14:** dopo la creazione della partizione fare clic su **Next** (Avanti) per continuare.

![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

**Passaggio 15:** se vengono rilevati dispositivi pre-esistenti, verrà visualizzato un messaggio di avviso che chiede di formattarli.

Fare clic su **Format** (Formatta) per formattare il disco rigido con la tabella di partizioni più recente.

![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)



**Passaggio 16**: fare clic su **Write changes to disk** (Scrivi modifiche su disco) per applicare le modifiche della partizione nel disco.

![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

**Passaggio 17:** selezionare l'opzione **Install boot loader** (Installa bootloader) e fare clic su **Next** (Avanti) per installare il bootloader nella partizione radice.

![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)



**Passaggio 18:** viene avviato il processo di installazione. È possibile monitorare l'avanzamento dell'installazione.

![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

**Passaggio 19:** se l'installazione ha esito positivo viene visualizzata la schermata seguente. Fare clic su **Riavvia**

![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Passaggi successivi all'installazione

Per ottenere l'ID SCSI per ogni disco rigido SCSI in una macchina virtuale Linux, è necessario abilitare il parametro "disk.EnableUUID = TRUE ".

Per attivare questo parametro, attenersi alla procedura seguente:

a. Arrestare la macchina virtuale.

b. Fare clic con il pulsante destro del mouse sulla voce della macchina virtuale nel pannello sinistro, quindi selezionare **Modifica impostazioni**.

c. Scegliere la scheda **Options (Opzioni)** .

d. Selezionare l'opzione **Advanced&gt;General item** (Avanzate>Elemento generale) sulla sinistra e fare clic sui **parametri di configurazione** visualizzati a destra.

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

L’opzione relativa ai "parametri di configurazione" è disattivata quando il computer è in esecuzione. Per rendere attiva la scheda, arrestare il computer.

e. Esiste già una riga con il valore **disk.EnableUUID**?

  Se è presente e se il valore è impostato su False, modificare il valore in True (valori True e False sono sensibili in maiuscole).

  Se il valore esiste ed è impostato su True, fare clic su Annulla e testare il comando SCSI all'interno del sistema operativo guest dopo il suo avvio.

f. Se non esiste, fare clic su **Add Row**(Aggiungi riga).

  Aggiungere il disk.EnableUUID nella colonna del nome.

  Impostare il relativo valore come TRUE

Nota: non aggiungere i valori precedenti insieme alle virgolette doppie.

![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>Scaricare e installare i pacchetti aggiuntivi

Nota: assicurarsi che il sistema disponga della connettività Internet prima di scaricare e installare pacchetti aggiuntivi.

```
# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

Con il comando sopra verranno scaricati i 15 pacchetti menzionati di seguito dal repository di CentOS 6.6  e installarli.


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

Nota: se le macchine di origine protette usano il filesystem Reiser o XFS per il dispositivo di avvio o radice, i pacchetti aggiuntivi seguenti devono essere scaricati e installati nel server master Linux di destinazione prima della protezione.

***ReiserFS (Suse11SP3. ReiserFS non è tuttavia il filesystem predefinito in Suse11SP3)***

```
# cd /usr/local

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL, CentOS 7 o versione successiva)***

```
# cd /usr/local

# wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

# yum install device-mapper-multipath 
```
Questa operazione è necessaria per abilitare i pacchetti Multipath sul server MT.

### <a name="download-the-mt-installation-packages"></a>Scaricare i pacchetti di installazione di MT

Scaricare da qui i bit di installazione più recenti per la destinazione master Linux: [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc).

Per scaricarli da sistema Linux, digitare 

```
    # wget https://aka.ms/latestlinuxmobsvc
```

Assicurarsi di scaricare e decomprimere il programma di installazione solo nella home directory. Se viene decompresso nel percorso /usr/Locale, l'installazione avrà esito negativo.

### <a name="apply-custom-configuration-changes"></a>Applicare le modifiche di configurazione personalizzate

Prima di applicare le modifiche di configurazione personalizzate assicurarsi di aver completato le operazioni successive all'installazione

Per applicare le modifiche di configurazione personalizzate, attenersi alla procedura seguente:

1. Copiare il file binario di RHEL6-64 Unified Agent nel sistema operativo appena creato.

2. Eseguire il seguente comando per decomprimere  il file binario.
    ```
    tar -zxvf <File name>
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
3. Eseguire il seguente comando per concedere l'autorizzazione.
    ```
    # chmod 755 ./ApplyCustomChanges.sh
    ```

4. Eseguire il comando per eseguire lo script seguente.
    ```
    # ./ApplyCustomChanges.sh
    ```
Nota: eseguire lo script solo una volta sul server. **RIAVVIARE** il server dopo la corretta esecuzione dello script precedente.

### <a name="add-retention-disk-to-linux-mt-vm"></a>Aggiungere un disco di conservazione a una macchina virtuale Linux MT 

Eseguire la procedura riportata di seguito per creare un disco di conservazione.

**Passaggio 1:** collegare un nuovo disco da **1 TB** alla macchina virtuale di Linux MT e individuare il relativo id Multipath

Usare il comando **multipath -ll** per conoscere l'id Multipath del disco di conservazione.

![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

**Step 2:** Eseguire il comando **mkfs.ext4 /dev/mapper/<Retention disk's multipath id>** per creare un file system nel disco di conservazione.

![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

**Passaggio 3:** una volta completata la creazione del file system, usare i comandi di seguito per montare il disco di conservazione.

![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

**Passaggio 4:** infine creare la voce fstab:
```
vi /etc/fstab 
```
e aggiungere la riga

** /dev/mapper/36000c2989daa2fe6dddcde67f2079afe /mnt/retention ext4 rw 0 0 **

### <a name="install-master-target"></a>Installare la destinazione master


> [!NOTE]
> La versione del server di destinazione master deve essere minore o pari a quella dei server di elaborazione e configurazione. Se la versione del server master di destinazione è superiore, la riprotezione avrà esito positivo, ma la replica avrà esito negativo.
> 

> [!NOTE]
> Prima di installare il server master di destinazione, assicurarsi che il file /etc/hosts nella macchina virtuale contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
> 


1. Eseguire il seguente comando per installare il server master di destinazione. Selezionare il ruolo agente "Master Target".
```
# ./install
```

2. Selezionare il percorso predefinito per l'installazione
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. Selezionare le impostazioni globali da configurare

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. Specificare gli indirizzi IP del server CS

5. Specificare la porta del Server CX, generalmente 9443.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. Copiare la passphrase CS da C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase nel server di configurazione.

7. Attendere il completamento di installazione e registrazione.
### <a name="install-vmware-tools-on-the-master-target-server"></a>Installare gli strumenti VMware nel server master di destinazione

È necessario installare gli strumenti VMware per consentire al server master di destinazione di rilevare gli archivi dati. Se non sono installati gli strumenti, la schermata di riprotezione non elencherà gli archivi dati.

## <a name="next-steps"></a>Passaggi successivi
 

## <a name="common-issues"></a>Problemi comuni



<!--HONumber=Feb17_HO3-->


