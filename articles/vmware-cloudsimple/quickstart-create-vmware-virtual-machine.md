---
title: Guida introduttiva alla soluzione VMware di Azure di CloudSimple-utilizzo di macchine virtuali VMware in Azure
description: Informazioni su come configurare e usare macchine virtuali VMware da portale di Azure tramite la soluzione VMware di Azure di CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 504550358bb56602093e58c90506c9140afccadb
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574499"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Guida introduttiva: usare macchine virtuali VMware in Azure

Per creare una macchina virtuale nella portale di Azure, usare i modelli di macchina virtuale abilitati dall'amministratore di CloudSimple per la sottoscrizione. I modelli di macchina virtuale sono disponibili nell'infrastruttura VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Per la creazione di macchine virtuali CloudSimple in Azure è necessario un modello di macchina virtuale

Creare una macchina virtuale nel cloud privato dall'interfaccia utente di vCenter. Per creare un modello, seguire le istruzioni in [clonare una macchina virtuale in un modello nel client Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Archiviare il modello di macchina virtuale nel cloud privato vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure

1. Selezionare **Tutti i servizi**.

2. Cercare **Macchine virtuali CloudSimple**.

3. Fare clic su **Aggiungi**.

    ![Creare una macchina virtuale CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Immettere le informazioni di base e fare clic su **Next: size**.

    ![Creare una macchina virtuale CloudSimple-nozioni di base](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | DESCRIZIONE |
    | ------------ | ------------- |
    | Sottoscrizione | Sottoscrizione di Azure associata al cloud privato.  |
    | Gruppo di risorse | Gruppo di risorse a cui verrà assegnata la macchina virtuale. È possibile selezionare un gruppo esistente o crearne uno nuovo. |
    | Name | Nome per identificare la macchina virtuale.  |
    | Location | Area di Azure in cui è ospitata questa macchina virtuale.  |
    | Cloud privato | CloudSimple cloud privato in cui si vuole creare la macchina virtuale. |
    | Pool di risorse | Pool di risorse mappato per la macchina virtuale. Selezionare un pool di risorse tra quelli disponibili. |
    | Modello vSphere | modello vSphere per la macchina virtuale.  |
    | Nome utente | Nome utente dell'amministratore della VM (per i modelli di Windows).|
    | Password |  Password per l'amministratore della VM (per i modelli di Windows). |
    | Conferma password | Confermare la password. |

5. Selezionare il numero di core e la capacità di memoria per la macchina virtuale e fare clic su **Avanti: configurazioni**. Selezionare la casella di controllo se si desidera esporre la virtualizzazione completa della CPU al sistema operativo guest. Le applicazioni che richiedono la virtualizzazione hardware possono essere eseguite su macchine virtuali senza conversione binaria o paravirtualizzazione. Per altre informazioni, vedere l'articolo <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Esporre la virtualizzazione assistita mediante hardware VMware</a> sul sito Web VMware.

    ![Crea macchina virtuale CloudSimple-dimensioni](media/create-cloudsimple-virtual-machine-size.png)

6. Configurare le interfacce di rete e i dischi come descritto nelle tabelle seguenti e fare clic su **Verifica + crea**.

    ![Creare una macchina virtuale CloudSimple-configurazioni](media/create-cloudsimple-virtual-machine-configurations.png)

    Per le interfacce di rete, fare clic su **Aggiungi interfaccia di rete** e configurare le impostazioni seguenti.

    | Control | Descrizione |
    | ------------ | ------------- |
    | Name | Immettere un nome per identificare l'interfaccia.  |
    | Rete | Selezionare dall'elenco di gruppi di porte distribuite configurate nel cloud privato vSphere.  |
    | Adapter | Selezionare un adattatore vSphere dall'elenco dei tipi disponibili configurato per la macchina virtuale. Per ulteriori informazioni, vedere l'articolo della Knowledge Base VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">scelta di una scheda di rete per la macchina virtuale</a>. |
    | Accensione all'avvio | Scegliere se abilitare l'hardware NIC all'avvio della macchina virtuale. Il valore predefinito è **Abilita**. |

    Per i dischi, fare clic su **Aggiungi disco** e configurare le impostazioni seguenti.

    | Elemento | Descrizione |
    | ------------ | ------------- |
    | Name | Immettere un nome per identificare il disco.  |
    | Dimensione | Selezionare una delle dimensioni disponibili.  |
    | Controller SCSI | Selezionare un controller SCSI per il disco.  |
    | Modalità | Determina la modalità di partecipazione del disco negli snapshot. Scegli una delle seguenti opzioni: <br> -Permanente indipendente: Tutti i dati scritti sul disco vengono scritti in modo permanente.<br> -Indipendente non persistente: le modifiche scritte su disco vengono eliminate quando si spegne o si ripristina la macchina virtuale.  La modalità indipendente non permanente consente di riavviare la macchina virtuale sempre nello stesso stato. Per altre informazioni, vedere la <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentazione di VMware</a>.

7. Al termine della convalida, rivedere le impostazioni e fare clic su **Crea**. Per apportare modifiche, fare clic sulle schede nella parte superiore o fare clic su.

    ![Creare una macchina virtuale CloudSimple-Revisione](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare l'elenco di macchine virtuali CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Gestire la macchina virtuale CloudSimple da Azure](azure-manage-vm.md)
