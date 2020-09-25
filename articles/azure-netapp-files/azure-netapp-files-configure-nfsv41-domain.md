---
title: Configurare il dominio predefinito NFSv 4.1 per Azure NetApp Files | Microsoft Docs
description: Viene descritto come configurare il client NFS per l'utilizzo di NFSv 4.1 con Azure NetApp Files.
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: e749f27875612136c50938712fded6a371f8c7ab
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325624"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Configurare il dominio predefinito di NFS versione 4.1 per Azure NetApp Files

In NFSv4 è stato introdotto il concetto di dominio di autenticazione. Azure NetApp Files supporta attualmente il mapping utente solo radice dal servizio al client NFS. Per usare la funzionalità NFSv 4.1 con Azure NetApp Files, è necessario aggiornare il client NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Comportamento predefinito del mapping di utenti/gruppi

Il mapping radice per impostazione predefinita `nobody` è l'utente perché il dominio NFSv4 è impostato su `localdomain` . Quando si monta un volume Azure NetApp Files NFSv 4.1 come radice, vengono visualizzate le autorizzazioni per i file come indicato di seguito:  

![Comportamento predefinito del mapping di utenti/gruppi per NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Come illustrato nell'esempio precedente, l'utente per `file1` deve essere `root` , ma è associato a per `nobody` impostazione predefinita.  Questo articolo illustra come impostare l' `file1` utente su `root` .  

## <a name="steps"></a>Passaggi 

1. Modificare il `/etc/idmapd.conf` file nel client NFS.   
    Rimuovere il commento dalla riga `#Domain` , ovvero rimuovere `#` dalla riga, e modificare il valore `localdomain` in `defaultv4iddomain.com` . 

    Configurazione iniziale: 
    
    ![Configurazione iniziale per NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Configurazione aggiornata:
    
    ![Configurazione aggiornata per NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Smontare tutti i volumi NFS attualmente montati.
3. Aggiornare il `/etc/idmapd.conf` file.
4. Riavviare il `rpcbind` servizio nell'host ( `service rpcbind restart` ) o semplicemente riavviare l'host.
5. Montare i volumi NFS come richiesto.   

    Vedere [montare o smontare un volume per le macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

Nell'esempio seguente viene illustrata la modifica di utenti/gruppi risultante: 

![Screenshot che mostra un esempio della modifica risultante di utenti/gruppi.](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Come illustrato nell'esempio, l'utente/gruppo è ora modificato da `nobody` a `root` .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Comportamento degli altri utenti e gruppi (non radice)

Azure NetApp Files supporta gli utenti locali (creati localmente in un host) che dispongono delle autorizzazioni associate a file o cartelle nei volumi NFSv 4.1. Tuttavia, il servizio non supporta attualmente il mapping di utenti/gruppi tra più nodi. Pertanto, gli utenti creati in un host non vengono mappati per impostazione predefinita agli utenti creati in un altro host. 

Nell'esempio seguente, `Host1` dispone di tre account utente di test esistenti ( `testuser01` , `testuser02` , `testuser03` ): 

![Screenshot che mostra che host1 dispone di tre account utente di test esistenti.](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

`Host2`Si noti che gli account utente di test non sono stati creati, ma lo stesso volume viene montato in entrambi gli host:

![Configurazione risultante per NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Passaggio successivo 

[Montare o smontare un volume per macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

