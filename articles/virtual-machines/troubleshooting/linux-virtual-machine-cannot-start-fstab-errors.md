---
title: Risolvere i problemi di avvio di una macchina virtuale Linux a causa di errori fstab . Documenti Microsoft
description: Spiega perché non è possibile avviare la macchina virtuale Linux e come risolvere il problema.
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
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351141"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Risolvere i problemi di avvio di macchine virtuali Linux a causa di errori fstabTroubleshoot Linux VM starting issues due to fstab errors

Non è possibile connettersi a una macchina virtuale (VM) di Azure Linux usando una connessione Secure Shell (SSH). Quando si esegue la funzionalità Diagnostica di [avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) nel portale di [Azure](https://portal.azure.com/), vengono visualizzate voci di log simili agli esempi seguenti:

## <a name="examples"></a>Esempi

Di seguito sono riportati alcuni esempi di possibili errori.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Esempio 1: un disco viene montato in base all'ID SCSI anziché all'identificatore univoco universale (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Esempio 2: Un dispositivo non collegato non è presente in CentOS

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

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

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Esempio 3: Impossibile avviare una macchina virtuale a causa di una configurazione errata di fstab o perché il disco non è più collegatoExample 3: A VM cannot start because of an fstab misconfiguration or because the disk is no longer attached

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Esempio 4: una voce di registro seriale mostra un UUID non correttoExample 4: A serial log entry shows an incorrect UUID

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
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Questo problema può verificarsi se la sintassi della tabella dei file system (fstab) non è corretta o se un disco dati richiesto mappato a una voce nel file "/etc/fstab" non è collegato alla macchina virtuale.

## <a name="resolution"></a>Risoluzione

Per risolvere questo problema, avviare la macchina virtuale in modalità di emergenza utilizzando la console seriale per le macchine virtuali di Azure.To resolve this problem, start the VM in emergency mode by using the serial console for Azure Virtual Machines. Quindi utilizzare lo strumento per riparare il file system. Se la console seriale non è abilitata nella macchina virtuale, passare alla sezione [Ripristinare la macchina virtuale offline.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Usare la console seriale

### <a name="using-single-user-mode"></a>Utilizzo della modalità utente singolo

1. Connettersi [alla console seriale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Utilizzare la console seriale per passare dalla modalità utente singolo per la [modalità utente singolo](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
3. Una volta che la macchina virtuale è stata avviata in modalità utente singolo. Utilizzare l'editor di testo preferito per aprire il file fstab. 

   ```
   # nano /etc/fstab
   ```

4. Esaminare i file system elencati. Ogni riga nel file fstab indica un file system montato all'avvio della macchina virtuale. Per ulteriori informazioni sulla sintassi del file fstab, eseguire il comando man fstab. Per risolvere un errore di avvio, esaminare ogni riga per assicurarsi che sia corretta sia nella struttura che nel contenuto.

   > [!Note]
   > * I campi di ogni riga sono separati da tabulazioni o spazi. Le righe vuote vengono ignorate. Le righe che hanno un simbolo di cancelletto (-) come primo carattere sono commenti. Le righe commentate possono rimanere nel file fstab, ma non verranno elaborate. Si consiglia di commentare le righe fstab di cui non si è sicuri invece di rimuoverle.
   > * Per il ripristino e l'avvio della macchina virtuale, le partizioni del file system devono essere le uniche partizioni necessarie. La macchina virtuale potrebbe verificarsi errori dell'applicazione relativi a altre partizioni commentate. Tuttavia, la macchina virtuale deve essere avviata senza le partizioni aggiuntive. In seguito è possibile rimuovere il commento da tutte le righe commentate.
   > * È consigliabile montare i dischi dati nelle macchine virtuali di Azure usando l'UUID della partizione del file system. Ad esempio, eseguire il comando seguente:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Per determinare l'UUID del file system, eseguire il comando blkid. Per ulteriori informazioni sulla sintassi, eseguire il comando man blkid.
   > * L'opzione nofail consente di assicurarsi che la macchina virtuale venga avviata anche se il file system è danneggiato o il file system non esiste all'avvio. È consigliabile usare l'opzione nofail nel file fstab per consentire all'avvio di continuare dopo che si verificano errori nelle partizioni che non sono necessarie per l'avvio della macchina virtuale.

5. Modificare o impostare come commento tutte le righe non corrette o non necessarie nel file fstab per consentire la corretta avvio della macchina virtuale.

6. Salvare le modifiche apportate al file fstab.

7. Riavviare il vm utilizzando il comando seguente.
   
   ```
   # reboot -f
   ```
> [!Note]
   > È inoltre possibile utilizzare il comando "ctrl-x" che riavvierebbe anche la macchina virtuale.


8. Se il commento o la correzione delle voci ha avuto esito positivo, il sistema deve raggiungere un prompt bash nel portale. Verificare se è possibile connettersi alla macchina virtuale.

### <a name="using-root-password"></a>Utilizzo della password principale

1. Connettersi [alla console seriale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Accedere al sistema utilizzando un utente locale e una password.

   > [!Note]
   > Non è possibile utilizzare una chiave SSH per accedere al sistema nella console seriale.

3. Cercare l'errore che indica che il disco non è stato montato. Nell'esempio seguente, il sistema stava tentando di collegare un disco che non era più presente:

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

4. Connettersi alla macchina virtuale usando la password radice (macchine virtuali basate su Cappello rosso).

5. Utilizzare l'editor di testo preferito per aprire il file fstab. Dopo aver montato il disco, eseguire il comando seguente per Nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Esaminare i file system elencati. Ogni riga nel file fstab indica un file system montato all'avvio della macchina virtuale. Per ulteriori informazioni sulla sintassi del file fstab, eseguire il comando man fstab. Per risolvere un errore di avvio, esaminare ogni riga per assicurarsi che sia corretta sia nella struttura che nel contenuto.

   > [!Note]
   > * I campi di ogni riga sono separati da tabulazioni o spazi. Le righe vuote vengono ignorate. Le righe che hanno un simbolo di cancelletto (-) come primo carattere sono commenti. Le righe commentate possono rimanere nel file fstab, ma non verranno elaborate. Si consiglia di commentare le righe fstab di cui non si è sicuri invece di rimuoverle.
   > * Per il ripristino e l'avvio della macchina virtuale, le partizioni del file system devono essere le uniche partizioni necessarie. La macchina virtuale potrebbe verificarsi errori dell'applicazione relativi a altre partizioni commentate. Tuttavia, la macchina virtuale deve essere avviata senza le partizioni aggiuntive. In seguito è possibile rimuovere il commento da tutte le righe commentate.
   > * È consigliabile montare i dischi dati nelle macchine virtuali di Azure usando l'UUID della partizione del file system. Ad esempio, eseguire il comando seguente:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Per determinare l'UUID del file system, eseguire il comando blkid. Per ulteriori informazioni sulla sintassi, eseguire il comando man blkid.
   > * L'opzione nofail consente di assicurarsi che la macchina virtuale venga avviata anche se il file system è danneggiato o il file system non esiste all'avvio. È consigliabile usare l'opzione nofail nel file fstab per consentire all'avvio di continuare dopo che si verificano errori nelle partizioni che non sono necessarie per l'avvio della macchina virtuale.

7. Modificare o impostare come commento tutte le righe non corrette o non necessarie nel file fstab per consentire la corretta avvio della macchina virtuale.

8. Salvare le modifiche apportate al file fstab.

9. Riavviare la macchina virtuale.

10. Se il commento o la correzione delle voci ha avuto esito positivo, il sistema deve raggiungere un prompt bash nel portale. Verificare se è possibile connettersi alla macchina virtuale.

11. Controllare i punti di montaggio quando si verifica qualsiasi modifica fstab eseguendo il comando mount –a. Se non ci sono errori, i punti di montaggio dovrebbero essere buoni.

## <a name="repair-the-vm-offline"></a>Riparare la macchina virtuale in modalità offline

1. Collegare il disco di sistema della macchina virtuale come disco dati a una macchina virtuale di ripristino (qualsiasi macchina virtuale Linux funzionante). A tale scopo, è possibile usare i [comandi dell'interfaccia della riga](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) di comando oppure automatizzare la configurazione della macchina virtuale di ripristino usando i comandi di ripristino della macchina [virtuale.](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

2. Dopo aver montato il disco di sistema come disco dati nella macchina virtuale di ripristino, eseguire il backup del file fstab prima di apportare modifiche e quindi seguire i passaggi successivi per correggere il file fstab.

3.    Cercare l'errore che indica che il disco non è stato montato. Nell'esempio seguente, il sistema stava tentando di collegare un disco che non era più presente:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Connettersi alla macchina virtuale usando la password radice (macchine virtuali basate su Cappello rosso).

5. Utilizzare l'editor di testo preferito per aprire il file fstab. Dopo aver montato il disco, eseguire il comando seguente per Nano. Assicurarsi di lavorare sul file fstab che si trova sul disco montato e non sul file fstab che si trova nella macchina virtuale di ripristino.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Esaminare i file system elencati. Ogni riga nel file fstab indica un file system montato all'avvio della macchina virtuale. Per ulteriori informazioni sulla sintassi del file fstab, eseguire il comando man fstab. Per risolvere un errore di avvio, esaminare ogni riga per assicurarsi che sia corretta sia nella struttura che nel contenuto.

   > [!Note]
   > * I campi di ogni riga sono separati da tabulazioni o spazi. Le righe vuote vengono ignorate. Le righe che hanno un simbolo di cancelletto (-) come primo carattere sono commenti. Le righe commentate possono rimanere nel file fstab, ma non verranno elaborate. Si consiglia di commentare le righe fstab di cui non si è sicuri invece di rimuoverle.
   > * Per il ripristino e l'avvio della macchina virtuale, le partizioni del file system devono essere le uniche partizioni necessarie. La macchina virtuale potrebbe verificarsi errori dell'applicazione relativi a altre partizioni commentate. Tuttavia, la macchina virtuale deve essere avviata senza le partizioni aggiuntive. In seguito è possibile rimuovere il commento da tutte le righe commentate.
   > * È consigliabile montare i dischi dati nelle macchine virtuali di Azure usando l'UUID della partizione del file system. Ad esempio, eseguire il comando seguente:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Per determinare l'UUID del file system, eseguire il comando blkid. Per ulteriori informazioni sulla sintassi, eseguire il comando man blkid. Si noti che il disco che si desidera ripristinare è ora montato in una nuova macchina virtuale. Anche se gli UUID devono essere coerenti, gli ID della partizione del dispositivo (ad esempio, "/dev/sda1") sono diversi in questa macchina virtuale. Le partizioni del file system della macchina virtuale in errore originale che si trovano in un disco rigido virtuale non di sistema non sono disponibili per la macchina virtuale di ripristino [tramite i comandi dell'interfaccia della riga di comando.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)
   > * L'opzione nofail consente di assicurarsi che la macchina virtuale venga avviata anche se il file system è danneggiato o il file system non esiste all'avvio. È consigliabile usare l'opzione nofail nel file fstab per consentire all'avvio di continuare dopo che si verificano errori nelle partizioni che non sono necessarie per l'avvio della macchina virtuale.

7. Modificare o impostare come commento tutte le righe non corrette o non necessarie nel file fstab per consentire la corretta avvio della macchina virtuale.

8. Salvare le modifiche apportate al file fstab.

9. Riavviare la macchina virtuale o ricompilare la macchina virtuale originale.

10. Se il commento o la correzione delle voci ha avuto esito positivo, il sistema deve raggiungere un prompt bash nel portale. Verificare se è possibile connettersi alla macchina virtuale.

11. Controllare i punti di montaggio quando si verifica qualsiasi modifica fstab eseguendo il comando mount –a. Se non ci sono errori, i punti di montaggio dovrebbero essere buoni.

12. Smontare e scollegare il disco rigido virtuale originale e quindi creare una macchina virtuale dal disco di sistema originale. A tale scopo, è possibile usare i [comandi dell'interfaccia della riga](troubleshoot-recovery-disks-linux.md) di comando o i comandi di ripristino della macchina [virtuale](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) se sono stati usati per creare la macchina virtuale di ripristino.

13. Dopo aver creato nuovamente la macchina virtuale ed è possibile connettersi ad essa tramite SSH, eseguire le azioni seguenti:After you create the VM again and you can connect to it through SSH, take the following actions:
    * Esaminare una delle righe fstab che sono state modificate o commentate durante il recupero.
    * Assicurarsi che si sta utilizzando UUID e l'opzione nofail in modo appropriato.
    * Testare le modifiche fstab prima di riavviare la macchina virtuale. A tale scopo, utilizzare il comando seguente:``$ sudo mount -a``
    * Creare una copia aggiuntiva del file fstab corretto da utilizzare in scenari di ripristino futuri.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi relativi a una VM Linux collegando il disco del sistema operativo a una VM di ripristino tramite l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Risolvere i problemi relativi a una macchina virtuale Linux collegando il disco del sistema operativo a una macchina virtuale di ripristino nel portale di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

