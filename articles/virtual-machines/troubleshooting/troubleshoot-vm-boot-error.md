---
title: Stivali Linux VM per Grub Rescue
description: Impossibile avviare la macchina virtuale perché la macchina virtuale è entrata in una console di salvataggio
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
# <a name="linux-vm-boots-to-grub-rescue"></a>Stivali Linux VM per Grub Rescue

È stato identificato che la macchina virtuale (VM) è entrata in una console di salvataggio. Il problema si verifica quando nella macchina virtuale Linux sono state applicate di recente, ad esempio un aggiornamento del kernel, e non viene più avviato correttamente a causa di errori del kernel durante il processo di avvio. Durante il processo di avvio, quando il caricatore di avvio tenta di individuare il kernel Linux e passare il controllo di avvio a esso, la macchina virtuale entra in una console di salvataggio quando il trasferimento non riesce.

Se non è possibile connettersi a una macchina virtuale in futuro, è possibile visualizzare uno screenshot della macchina virtuale usando il pannello di diagnostica di avvio nel portale di Azure.If you can't connect to a VM in the future, you can view a screenshot of your VM using the boot diagnostics blade in the Azure portal. Questo potrebbe aiutare a diagnosticare il problema e stabilire se è causato da un errore di avvio simile.

## <a name="recommended-steps"></a>Procedure consigliate

Attenersi alla procedura di attenuazione riportata di seguito a seconda dell'errore visualizzato:

### <a name="error---unknown-filesystem"></a>Errore - File system sconosciuto

* Se viene visualizzato l'errore **Filesystem sconosciuto**, questo errore può derivare da un danneggiamento del file system nella partizione di avvio o da una configurazione del kernel non corretta.

   * Per problemi relativi al file system, seguire la procedura descritta nell'articolo [Ripristino Linux: Impossibile eseguire SSH nella macchina virtuale Linux a causa di errori del file system (fsck, inodes)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * Per problemi relativi al kernel, attenersi alla procedura descritta nell'articolo [Ripristino Linux: Correzione manuale dei problemi](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)non di avvio relativi ai problemi del kernel o del ripristino di Linux: risoluzione dei problemi non di [avvio correlati ai problemi del kernel mediante chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Errore - File non trovato

* Se viene visualizzato l'errore **15: File non trovato o disco RAM iniziale** o **file initrd/initramfs non trovato,** attenersi alla seguente procedura.

    * Per il `/boot/grub2/grub.cfg` file `initrd/initramfs` mancante o procedere con il seguente processo:

    1. Assicurarsi `/etc/default/grub` che esistano e che siano corrette/desiderate le impostazioni. Se non si conoscono le impostazioni predefinite, è possibile eseguire il controllo con una macchina virtuale funzionante.

    2. Successivamente, eseguire il comando seguente per rigenerare la configurazione:Next, run the following command to regenerate its configuration:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Se il file `/boot/grub/menu.lst`mancante è , questo errore riguarda le versioni precedenti del sistema operativo (**RHEL 6.x**, **Centos 6.x** e **Ubuntu 14.04**) in modo che i comandi possano differire. Si dovrà girare su un vecchio server e testare per assicurarsi che vengano forniti i comandi corretti.

### <a name="error---no-such-partition"></a>Errore - Nessuna partizione di questo tipo

* Se viene visualizzato l'errore **Nessuna partizione**di questo tipo, fare riferimento all'errore [Scenario caso : "Nessuna partizione di questo tipo" durante il tentativo di avviare la macchina virtuale dopo il tentativo di estendere l'unità del sistema operativo](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Errore - file grub.cfg non trovato

* Se viene visualizzato il file di errore **/boot/grub2/grub.cfg non trovato,** attenersi alla seguente procedura.

    * Per il `/boot/grub2/grub.cfg` file `initrd/initramfs` mancante o procedere con il seguente processo:

    1. Assicurarsi `/etc/default/grub` che esistano e che siano corrette/desiderate le impostazioni. Se non si conoscono le impostazioni predefinite, è possibile eseguire il controllo con una macchina virtuale funzionante.

    2. Eseguire quindi il comando seguente per `grub2-mkconfig -o /boot/grub2/grub.cfg`rigenerarne la configurazione: .

   * Se il file `/boot/grub/menu.lst`mancante è , questo errore riguarda le versioni precedenti del sistema operativo (**RHEL 6.x**, **Centos 6.x** e **Ubuntu 14.04**) in modo che i comandi possano rinviare. Eseguire il suato di un server precedente e testarlo per assicurarsi che vengano forniti i comandi corretti.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dell'agente di macchine virtuali di Azure](../extensions/agent-windows.md)
* [Estensioni e funzionalità della macchina virtuale per Windows](../extensions/features-windows.md)

