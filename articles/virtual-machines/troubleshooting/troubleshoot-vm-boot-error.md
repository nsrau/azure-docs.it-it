---
title: Avvio di macchine virtuali Linux in grub rescue
description: Non è stato possibile avviare la macchina virtuale perché la macchina virtuale è passata a una console di ripristino
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561950"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Avvio di macchine virtuali Linux in grub rescue

È stato rilevato che la macchina virtuale (VM) è stata inserita in una console di ripristino. Il problema si verifica quando la macchina virtuale Linux presenta modifiche del kernel applicate di recente, ad esempio un aggiornamento del kernel, e non viene più avviata correttamente a causa di errori del kernel durante il processo di avvio. Durante il processo di avvio, quando il caricatore di avvio tenta di individuare il kernel Linux e di passare il controllo di avvio, la macchina virtuale entra in una console di ripristino in caso di errore della consegna.

Se non è possibile connettersi a una macchina virtuale in futuro, è possibile visualizzare uno screenshot della macchina virtuale usando il pannello diagnostica di avvio nella portale di Azure. Questo potrebbe aiutare a diagnosticare il problema e stabilire se è causato da un errore di avvio simile.

## <a name="recommended-steps"></a>Procedure consigliate

Attenersi alla procedura di mitigazione riportata di seguito, a seconda dell'errore ricevuto:

### <a name="error---unknown-filesystem"></a>Errore-file System sconosciuto

* Se viene visualizzato il **file System sconosciuto**Error, questo errore può essere causato da un file System danneggiamento della partizione di avvio o da una configurazione kernel non corretta.

   * Per file system problemi, attenersi alla procedura descritta nell'articolo [ripristino di Linux: non è possibile connettersi a una VM Linux da SSH a causa di errori file System (fsck, inode)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * Per i problemi del kernel, seguire la procedura descritta nell'articolo [ripristino di Linux: correzione manuale di problemi non di avvio correlati ai problemi del kernel](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)o [ripristino di Linux: correzione di problemi di avvio non correlati a problemi del kernel con chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Errore-il file non è stato trovato

* Se viene ricevuto l' **errore 15: file non trovato o disco RAM iniziale** o **file initrd/initramfs non trovato**, attenersi alla procedura riportata di seguito.

    * Per il file `/boot/grub2/grub.cfg` mancante o `initrd/initramfs` procedere con il processo seguente:

    1. Verificare `/etc/default/grub` che esista e abbia le impostazioni corrette/desiderate. Se non si sa quali sono le impostazioni predefinite, è possibile verificare con una VM funzionante.

    2. Eseguire quindi il comando seguente per rigenerare la configurazione:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Se il file mancante è `/boot/grub/menu.lst`, questo errore è per le versioni precedenti del sistema operativo (**RHEL 6. x**, **CentOS 6. x** e **Ubuntu 14,04**), quindi i comandi potrebbero essere diversi. Sarà necessario avviare un vecchio server e verificare che vengano forniti i comandi corretti.

### <a name="error---no-such-partition"></a>Errore-nessuna partizione di questo tipo

* Se si verifica un errore di **questo tipo**, fare riferimento allo [scenario case: "nessuna partizione di questo tipo" durante il tentativo di avviare la macchina virtuale dopo il tentativo di estendere l'unità del sistema operativo](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Errore-il file grub. cfg non è stato trovato

* Se si sta ricevendo l'errore **/boot/GRUB2/grub.cfg file non trovato**, attenersi alla procedura riportata di seguito.

    * Per il file `/boot/grub2/grub.cfg` mancante o `initrd/initramfs` procedere con il processo seguente:

    1. Verificare `/etc/default/grub` che esista e abbia le impostazioni corrette/desiderate. Se non si sa quali sono le impostazioni predefinite, è possibile verificare con una VM funzionante.

    2. Eseguire quindi il comando seguente per rigenerare la configurazione: `grub2-mkconfig -o /boot/grub2/grub.cfg`.

   * Se il file mancante è `/boot/grub/menu.lst`, questo errore è per le versioni precedenti del sistema operativo (**RHEL 6. x**, **CentOS 6. x** e **Ubuntu 14,04**), in modo che i comandi possano rinviare. Avviare un vecchio server e testarlo per assicurarsi che vengano forniti i comandi corretti.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dell'agente di macchine virtuali di Azure](../extensions/agent-windows.md)
* [Estensioni e funzionalità della macchina virtuale per Windows](../extensions/features-windows.md)

