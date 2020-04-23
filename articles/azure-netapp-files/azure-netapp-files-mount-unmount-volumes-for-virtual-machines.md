---
title: Montare i volumi dei file netApp di Azure per le macchine virtualiMount Azure NetApp Files volumes for virtual machines
description: Informazioni su come montare o smontare un volume per macchine virtuali Windows o macchine virtuali Linux in Azure.Learn how to mount or unmount a volume for Windows virtual machines or Linux virtual machines in Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084941"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montare o smontare un volume per macchine virtuali Windows o Linux 

È possibile montare o smontare un volume per macchine virtuali Windows o Linux a seconda delle esigenze.  Le istruzioni di montaggio per le macchine virtuali Linux sono disponibili in Azure NetApp Files.  

1. Fare clic sul pannello **Volumi** e quindi selezionare il volume per il quale si desidera montare. 
2. Fare clic su **Monta istruzioni** dal volume selezionato e quindi seguire le istruzioni per montare il volume. 

    ![Istruzioni di montaggio NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Istruzioni di montaggio SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Se si utilizza NFSv4.1, utilizzare il seguente comando per montare il file system:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Se si vuole che un volume NFS venga montato automaticamente quando una `/etc/fstab` macchina virtuale di Azure viene avviata o riavviata, aggiungere una voce al file nell'host. 

    Ad esempio: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`è l'indirizzo IP del volume File NetApp di Azure presente nel pannello delle proprietà del volume.
    * `$FILEPATH`è il percorso di esportazione del volume File NetApp di Azure.
    * `$MOUNTPOINT`è la directory creata sull'host Linux utilizzato per montare l'esportazione NFS.

4. Se si desidera montare il volume in Windows utilizzando NFS:

    a. Montare prima il volume su una macchina virtuale Unix o Linux.Mount the volume onto a Unix or Linux VM first.  
    b. Eseguire `chmod 777` un `chmod 775` comando o sul volume.  
    c. Montare il volume tramite il client NFS in Windows.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare il dominio predefinito di NFS versione 4.1 per Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Domande frequenti su NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Panoramica di Network File System](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
