---
title: Azure VMware Solution by CloudSimple - Migrate workload VMs to Private Cloud
description: Descrive come eseguire la migrazione di macchine virtuali da vCenter locale a CloudSimple Private Cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019996"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Eseguire la migrazione delle macchine virtuali del carico di lavoro da vCenter locale all'ambiente vCenter del cloud privatoMigrate workload VMs from on-premises vCenter to Private Cloud vCenter environment

Per eseguire la migrazione delle macchine virtuali da un data center locale al cloud privato CloudSimple, sono disponibili diverse opzioni.  Il cloud privato fornisce l'accesso nativo a VMware vCenter e gli strumenti supportati da VMware possono essere utilizzati per la migrazione del carico di lavoro. In questo articolo vengono descritte alcune delle opzioni di migrazione di vCenter.

## <a name="prerequisites"></a>Prerequisiti

La migrazione di macchine virtuali e dati dal data center locale richiede la connettività di rete dal data center all'ambiente cloud privato.  Utilizzare uno dei seguenti metodi per stabilire la connettività di rete:

* [Connessione VPN](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) da sito a sito tra l'ambiente locale e il cloud privato.
* Connessione di Reach globale ExpressRoute tra il circuito ExpressRoute locale e un circuito CloudSimple ExpressRoute.ExpressRoute Global Reach connection between your on-premises ExpressRoute circuit and a CloudSimple ExpressRoute circuit.

Il percorso di rete dall'ambiente vCenter locale al cloud privato deve essere disponibile per la migrazione delle macchine virtuali tramite vMotion.The network path from your on-premises vCenter environment to your Private Cloud must be available for migration of VMs using vMotion.  La rete vMotion nel vCenter locale deve avere capacità di routing.  Verificare che il firewall consenta tutto il traffico vMotion tra vCenter locale e il cloud privato vCenter. Nel cloud privato, il routing sulla rete vMotion è configurato per impostazione predefinita.

## <a name="migrate-isos-and-templates"></a>Eseguire la migrazione di ISO e modelli

Per creare nuove macchine virtuali nel cloud privato, usare ISO e modelli di macchine virtuali.  Per caricare le ISO e i modelli nel tuo vCenter Private Cloud e renderli disponibili, usa il metodo seguente.

1. Caricare l'ISO nel cloud privato vCenter dall'interfaccia utente di vCenter.
2. [Pubblicare una raccolta contenuto](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) nel centro commerciale Private Cloud:

    1. Pubblicare la raccolta contenuto locale.
    2. Creare una nuova raccolta contenuto nel cloud privato vCenter.
    3. Sottoscrivere la raccolta contenuto locale pubblicata.
    4. Sincronizzare la raccolta contenuto per l'accesso ai contenuti sottoscritti.

## <a name="migrate-vms-using-powercli"></a>Eseguire la migrazione di macchine virtuali usando PowerCLIMigrate VMs using PowerCLI

Per eseguire la migrazione delle macchine virtuali dal vCenter locale al centro commerciale Private Cloud vCenter, utilizzare VMware PowerCLI o l'utilità di migrazione del carico di lavoro Cross vCenter disponibile da VMware Labs.  Lo script di esempio seguente mostra i comandi di migrazione PowerCLI.The following sample script shows the PowerCLI migration commands.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Per utilizzare i nomi del server vCenter di destinazione e degli host ESXi, configurare l'inoltro DNS dall'ambiente locale al cloud privato.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Eseguire la migrazione di macchine virtuali tramite NSX Layer 2 VPNMigrate VMs using NSX Layer 2 VPN

Questa opzione consente la migrazione in tempo reale dei carichi di lavoro dall'ambiente VMware locale al cloud privato in Azure.This option enables live migration of workloads from your on-premises VMware environment to the Private Cloud in Azure.  Con questa rete estesa di livello 2, la subnet locale sarà disponibile nel cloud privato.  Dopo la migrazione, l'assegnazione di una nuova indirizzo IP non è necessaria per le macchine virtuali.

[Migrare i carichi di lavoro utilizzando reti estese](migration-layer-2-vpn.md) di livello 2 descrive come usare una VPN di livello 2 per estendere una rete di livello 2 dall'ambiente locale al cloud privato.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Eseguire la migrazione delle macchine virtuali usando gli strumenti di backup e ripristino di emergenzaMigrate VMs using backup and disaster recovery tools

La migrazione delle macchine virtuali nel cloud privato può essere eseguita utilizzando gli strumenti di backup/ripristino e gli strumenti di ripristino di emergenza.  Utilizzare il cloud privato come destinazione per il ripristino da backup creati utilizzando uno strumento di terze parti.  Il cloud privato può essere utilizzato anche come destinazione per il ripristino di emergenza utilizzando VMware SRM o uno strumento di terze parti.

Per altre informazioni sull'uso di questi strumenti, vedere gli argomenti seguenti:For more information using these tools, see the following topics:

* [Eseguire il backup delle macchine virtuali del carico di lavoro su CloudSimple Private Cloud usando Veeam B&R](backup-workloads-veeam.md)
* [Configurare CloudSimple Private Cloud come sito di ripristino di emergenza per i carichi di lavoro VMware localiSet up CloudSimple Private Cloud as a disaster recovery site for on-premises VMware workloads](disaster-recovery-zerto.md)
