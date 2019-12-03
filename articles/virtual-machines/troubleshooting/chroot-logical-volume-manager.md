---
title: Ripristinare macchine virtuali Linux con chroot in cui viene usato LVM (Logical Volume Manager)-macchine virtuali di Azure
description: Ripristino di VM Linux con LVM.
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
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684138"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Risoluzione dei problemi di una VM Linux quando non è possibile accedere alla console seriale di Azure e il layout del disco usa LVM (Logical Volume Manager)

Questa guida alla risoluzione dei problemi è utile per gli scenari in cui una VM Linux non è in avvio, SSH non è possibile e il layout file system sottostante è configurato con LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Eseguire lo snapshot della macchina virtuale non riuscita

Eseguire uno snapshot della macchina virtuale interessata. 

Lo snapshot verrà quindi collegato a una macchina virtuale di **ripristino** . Seguire le istruzioni riportate [qui](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) per informazioni su come creare uno **snapshot**.

## <a name="create-a-rescue-vm"></a>Creare una macchina virtuale di ripristino
È in genere consigliabile una macchina virtuale di ripristino della stessa versione del sistema operativo o di una versione simile. Usare la stessa **area** e il **gruppo di risorse** della VM interessata

## <a name="connect-to-the-rescue-vm"></a>Connettersi alla macchina virtuale di ripristino
Connettersi tramite SSH alla macchina virtuale di **ripristino** . Eleva i privilegi e diventa un utente con privilegi avanzati usando

`sudo su -`

## <a name="attach-the-disk"></a>Connetti il disco
Alleghi un disco alla macchina virtuale di **ripristino** eseguita dallo snapshot eseguito in precedenza.

Portale di Azure-> selezionare i **dischi** della macchina virtuale di **ripristino** > 

![Creare un disco](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Popolare i campi. Assegnare un nome al nuovo disco, selezionare lo stesso gruppo di risorse dello snapshot, della VM interessata e della VM di ripristino.

Il **tipo di origine** è **snapshot** .
Lo **snapshot di origine** è il nome dello **snapshot** creato in precedenza.

![Creazione disco 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Creare un punto di montaggio per il disco collegato.

`mkdir /rescue`

Eseguire il comando **fdisk-l** per verificare che il disco snapshot sia stato collegato ed elencare tutti i dispositivi e le partizioni disponibili

`fdisk -l`

La maggior parte degli scenari, il disco snapshot collegato viene visualizzato come **/dev/sdc** che visualizza due partizioni **/dev/sdc1** e **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Il **\*** indica una partizione di avvio, entrambe le partizioni devono essere montate.

Eseguire il comando **lsblk** per visualizzare il LVM della macchina virtuale interessata

`lsblk`

![Esegui lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Verificare che vengano visualizzati LVM dalla macchina virtuale interessata.
In caso contrario, usare i comandi seguenti per abilitarli ed eseguire di nuovo **lsblk**.
Prima di procedere, verificare che il LVM del disco collegato sia visibile.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Individuare il percorso per montare il volume logico che contiene la partizione/(radice). Contiene i file di configurazione, ad esempio/etc/default/grub

In questo esempio, l'output del comando **lsblk** precedente **rootvg-rootlv** è la **radice** BT corretta da montare e può essere usato nel comando successivo.

L'output del comando successivo indicherà il percorso da montare per la **radice** LV

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Procedere alla montaggio del dispositivo nella directory/Rescue

`mount /dev/rootvg/rootlv /rescue`

Montare la partizione in cui è impostato il **flag di avvio** in/Rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Verificare che i file System del disco collegato siano ora montati correttamente usando il comando **lsblk**

![Esegui lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

o il comando **DF-TH**

![DF](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Ottenere l'accesso chroot

Ottenere l'accesso **chroot** , che consente di eseguire varie correzioni, per ogni distribuzione Linux sono presenti lievi variazioni.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Se viene rilevato un errore, ad esempio:

**chroot: non è stato possibile eseguire il comando '/bin/bash ': nessun file o directory di questo tipo**

tentativo di montaggio del volume logico **usr**

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Quando si eseguono comandi in un ambiente **chroot** , si noti che vengono eseguiti sul disco del sistema operativo collegato e non sulla macchina virtuale di **ripristino** locale. 

È possibile utilizzare i comandi per installare, rimuovere e aggiornare il software. Risolvere i problemi relativi alle macchine virtuali per correggere gli errori.


Eseguire il comando lsblk e/Rescue è ora/e/Rescue/boot è/boot ![chroot](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Eseguire correzioni

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Esempio 1: configurare la macchina virtuale per l'avvio da un kernel diverso

Uno scenario comune consiste nel forzare l'avvio di una macchina virtuale da un kernel precedente, perché il kernel installato corrente potrebbe essere danneggiato o un aggiornamento non è stato completato correttamente.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*procedura dettagliata*

Il comando **grep** elenca i kernel che **grub. cfg** è in grado di riconoscere.
![kernel](./media/chroot-logical-volume-manager/kernels.png)

l' **elenco GRUB2-editenv** Visualizza il kernel che verrà caricato all'avvio successivo ![il kernel predefinito](./media/chroot-logical-volume-manager/kernel-default.png)

**GRUB2-set-default** viene usato per passare a un altro kernel ![set GRUB2](./media/chroot-logical-volume-manager/grub2-set-default.png)

l'elenco **GRUB2-editenv** Visualizza il kernel che verrà caricato all'avvio successivo ![nuovo kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**GRUB2-mkconfig** ricompila grub. cfg usando le versioni richieste ![grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Esempio 2: aggiornare i pacchetti

Un aggiornamento del kernel non riuscito può rendere la macchina virtuale non avviabile.
Montare tutti i volumi logici per consentire la rimozione o la reinstallazione dei pacchetti

Eseguire il comando **LVS** per verificare quali **LVS** sono disponibili per il montaggio, ogni macchina virtuale di cui è stata eseguita la migrazione o che deriva da un altro provider di servizi cloud può variare in base alla configurazione.

Uscire dall'ambiente **chroot** montare la richiesta **LV**

![Funzionalità avanzate](./media/chroot-logical-volume-manager/advanced.png)

Ora accedere di nuovo all'ambiente **chroot** eseguendo

`chroot /rescue`

Tutti LVs devono essere visibili come partizioni montate

![Funzionalità avanzate](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Eseguire una query sul **kernel** installato

![Funzionalità avanzate](./media/chroot-logical-volume-manager/rpm-kernel.png)

Se necessario, rimuovere o aggiornare il **kernel**
![Advanced](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Esempio 3: abilitare la console seriale
Se l'accesso non è stato possibile alla console seriale di Azure, verificare i parametri di configurazione di GRUB per la VM Linux e correggerli. Informazioni dettagliate sono disponibili [in questo documento](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Esempio 4: caricamento del kernel con un volume di swap LVM problematico

Una macchina virtuale potrebbe non riuscire a eseguire l'avvio completo e rientrare nel prompt **Dracut** .
Altre informazioni sull'errore possono essere individuate dalla console seriale di Azure o passare a portale di Azure-> diagnostica di avvio-> log seriale


Potrebbe essere presente un errore simile al seguente:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

Grub. cfg è configurato in questo esempio per caricare un valore LV con il nome **Rd. LVM. lv = VG/SwapVol** e la macchina virtuale non è in grado di individuarlo. Questa riga Mostra come viene caricato il kernel che fa riferimento a LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Rimuovere il LV danneggiato dalla configurazione/etc/default/grub e ricompilare GRUB2. cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Uscire da chroot e scambiare il disco del sistema operativo

Dopo aver ripristinato il problema, continuare a smontare e scollegare il disco dalla macchina virtuale di ripristino, in modo che venga scambiato con il disco del sistema operativo della VM interessato.

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

Scollegare il disco dalla macchina virtuale di ripristino ed eseguire uno scambio su disco.

Selezionare la macchina virtuale dai **dischi** del portale e selezionare **Scollega**
![scollega disco](./media/chroot-logical-volume-manager/detach-disk.png) 

Salva le modifiche ![Salva scollega](./media/chroot-logical-volume-manager/save-detach.png) 

Il disco ora diventa disponibile per consentire lo scambio con il disco del sistema operativo originale della VM interessata.

Spostarsi nella portale di Azure alla macchina virtuale che ha avuto esito negativo e selezionare **dischi** -> **Scambia disco del sistema operativo**
![disco di scambio](./media/chroot-logical-volume-manager/swap-disk.png) 

Completare i campi il disco **scelto** è il disco dello snapshot appena scollegato nel passaggio precedente. È necessario anche il nome della macchina virtuale interessata, quindi selezionare **OK** .

![Nuovo disco del sistema operativo](./media/chroot-logical-volume-manager/new-osdisk.png) 

Se la macchina virtuale esegue lo scambio di disco lo arresterà, riavviare la macchina virtuale al termine dell'operazione di scambio del disco.


## <a name="next-steps"></a>Passaggi successivi
Scopri di più

 [Console seriale di Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Modalità utente singolo](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
