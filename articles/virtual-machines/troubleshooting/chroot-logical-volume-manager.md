---
title: Ripristinare macchine virtuali Linux usando chroot in cui viene usato LVM (Logical Volume Manager) - Macchine virtuali di AzureRecover Linux VMs using chroot where LVM (Logical Volume Manager) is used - Azure VMs
description: Ripristino di macchine virtuali Linux con Macchine virtuali.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684138"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Risoluzione dei problemi di una macchina virtuale Linux quando non è disponibile l'accesso alla console seriale di Azure e il layout del disco usa LVM (Logical Volume Manager)

Questa guida alla risoluzione dei problemi è vantaggiosa per gli scenari in cui una macchina virtuale Linux non è in avvio, ssh non è possibile e il layout del file system sottostante è configurato con LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Creare uno snapshot della macchina virtuale in errore

Creare uno snapshot della macchina virtuale interessata. 

Lo snapshot verrà quindi collegato a una macchina virtuale di **ripristino.** Seguire le istruzioni [riportate](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) su come creare **un'istantanea.**

## <a name="create-a-rescue-vm"></a>Creare una macchina virtuale di ripristinoCreate a rescue VM
Di solito è consigliata una macchina virtuale di salvataggio della stessa versione del sistema operativo o simile. Usare la stessa **area** e lo stesso gruppo di risorse della macchina virtuale interessataUse the same region and **resource group** of the affected VM

## <a name="connect-to-the-rescue-vm"></a>Connettersi alla macchina virtuale di salvataggioConnect to the rescue VM
Connettersi usando ssh nella macchina virtuale di **salvataggio.** Elevare i privilegi e diventare utente con privilegi avanzati utilizzando

`sudo su -`

## <a name="attach-the-disk"></a>Collegare il disco
Collegare un disco alla macchina virtuale di **ripristino** creata dallo snapshot creato in precedenza.

Portale di Azure -> selezionare la macchina virtuale di ripristino -> **dischiAzure** portal -select the **rescue** VM -> Disks 

![Creare un disco](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Compilare i campi. Assegnare un nome al nuovo disco, selezionare lo stesso gruppo di risorse dello snapshot, della macchina virtuale interessata e della macchina virtuale di ripristino.

Il **tipo di origine** è **Snapshot** .
Lo **snapshot di origine** è il nome dello **snapshot** creato in precedenza.

![creare il disco 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Creare un punto di montaggio per il disco collegato.

`mkdir /rescue`

Eseguire il comando **fdisk -l** per verificare che il disco snapshot sia stato collegato ed elencare tutti i dispositivi e le partizioni disponibili

`fdisk -l`

La maggior parte degli scenari, il disco snapshot collegato verrà visualizzato come **/dev/sdc** visualizzando due partizioni **/dev/sdc1** e **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Indica **\*** una partizione di avvio, entrambe le partizioni devono essere montate.

Eseguire il comando lsblk per visualizzare le Macchine virtuali della macchina virtuale interessataRun the command **lsblk** to see the LVMs of the affected VM

`lsblk`

![Eseguire lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Verificare se vengono visualizzate le Macchine virtuali della macchina virtuale interessata.
In caso contrario, utilizzare i comandi seguenti per abilitarli ed eseguire nuovamente **lsblk**.
Assicurarsi di avere le Macchine Virtuali dal disco collegato visibile prima di procedere.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Individuare il percorso per montare il volume logico che contiene la partizione / (radice). Ha i file di configurazione come /etc/default/grub

In questo esempio, prendendo l'output dal comando **lsblk** precedente **rootvg-rootlv** è il l'LV **radice** corretto da montare e può essere utilizzato nel comando successivo.

L'output del comando successivo mostrerà il percorso da montare per la **radice** LV

`pvdisplay -m | grep -i rootlv`

![Radicedino](./media/chroot-logical-volume-manager/locate-rootlv.png)

Procedere al montaggio del dispositivo nella directory /rescue

`mount /dev/rootvg/rootlv /rescue`

Montare la partizione con il **flag Boot** impostato su /rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Verificare che i file system del disco collegato siano ora montati correttamente utilizzando il comando **lsblk**

![Eseguire lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

o il comando **df -Th**

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Ottenere l'accesso chroot

Ottenere **l'accesso chroot,** che vi permetterà di eseguire varie correzioni, esistono lievi variazioni per ogni distribuzione Linux.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Se si verifica un errore come:

**chroot: impossibile eseguire il comando '/bin/bash': nessun file o directory di questo tipo**

tentare di montare il volume **logico usr**

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Quando si eseguono comandi in un ambiente **chroot,** si noti che vengono eseguiti sul disco del sistema operativo collegato e non sulla macchina virtuale di **ripristino** locale. 

I comandi possono essere utilizzati per installare, rimuovere e aggiornare il software. Risolvere i problemi relativi alle macchine virtuali per correggere gli errori.


Eseguire il comando lsblk e il /rescue è ora ![/ e /rescue/boot è /boot Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Eseguire correzioni

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Esempio 1 - configurare la macchina virtuale per l'avvio da un kernel diversoExample 1 - configure the VM to boot from a different kernel

Uno scenario comune consiste nell'imporre l'avvio di una macchina virtuale da un kernel precedente poiché il kernel attualmente installato potrebbe essere danneggiato o un aggiornamento non completato correttamente.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*Procedura dettagliata*

Il comando **grep** elenca i kernel di cui **grub.cfg** è a conoscenza.
![Kernel](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv elenco** visualizza quale kernel verrà ![caricato al prossimo avvio Kernel predefinito](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** viene utilizzato per ![passare a un altro set Grub2 del kernel](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** elenco visualizza quale kernel verrà ![caricato al prossimo avvio Nuovo kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** ricostruisce grub.cfg utilizzando ![le versioni richieste Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Esempio 2 - pacchetti di aggiornamentoExample 2 - upgrade packages

Un aggiornamento del kernel non riuscito può rendere la macchina virtuale non avviabile.
Montare tutti i volumi logici per consentire la rimozione o la reinstallazione dei pacchetti

Eseguire il comando **lvs** per verificare quali **lv** sono disponibili per il montaggio, ogni macchina virtuale, che è stata migrata o proviene da un altro Cloud Provider varierà nella configurazione.

Uscire dall'ambiente **chroot** montare il **LV** richiesto

![Avanzate](./media/chroot-logical-volume-manager/advanced.png)

Ora accedere di nuovo all'ambiente **chroot** eseguendo

`chroot /rescue`

Tutti i VL devono essere visibili come partizioni montate

![Avanzate](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Interrogare il **kernel** installato

![Avanzate](./media/chroot-logical-volume-manager/rpm-kernel.png)

Se necessario, rimuovere o aggiornare il **kernel**
![Advanced](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Esempio 3 - abilitare la console serialeExample 3 - enable Serial Console
Se l'accesso alla console seriale di Azure non è stato possibile, verificare i parametri di configurazione GRUB per la macchina virtuale Linux e correggerli. Informazioni dettagliate possono essere trovate [in questo documento](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Esempio 4 - caricamento del kernel con volume di swap LVM problematico

Una macchina virtuale potrebbe non riuscire ad avviarsi completamente e rilascia nel prompt **di taglio.**
Ulteriori dettagli dell'errore possono essere individuati dalla console seriale di Azure o passare al portale di Azure -> diagnostica di avvio -> log di serie


Un errore simile a questo può essere presente:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

Il file grub.cfg è configurato in questo esempio per caricare un LV con il nome **rd.lvm.lv.VG/SwapVol** e la macchina virtuale non è in grado di individuarla. Questa riga mostra come viene caricato il kernel facendo riferimento al LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Rimuovere il LV incriminato dalla configurazione /etc/default/grub e ricostruire grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Uscire da chroot e scambiare il disco del sistema operativo

Dopo aver riparato il problema, procedere allo smontaggio e allo scollegare il disco dalla macchina virtuale di ripristino consentendone lo scambio con il disco del sistema operativo vm interessato.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Scollegare il disco dalla macchina virtuale di ripristino ed eseguire uno scambio di schietto.

Selezionare la macchina virtuale dai **dischi** del portale e selezionare **Scollega**
![disco](./media/chroot-logical-volume-manager/detach-disk.png) 

Salvare le ![modifiche Salva scollegamento](./media/chroot-logical-volume-manager/save-detach.png) 

Il disco sarà ora disponibile consentendone lo scambio con il disco del sistema operativo originale della macchina virtuale interessata.

Passare al portale di Azure alla macchina virtuale in errore e selezionare **Disco** -> **Scambia disco**
![scambia disco](./media/chroot-logical-volume-manager/swap-disk.png) 

Compilare i campi il **Disco Scegli** è il disco snapshot appena scollegato nel passaggio precedente. Anche il nome della macchina virtuale interessata è obbligatorio, quindi selezionare **OK**

![Nuovo disco os](./media/chroot-logical-volume-manager/new-osdisk.png) 

Se la macchina virtuale è in esecuzione, Lo scambio di dischi lo arresterà, riavviarla al termine dell'operazione di scambio del disco.


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su:

 [Console seriale di AzureAzure Serial Console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Modalità utente singolo](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
