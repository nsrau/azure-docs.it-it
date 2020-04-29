---
title: Montare volumi Azure NetApp Files per le macchine virtuali
description: Informazioni su come montare o smontare un volume per macchine virtuali Windows o macchine virtuali Linux in Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084941"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montare o smontare un volume per macchine virtuali Windows o Linux 

È possibile montare o smontare un volume per macchine virtuali Windows o Linux a seconda delle esigenze.  Le istruzioni di montaggio per le macchine virtuali Linux sono disponibili in Azure NetApp Files.  

1. Fare clic sul pannello **volumi** e quindi selezionare il volume per il quale si desidera montare. 
2. Fare clic su **istruzioni di montaggio** dal volume selezionato, quindi seguire le istruzioni per montare il volume. 

    ![Istruzioni di montaggio NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Istruzioni di montaggio SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Se si usa NFSv 4.1, usare il comando seguente per montare i file system:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Se si vuole che un volume NFS venga montato automaticamente quando una macchina virtuale di Azure viene avviata o riavviata, aggiungere una `/etc/fstab` voce al file nell'host. 

    Ad esempio: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`è l'indirizzo IP del volume Azure NetApp Files trovato nel pannello delle proprietà del volume.
    * `$FILEPATH`percorso di esportazione del volume Azure NetApp Files.
    * `$MOUNTPOINT`è la directory creata nell'host Linux usato per montare l'esportazione NFS.

4. Se si desidera montare il volume in Windows utilizzando NFS:

    a. Montare prima il volume in una VM UNIX o Linux.  
    b. Eseguire un `chmod 777` comando `chmod 775` o sul volume.  
    c. Montare il volume tramite il client NFS in Windows.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare il dominio predefinito di NFS versione 4.1 per Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Domande frequenti su NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Panoramica di Network File System](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
