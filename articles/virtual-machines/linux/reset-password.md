---
title: Come reimpostare la password di Linux locale nelle VM di Azure | Microsoft Docs
description: Viene illustrata la procedura per reimpostare la password di Linux locale in una VM di Azure
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 7/3/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: bd48128a078821b7a4baa02d5d7ceecc6de99608
ms.contentlocale: it-it
ms.lasthandoff: 08/11/2017

---

# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Come reimpostare la password di Linux locale nelle VM di Azure

Questo articolo illustra diversi metodi per reimpostare le password delle macchine virtuali (VM) di Linux locali. Se l'account utente è scaduto o si vuole creare un nuovo account, è possibile usare i metodi seguenti per creare un nuovo account amministratore locale e riottenere l'accesso alla VM.

## <a name="symptoms"></a>Sintomi

Non è possibile accedere alla VM e viene visualizzato un messaggio indicante che la password usata non è corretta. Inoltre non è possibile usare VMAgent per reimpostare la password nel portale di Azure. 

## <a name="manual-password-reset-procedure"></a>Procedura di reimpostazione manuale della password

1.  Eliminare la VM e mantenere i dischi collegati.

2.  Collegare l'unità del sistema operativo come disco dati a un'altra VM temporale nella stessa posizione.

3.  Usare il comando SSH seguente nella VM temporale per diventare utente con privilegi avanzati.


    ~~~~
    sudo su
    ~~~~

4.  Eseguire **fdisk -l** o esaminare i log di sistema per trovare il nuovo disco collegato. Trovare il nome dell'unità da montare, quindi nella VM temporale esaminare il file di log pertinente.

    ~~~~
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ~~~~

    Il seguente è l'output di esempio del comando grep:

    ~~~~
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ~~~~

5.  Creare un punto di montaggio denominato **tempmount**.

    ~~~~
    mkdir /tempmount
    ~~~~

6.  Montare il disco del sistema operativo nel punto di montaggio. È in genere necessario montare sdc1 o sdc2 a seconda della partizione di hosting nella directory /etc del disco del computer danneggiato.

    ~~~~
    mount /dev/sdc1 /tempmount
    ~~~~

7.  Eseguire un backup prima di apportare modifiche:

    ~~~~
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ~~~~

8.  Reimpostare la password dell'utente necessaria:

    ~~~~
    passwd <<USER>> 
    ~~~~

9.  Spostare i file modificati nella posizione corretta sul disco del computer danneggiato.

    ~~~~
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    
10. Go back to the root and unmount the disk.

    ~~~~
    cd / umount /tempmount
    ~~~~

11. Detach the disk from the management portal.

12. Recreate the VM.

## Next steps

* [Troubleshoot Azure VM by attaching OS disk to another Azure VM](http://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: How to delete and re-deploy a VM from VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
