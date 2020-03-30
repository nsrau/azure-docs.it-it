---
title: 'Guida introduttiva: Usare le macchine virtuali VMware in AzureQuickstart: Consume VMware VMs on Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Informazioni su come configurare e usare le macchine virtuali VMware dal portale di Azure usando Azure VMware Solution di CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019554"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Guida introduttiva - Usare le macchine virtuali VMware in AzureQuickstart - Consume VMware VMs on Azure

Per creare una macchina virtuale nel portale di Azure, usare i modelli di macchina virtuale abilitati dall'amministratore CloudSimple per la sottoscrizione. I modelli di macchina virtuale si trovano nell'infrastruttura VMware.The VM templates are found in the VMware infrastructure.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>La creazione di vm CloudSimple in Azure richiede un modello di macchina virtualeCloudSimple VM creation on Azure requires a VM template

Creare una macchina virtuale nel cloud privato dall'interfaccia utente di vCenter.Create a virtual machine on your Private Cloud from the vCenter UI. Per creare un modello, seguire le istruzioni in [Clonare una macchina virtuale a un modello nel client Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Archiviare il modello di macchina virtuale nel cloud privato vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure

1. Selezionare **Tutti i servizi**.

2. Cercare **Macchine virtuali CloudSimple**.

3. Fare clic su **Aggiungi**.

    ![Creare una macchina virtuale CloudSimpleCreate CloudSimple virtual machine](media/create-cloudsimple-virtual-machine.png)

4. Immettere le informazioni di base e fare clic su **Avanti:Dimensioni**.

    ![Creare una macchina virtuale CloudSimple - Nozioni di baseCreate CloudSimple virtual machine - basics](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrizione |
    | ------------ | ------------- |
    | Subscription | Sottoscrizione di Azure associata al cloud privato.  |
    | Gruppo di risorse | Gruppo di risorse a cui verrà assegnata la macchina virtuale. È possibile selezionare un gruppo esistente o crearne uno nuovo. |
    | Nome | Nome per identificare la macchina virtuale.  |
    | Location | Area di Azure in cui è ospitata questa macchina virtuale.  |
    | Cloud privato | CloudSimple Private Cloud in cui si vuole creare la macchina virtuale. |
    | Pool di risorse | Pool di risorse mappato per la macchina virtuale. Selezionare un pool di risorse tra quelli disponibili. |
    | Modello vSphere | modello vSphere per la macchina virtuale.  |
    | Nome utente | Nome utente dell'amministratore della macchina virtuale (per i modelli di Windows).|
    | Password |  Password per l'amministratore della macchina virtuale (per i modelli di Windows). |
    | Conferma password | Confermare la password. |

5. Selezionare il numero di core e la capacità di memoria per la macchina virtuale e fare clic su **Avanti:Configurazioni**. Selezionare la casella di controllo se si desidera esporre la virtualizzazione completa della CPU al sistema operativo guest. Le applicazioni che richiedono la virtualizzazione hardware possono essere eseguite su macchine virtuali senza conversione binaria o paravirtualizzazione. Per altre informazioni, vedere l'articolo <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Esporre la virtualizzazione assistita mediante hardware VMware</a> sul sito Web VMware.

    ![Crea macchina virtuale CloudSimple - dimensioni](media/create-cloudsimple-virtual-machine-size.png)

6. Configurare le interfacce di rete e i dischi come descritto nelle tabelle seguenti e fare clic su **Revisione e creazione**.

    ![Creare una macchina virtuale CloudSimple - configurazioniCreate CloudSimple virtual machine - configurations](media/create-cloudsimple-virtual-machine-configurations.png)

    Per le interfacce di rete, fare clic su **Aggiungi interfaccia** di rete e configurare le impostazioni seguenti.

    | Controllo | Descrizione |
    | ------------ | ------------- |
    | Nome | Immettere un nome per identificare l'interfaccia.  |
    | Rete | Selezionare dall'elenco di gruppo di porte distribuite configurato in VSphere di Private Cloud.  |
    | Adattatore | Selezionare un adattatore vSphere dall'elenco dei tipi disponibili configurati per la macchina virtuale. Per ulteriori informazioni, vedere l'articolo della Knowledge Base VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Sulla scelta di una scheda di rete per la macchina virtuale.</a> |
    | Power on at Boot (Accendi all'avvio) | Scegliere se abilitare l'hardware NIC all'avvio della macchina virtuale. Il valore predefinito è **Abilita**. |

    Per i dischi, fare clic su **Aggiungi disco** e configurare le impostazioni seguenti.

    | Elemento | Descrizione |
    | ------------ | ------------- |
    | Nome | Immettere un nome per identificare il disco.  |
    | Dimensione | Selezionare una delle dimensioni disponibili.  |
    | Controller SCSI | Selezionare un controller SCSI per il disco.  |
    | Mode | Determina la modalità di partecipazione del disco alle istantanee. Scegliere una delle opzioni seguenti: <br> - Permanente indipendente: tutti i dati scritti sul disco vengono scritti in modo permanente.- Independent persistent: All data written to the disk is written permanently.<br> - Indipendente non persistente: le modifiche scritte sul disco vengono eliminate quando si spegne o si reimposta la macchina virtuale.- Independent non-persistent: Changes written to the disk are discarded when you off or reset the virtual machine.  La modalità indipendente non permanente consente di riavviare la macchina virtuale sempre nello stesso stato. Per altre informazioni, vedere la <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentazione di VMware</a>.

7. Al termine della convalida, rivedere le impostazioni e fare clic su **Crea**. Per apportare modifiche, fare clic sulle schede nella parte superiore o fare clic.

    ![Creare una macchina virtuale CloudSimple - revisioneCreate CloudSimple virtual machine - review](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare l'elenco di macchine virtuali CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Gestire la macchina virtuale CloudSimple da AzureManage CloudSimple virtual machine from Azure](azure-manage-vm.md)
