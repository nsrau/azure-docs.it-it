---
title: Soluzione VMware di Azure di CloudSimple-migrare le macchine virtuali del carico di lavoro nel cloud privato
description: Viene descritto come eseguire la migrazione di macchine virtuali da vCenter locale a CloudSimple cloud privato vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972669"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Eseguire la migrazione di macchine virtuali del carico di lavoro da vCenter locale a un ambiente vCenter cloud privato

Per eseguire la migrazione di VM da un Data Center locale al cloud privato di CloudSimple, sono disponibili diverse opzioni.  Il cloud privato fornisce l'accesso nativo a VMware vCenter e gli strumenti supportati da VMware possono essere usati per la migrazione del carico di lavoro. Questo articolo descrive alcune opzioni di migrazione di vCenter.

## <a name="prerequisites"></a>Prerequisiti

La migrazione di VM e dati dal Data Center locale richiede la connettività di rete dal Data Center all'ambiente cloud privato.  Per stabilire la connettività di rete, usare uno dei metodi seguenti:

* [Connessione VPN da sito a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tra l'ambiente locale e il cloud privato.
* ExpressRoute Copertura globale la connessione tra il circuito ExpressRoute locale e un circuito ExpressRoute CloudSimple.

Il percorso di rete dall'ambiente vCenter locale al cloud privato deve essere disponibile per la migrazione di macchine virtuali con vMotion.  La rete vMotion in vCenter locale deve avere capacità di routing.  Verificare che il firewall consenta tutto il traffico vMotion tra l'ambiente vCenter locale e il cloud privato vCenter. Nel cloud privato il routing nella rete vMotion è configurato per impostazione predefinita.

## <a name="migrate-isos-and-templates"></a>Eseguire la migrazione di modelli e ISOs

Per creare nuove macchine virtuali nel cloud privato, usare le immagini ISO e i modelli di VM.  Per caricare le immagini ISO e i modelli nel cloud privato vCenter e renderli disponibili, usare il metodo seguente.

1. Caricare l'immagine ISO nel cloud privato vCenter dall'interfaccia utente di vCenter.
2. [Pubblicare una raccolta contenuto](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) nel cloud privato vCenter:

    1. Pubblicare la raccolta contenuto locale.
    2. Creare una nuova raccolta contenuto nel cloud privato vCenter.
    3. Sottoscrivere la raccolta contenuto locale pubblicata.
    4. Sincronizzare la raccolta contenuto per l'accesso ai contenuti sottoscritti.

## <a name="migrate-vms-using-powercli"></a>Eseguire la migrazione di macchine virtuali con PowerCLI

Per eseguire la migrazione di VM da vCenter locale al cloud privato vCenter, usare VMware PowerCLI o l'utilità di migrazione del carico di lavoro Cross vCenter disponibile in VMware Labs.  Lo script di esempio seguente mostra i comandi di migrazione PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Per usare i nomi del server vCenter di destinazione e degli host ESXi, configurare l'invio DNS da locale al cloud privato.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Eseguire la migrazione di VM con la rete VPN di livello 2

Questa opzione consente la migrazione in tempo reale dei carichi di lavoro dall'ambiente VMware locale al cloud privato in Azure.  Con questa rete di livello 2 estesa, la subnet dall'ambiente locale sarà disponibile nel cloud privato.  Dopo la migrazione, la nuova assegnazione di indirizzi IP non è necessaria per le macchine virtuali.

[Migrare i carichi di lavoro usando reti estese di livello 2](migration-layer-2-vpn.md) descrive come usare una VPN di livello 2 per estendere una rete di livello 2 dall'ambiente locale al cloud privato.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Eseguire la migrazione di macchine virtuali con strumenti di backup e ripristino di emergenza

La migrazione delle macchine virtuali al cloud privato può essere eseguita usando gli strumenti di backup e ripristino e gli strumenti di ripristino di emergenza.  Usare il cloud privato come destinazione per il ripristino da backup creati usando uno strumento di terze parti.  Il cloud privato può essere usato anche come destinazione per il ripristino di emergenza usando VMware SRM o uno strumento di terze parti.

Per ulteriori informazioni sull'utilizzo di questi strumenti, vedere gli argomenti seguenti:

* [Eseguire il backup di macchine virtuali del carico di lavoro nel cloud privato CloudSimple usando Veeam B & R](backup-workloads-veeam.md)
* [Configurare il cloud privato CloudSimple come sito di ripristino di emergenza per i carichi di lavoro VMware locali](disaster-recovery-zerto.md)
