---
title: Risolvere i problemi di avvio della macchina virtuale Linux a causa di errori fstab | Microsoft Docs
description: Spiega il motivo per cui non è possibile avviare la macchina virtuale Linux e come risolvere il problema.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 868a0238092786d0999a6a41de71d30011bbef7a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245344"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Risolvere i problemi di avvio della macchina virtuale Linux a causa di errori fstab

Non è possibile connettersi a una macchina virtuale (VM) Linux di Azure usando una connessione Secure Shell (SSH). Quando si esegue la funzionalità di [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) nella [portale di Azure](https://portal.azure.com/), vengono visualizzate le voci di log simili agli esempi seguenti:

## <a name="examples"></a>Esempi

Di seguito sono riportati alcuni esempi di possibili errori.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Esempio 1: Un disco viene montato dall'ID SCSI anziché dall'identificatore univoco universale (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type “journalctl -xb” to viewsystem logs, “systemctl reboot” to reboot, “systemctl default” to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Esempio 2 Un dispositivo non collegato è mancante in CentOS

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device (“nofail” fstab option may be used to skip this device)
/dev/sdd1: nonexistent device (“nofail” fstab option may be used to skip this device)
/dev/sde1: nonexistent device (“nofail” fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Esempio 3: Non è possibile avviare una macchina virtuale a causa di un errore di configurazione di fstab o perché il disco non è più collegato

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Esempio 4: Una voce di log seriale Mostra un UUID errato

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run ‘setenforce 1’ to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Questo problema può verificarsi se la sintassi fstab (file System Table) non è corretta o se un disco dati richiesto di cui è stato eseguito il mapping a una voce nel file "/etc/fstab" non è collegato alla macchina virtuale.

## <a name="resolution"></a>Risoluzione

Per risolvere questo problema, avviare la macchina virtuale in modalità di emergenza usando la console seriale per le macchine virtuali di Azure. Usare quindi lo strumento per ripristinare la file system. Se la console seriale non è abilitata nella macchina virtuale, passare alla sezione [ripristinare la macchina virtuale offline](#repair-the-vm-offline) .

## <a name="use-the-serial-console"></a>Usare la console seriale

1. Connettersi alla [console seriale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Accedere al sistema usando un utente e una password locali.

   > [!Note]
   > Non è possibile usare una chiave SSH per accedere al sistema nella console seriale.

3. Cercare l'errore che indica che il disco non è stato montato. Nell'esempio seguente il sistema sta tentando di aggiungere un disco che non era più presente:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Connettersi alla macchina virtuale usando la password radice (VM basate su Red Hat).

5. Utilizzare l'editor di testo preferito per aprire il file fstab. Dopo aver montato il disco, eseguire il comando seguente per nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Esaminare i file system elencati. Ogni riga nel file fstab indica una file system montata all'avvio della macchina virtuale. Per ulteriori informazioni sulla sintassi del file fstab, eseguire il comando man fstab. Per risolvere un errore di avvio, esaminare ogni riga per assicurarsi che sia corretta sia nella struttura che nel contenuto.

   > [!Note]
   > * I campi di ogni riga sono separati da tabulazioni o spazi. Le righe vuote vengono ignorate. Le righe che dispongono di un segno di cancelletto (#) come primo carattere sono commenti. Le righe commentate possono rimanere nel file fstab, ma non verranno elaborate. Si consiglia di commentare le righe fstab che non si è sicuri di fare anziché rimuovere le righe.
   > * Per il ripristino e l'avvio della macchina virtuale, le partizioni file system devono essere le uniche partizioni necessarie. È possibile che la macchina virtuale riscontri errori dell'applicazione relativi a partizioni aggiuntive commentate. Tuttavia, la macchina virtuale verrà avviata senza le partizioni aggiuntive. In un secondo momento è possibile rimuovere il commento dalle righe commentate.
   > * Si consiglia di montare i dischi dati nelle macchine virtuali di Azure usando l'UUID della partizione file system. Ad esempio, eseguire il comando seguente: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Per determinare l'UUID del file system, eseguire il comando blkid. Per ulteriori informazioni sulla sintassi, eseguire il comando blkid di Man.
   > * L'opzione nofail consente di verificare che la macchina virtuale venga avviata anche se il file system è danneggiato o se il file system non esiste all'avvio. Si consiglia di utilizzare l'opzione nofail nel file fstab per consentire l'avvio per continuare dopo che si sono verificati errori nelle partizioni non necessarie per l'avvio della macchina virtuale.

7. Modificare o impostare come commento le righe errate o non necessarie nel file fstab per consentire l'avvio corretto della macchina virtuale.

8. Salvare le modifiche apportate al file fstab.

9. Riavviare la macchina virtuale.

10. Se il commento o la correzione delle voci ha avuto esito positivo, il sistema deve raggiungere una richiesta bash nel portale. Verificare se è possibile connettersi alla macchina virtuale.

11. Controllare i punti di montaggio quando si esegue il test di qualsiasi modifica fstab eseguendo il comando mount-a. Se non sono presenti errori, i punti di montaggio dovrebbero essere validi.

## <a name="repair-the-vm-offline"></a>Riparare la macchina virtuale in modalità offline

1. Alleghi il disco di sistema della macchina virtuale come disco dati a una macchina virtuale di ripristino (qualsiasi VM Linux funzionante). A tale scopo, è possibile usare i [comandi dell'interfaccia](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) della riga di comando oppure è possibile automatizzare la configurazione della macchina virtuale di ripristino usando i comandi di ripristino della [macchina virtuale](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Dopo aver montato il disco di sistema come disco dati nella macchina virtuale di ripristino, eseguire il backup del file fstab prima di apportare modifiche, quindi seguire i passaggi successivi per correggere il file fstab.

3.  Cercare l'errore che indica che il disco non è stato montato. Nell'esempio seguente il sistema sta tentando di aggiungere un disco che non era più presente:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Connettersi alla macchina virtuale usando la password radice (VM basate su Red Hat).

5. Utilizzare l'editor di testo preferito per aprire il file fstab. Dopo aver montato il disco, eseguire il comando seguente per nano. Assicurarsi di usare il file fstab che si trova sul disco montato e non il file fstab presente nella macchina virtuale di ripristino.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Esaminare i file system elencati. Ogni riga nel file fstab indica una file system montata all'avvio della macchina virtuale. Per ulteriori informazioni sulla sintassi del file fstab, eseguire il comando man fstab. Per risolvere un errore di avvio, esaminare ogni riga per assicurarsi che sia corretta sia nella struttura che nel contenuto.

   > [!Note]
   > * I campi di ogni riga sono separati da tabulazioni o spazi. Le righe vuote vengono ignorate. Le righe che dispongono di un segno di cancelletto (#) come primo carattere sono commenti. Le righe commentate possono rimanere nel file fstab, ma non verranno elaborate. Si consiglia di commentare le righe fstab che non si è sicuri di fare anziché rimuovere le righe.
   > * Per il ripristino e l'avvio della macchina virtuale, le partizioni file system devono essere le uniche partizioni necessarie. È possibile che la macchina virtuale riscontri errori dell'applicazione relativi a partizioni aggiuntive commentate. Tuttavia, la macchina virtuale verrà avviata senza le partizioni aggiuntive. In un secondo momento è possibile rimuovere il commento dalle righe commentate.
   > * Si consiglia di montare i dischi dati nelle macchine virtuali di Azure usando l'UUID della partizione file system. Ad esempio, eseguire il comando seguente: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Per determinare l'UUID del file system, eseguire il comando blkid. Per ulteriori informazioni sulla sintassi, eseguire il comando blkid di Man. Si noti che il disco che si vuole ripristinare è ora montato in una nuova macchina virtuale. Sebbene gli UUID siano coerenti, gli ID di partizione del dispositivo (ad esempio, "/dev/sda1") sono diversi in questa macchina virtuale. Le partizioni file system della VM non riuscita originale che si trovano in un disco rigido virtuale non di sistema non sono disponibili per la VM di ripristino [usando i comandi dell'interfaccia](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)della riga di comando.
   > * L'opzione nofail consente di verificare che la macchina virtuale venga avviata anche se il file system è danneggiato o se il file system non esiste all'avvio. Si consiglia di utilizzare l'opzione nofail nel file fstab per consentire l'avvio per continuare dopo che si sono verificati errori nelle partizioni non necessarie per l'avvio della macchina virtuale.

7. Modificare o impostare come commento le righe errate o non necessarie nel file fstab per consentire l'avvio corretto della macchina virtuale.

8. Salvare le modifiche apportate al file fstab.

9. Riavviare la macchina virtuale o ricompilare la VM originale.

10. Se il commento o la correzione delle voci ha avuto esito positivo, il sistema deve raggiungere una richiesta bash nel portale. Verificare se è possibile connettersi alla macchina virtuale.

11. Controllare i punti di montaggio quando si esegue il test di qualsiasi modifica fstab eseguendo il comando mount-a. Se non sono presenti errori, i punti di montaggio dovrebbero essere validi.

12. Smontare e scollegare il disco rigido virtuale originale, quindi creare una macchina virtuale dal disco di sistema originale. A tale scopo, è possibile usare i [comandi dell'interfaccia](troubleshoot-recovery-disks-linux.md) della riga di comando o i comandi di ripristino della [macchina virtuale](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) se sono stati usati per creare la macchina virtuale di ripristino.

13. Dopo aver creato di nuovo la macchina virtuale ed è possibile connettersi tramite SSH, intraprendere le azioni seguenti:
    * Esaminare le righe fstab modificate o impostate come commenti durante il ripristino.
    * Assicurarsi di usare UUID e l'opzione nofail in modo appropriato.
    * Testare le modifiche fstab prima di riavviare la macchina virtuale. Per eseguire questa operazione, usare il comando seguente: ``$ sudo mount -a``
    * Creare una copia aggiuntiva del file fstab corretto da usare negli scenari di ripristino futuri.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di una VM Linux connettendo il disco del sistema operativo a una macchina virtuale di ripristino con l'2,0 interfaccia della riga di comando](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Risolvere i problemi di una VM Linux connettendo il disco del sistema operativo a una VM di ripristino usando il portale di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

