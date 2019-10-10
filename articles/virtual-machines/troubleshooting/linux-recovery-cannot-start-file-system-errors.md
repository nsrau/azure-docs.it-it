---
title: Risolvere i problemi di avvio della macchina virtuale Linux a causa di errori file system | Microsoft Docs
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
ms.openlocfilehash: 298fd336e87d07f9e65221d5e5f539e255c94993
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245331"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Risolvere i problemi di avvio della macchina virtuale Linux a causa di errori file system

Non è possibile connettersi a una macchina virtuale (VM) Linux di Azure usando Secure Shell (SSH). Quando si esegue la funzionalità di diagnostica di avvio in [portale di Azure](https://portal.azure.com/), vengono visualizzate le voci di log simili ai seguenti esempi.

## <a name="examples"></a>Esempi

Di seguito sono riportati alcuni esempi di possibili errori.

### <a name="example-1"></a>Esempio 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Esempio 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Esempio 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Esempio 4 

Questo esempio è causato da un fsck pulito. In questo caso, sono presenti anche dischi dati aggiuntivi collegati alla VM (/dev/sdc1 e/dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Questo problema può verificarsi se il file system non è stato arrestato in modo corretto o in caso di problemi relativi all'archiviazione. I problemi includono errori hardware o software, problemi relativi a driver o programmi, errori di scrittura e così via. È sempre importante avere un backup dei dati critici. Gli strumenti descritti in questo articolo possono contribuire a ripristinare i file System, ma è comunque possibile che si verifichi una perdita di dati.

Per Linux sono disponibili diversi file system Checker. Il più comune per le distribuzioni in Azure è: [FSCK](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)e [Xfs_repair](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Risoluzione

Per risolvere il problema, avviare la macchina virtuale in modalità di emergenza usando la [console seriale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) e usare tale strumento per ripristinare la file System. Se la console seriale non è abilitata nella macchina virtuale o non funziona, vedere la sezione [ripristinare la macchina virtuale offline](#repair-the-vm-offline) di questo articolo.

## <a name="use-the-serial-console"></a>Usare la console seriale

1. Connettersi alla console seriale.

   > [!Note]
   > Per ulteriori informazioni sull'utilizzo della console seriale per Linux, vedere:
   > * [Usare la console seriale per accedere a GRUB e alla modalità utente singolo](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Usare la console seriale per le chiamate SysRq e NMI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Selezionare il pulsante icona di alimentazione, quindi selezionare Riavvia macchina virtuale. Se la console seriale non è abilitata o non è connessa correttamente, il pulsante non verrà visualizzato.

   ![IMMAGINE](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Avviare la macchina virtuale in modalità di emergenza.

4. Immettere la password dell'account radice per accedere alla modalità di emergenza.

5. Usare xfs_repair con l'opzione-n per rilevare gli errori nel file system. Nell'esempio seguente si presuppone che la partizione di sistema sia/dev/sda1. Sostituirlo con il valore appropriato per la macchina virtuale:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Eseguire il comando seguente per ripristinare la file system:

   ```
   xfs_repair /dev/sda1
   ```

7. Se viene visualizzato il messaggio di errore "errore: Il file System presenta importanti modifiche ai metadati in un log che deve essere riprodotto ", creare una directory temporanea e montare il file System:

   ```
   mkdir /temp
   mount /dev/sda2 /temp
   ```

8. Se il montaggio del disco non riesce, eseguire il comando xfs_repair con l'opzione-L (Force log zero):

   ```
   xfs_repair /dev/sda2 -L
   ```

9. Provare quindi a montare il file system. Se il disco è stato montato correttamente, verrà visualizzato l'output seguente:
 
   ```
   XFS (sda2): Mounting V1 Filesystem
   XFS (sda2): Ending clean mount
   ```

10. Riavviare la macchina virtuale, quindi controllare se il problema è stato risolto.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Riparare la macchina virtuale in modalità offline

1. Alleghi il disco di sistema della macchina virtuale come disco dati a una macchina virtuale di ripristino (qualsiasi VM Linux funzionante). A tale scopo, è possibile usare i [comandi dell'interfaccia](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) della riga di comando oppure è possibile automatizzare la configurazione della macchina virtuale di ripristino usando i comandi di ripristino della [macchina virtuale](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Individuare l'etichetta dell'unità del disco di sistema collegato. In questo caso, si presuppone che l'etichetta del disco di sistema collegato sia/dev/sdc1. Sostituirlo con il valore appropriato per la macchina virtuale.

3. Usare xfs_repair con l'opzione-n per rilevare gli errori nel file system.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Eseguire il comando seguente per ripristinare la file system:

   ```
   xfs_repair /dev/sdc1
   ```

5. Se viene visualizzato il messaggio di errore "errore: Il file System presenta importanti modifiche ai metadati in un log che deve essere riprodotto ", creare una directory temporanea e montare il file System:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Se il montaggio del disco non riesce, eseguire il comando xfs_repair con l'opzione-L (Force log zero):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Provare quindi a montare il file system. Se il disco è stato montato correttamente, verrà visualizzato l'output seguente:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Smontare e scollegare il disco rigido virtuale originale, quindi creare una macchina virtuale dal disco di sistema originale. A tale scopo, è possibile usare i [comandi dell'interfaccia](troubleshoot-recovery-disks-linux.md) della riga di comando o i comandi di ripristino della [macchina virtuale](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) se sono stati usati per creare la macchina virtuale di ripristino.

8. Controllare se il problema è stato risolto.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di una VM Linux connettendo il disco del sistema operativo a una macchina virtuale di ripristino con l'2,0 interfaccia della riga di comando](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Usare il portale per aggiungere un disco dati a una VM Linux](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

