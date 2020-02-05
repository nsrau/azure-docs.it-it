---
title: Azure VMware Solutions (AVS)-migrare le macchine virtuali del carico di lavoro nel cloud privato AVS
description: Viene descritto come eseguire la migrazione di macchine virtuali da vCenter locale a AVS private cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: af02bd947c73b670306704a10a09ca981b34c9a9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019996"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-avs-private-cloud-vcenter-environment"></a>Eseguire la migrazione di macchine virtuali del carico di lavoro da vCenter locale a AVS private cloud vCenter Environment

Per eseguire la migrazione di VM da un Data Center locale al cloud privato AVS, sono disponibili diverse opzioni. Il cloud privato AVS fornisce l'accesso nativo a VMware vCenter e gli strumenti supportati da VMware possono essere usati per la migrazione del carico di lavoro. Questo articolo descrive alcune opzioni di migrazione di vCenter.

## <a name="prerequisites"></a>Prerequisiti

La migrazione di VM e dati dal Data Center locale richiede la connettività di rete dal Data Center all'ambiente di cloud privato AVS. Per stabilire la connettività di rete, usare uno dei metodi seguenti:

* [Connessione VPN da sito a sito](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tra l'ambiente locale e il cloud privato AVS.
* ExpressRoute Copertura globale la connessione tra il circuito ExpressRoute locale e un circuito AVS ExpressRoute.

Il percorso di rete dall'ambiente vCenter locale al cloud privato AVS deve essere disponibile per la migrazione di macchine virtuali con vMotion. La rete vMotion in vCenter locale deve avere capacità di routing. Verificare che il firewall consenta tutto il traffico vMotion tra i locali vCenter e AVS private cloud vCenter. Nel cloud privato AVS, il routing nella rete vMotion è configurato per impostazione predefinita.

## <a name="migrate-isos-and-templates"></a>Eseguire la migrazione di modelli e ISOs

Per creare nuove macchine virtuali nel cloud privato AVS, usare le immagini ISO e i modelli di VM. Per caricare le immagini ISO e i modelli in AVS private cloud vCenter e renderli disponibili, usare il metodo seguente.

1. Caricare l'immagine ISO in AVS private cloud vCenter dall'interfaccia utente di vCenter.
2. [Pubblicare una raccolta contenuto](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) in AVS private cloud vCenter:

    1. Pubblicare la raccolta contenuto locale.
    2. Creare una nuova raccolta contenuto in AVS private cloud vCenter.
    3. Sottoscrivere la raccolta contenuto locale pubblicata.
    4. Sincronizzare la raccolta contenuto per l'accesso ai contenuti sottoscritti.

## <a name="migrate-vms-using-powercli"></a>Eseguire la migrazione di macchine virtuali con PowerCLI

Per eseguire la migrazione di macchine virtuali da vCenter locale ad AVS private cloud vCenter, usare VMware PowerCLI o l'utilità di migrazione del carico di lavoro Cross vCenter disponibile in VMware Labs. Lo script di esempio seguente mostra i comandi di migrazione PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Per usare i nomi del server vCenter di destinazione e degli host ESXi, configurare l'invio DNS da locale al cloud privato AVS.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Eseguire la migrazione di VM con la rete VPN di livello 2

Questa opzione consente la migrazione in tempo reale dei carichi di lavoro dall'ambiente VMware locale al cloud privato AVS in Azure. Con questa rete di livello 2 estesa, la subnet dall'ambiente locale sarà disponibile nel cloud privato AVS. Dopo la migrazione, la nuova assegnazione di indirizzi IP non è necessaria per le macchine virtuali.

[Migrare i carichi di lavoro usando reti estese di livello 2](migration-layer-2-vpn.md) descrive come usare una VPN di livello 2 per estendere una rete di livello 2 dall'ambiente locale al cloud privato AVS.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Eseguire la migrazione di macchine virtuali con strumenti di backup e ripristino di emergenza

La migrazione delle macchine virtuali al cloud privato AVS può essere eseguita usando gli strumenti di backup e ripristino e gli strumenti di ripristino di emergenza. Usare il cloud privato AVS come destinazione per il ripristino da backup creati usando uno strumento di terze parti. Il cloud privato AVS può essere usato anche come destinazione per il ripristino di emergenza usando VMware SRM o uno strumento di terze parti.

Per ulteriori informazioni sull'utilizzo di questi strumenti, vedere gli argomenti seguenti:

* [Eseguire il backup di macchine virtuali del carico di lavoro nel cloud privato AVS usando Veeam B & R](backup-workloads-veeam.md)
* [Configurare AVS private cloud come sito di ripristino di emergenza per i carichi di lavoro VMware locali](disaster-recovery-zerto.md)
