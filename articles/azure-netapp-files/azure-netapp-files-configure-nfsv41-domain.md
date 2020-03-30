---
title: Configurare il dominio predefinito di NFSv4.1 per i file netApp di Azure Documenti Microsoft
description: Viene descritto come configurare il client NFS per l'utilizzo di NFSv4.1 con i file NetApp di Azure.Describes how to configure the NFS client for using NFSv4.1 with Azure NetApp Files.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906286"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Configurare il dominio predefinito di NFS versione 4.1 per Azure NetApp Files

NFSv4 introduce il concetto di dominio di autenticazione. File NetApp di Azure supporta attualmente il mapping degli utenti solo radice dal servizio al client NFS. Per usare la funzionalità NFSv4.1 con i file NetApp di Azure, è necessario aggiornare il client NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Comportamento predefinito del mapping utente/gruppo

Il mapping principale `nobody` viene impostato per impostazione predefinita sull'utente perché il dominio NFSv4 è impostato su `localdomain`. Quando si monta un volume NFSv4.1 file NetApp di Azure come radice, verranno visualizzate le autorizzazioni per i file come segue:When you mount an Azure NetApp Files NFSv4.1 volume as root, you will see file permissions as follows:  

![Comportamento predefinito del mapping utente/gruppo per NFSv4.1Default behavior of user/group mapping for NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Come illustrato nell'esempio precedente, `root`l'utente dovrebbe `nobody` `file1` essere , ma viene eseguito il mapping per impostazione predefinita.  In questo articolo viene `file1` illustrato `root`come impostare l'utente su .  

## <a name="steps"></a>Passaggi 

1. Modificare `/etc/idmapd.conf` il file sul client NFS.   
    Rimuovere il `#Domain` commento dalla riga, `#` ovvero rimuoverla dalla `localdomain` riga, e modificare il valore in `defaultv4iddomain.com`. 

    Configurazione iniziale: 
    
    ![Configurazione iniziale per NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Configurazione aggiornata:
    
    ![Configurazione aggiornata per NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Smontare tutti i volumi NFS attualmente montati.
3. Aggiornare `/etc/idmapd.conf` il file.
4. Riavviare `rpcbind` il servizio`service rpcbind restart`nell'host ( ) o semplicemente riavviare l'host.
5. Montare i volumi NFS in base alle esigenze.   

    Vedere [Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

L'esempio seguente mostra la modifica utente/gruppo risultante:The following example shows the resulting user/group change: 

![Configurazione risultante per NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Come illustrato nell'esempio, l'utente/gruppo è ora passato da `nobody` a `root`.

## <a name="behavior-of-other-non-root-users-and-groups"></a>Comportamento di altri utenti e gruppi (non root)

File NetApp di Azure supporta gli utenti locali (utenti creati localmente in un host) che dispongono di autorizzazioni associate a file o cartelle nei volumi NFSv4.1.Azure NetApp Files supports local users (users created locally on a host) who have permissions associated with files or folders in NFSv4.1 volumes. Tuttavia, il servizio non supporta attualmente il mapping degli utenti/gruppi tra più nodi. Di conseguenza, gli utenti creati in un host non eseguono il mapping per impostazione predefinita agli utenti creati in un altro host. 

Nell'esempio seguente `Host1` sono disponibili tre`testuser01`account `testuser02` `testuser03`utente di prova esistenti ( , , ): 

![Configurazione risultante per NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

In `Host2`, si noti che gli account utente di prova non sono stati creati, ma lo stesso volume è montato su entrambi gli host:

![Configurazione risultante per NFSv4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Passaggio successivo 

[Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

