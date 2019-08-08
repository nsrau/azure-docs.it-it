---
title: 'Avvio rapido: Soluzione Azure VMware di CloudSimple - Utilizzare macchine virtuali VMware in Azure'
description: In questa guida di avvio rapido si apprenderà come configurare e utilizzare macchine virtuali VMware dal portale di Azure usando la soluzione Azure VMware di CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dd4faf6df54d478654c59ffc18bf8c5873d576b9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816661"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Guida introduttiva: Utilizzare VM di VMware in Azure

Per creare una macchina virtuale nel portale di Azure, è possibile usare i modelli di macchina virtuale disponibili nel vCenter del cloud privato. Un amministratore del vCenter può creare altri modelli nel cloud privato.

## <a name="create-a-vm-template"></a>Creare un modello di macchina virtuale

Creare prima di tutto una macchina virtuale nel cloud privato usando l'interfaccia utente vCenter. Per creare un modello, seguire le istruzioni nell'articolo [Clonare una macchina virtuale in un modello nel client Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html) sul sito Web VMware. Archiviare il modello di macchina virtuale nel vCenter del cloud privato.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure

1. Selezionare **Tutti i servizi**.

2. Cercare **Macchine virtuali CloudSimple**.

3. Selezionare **Aggiungi**.

    ![Selezionare Aggiungi](media/create-cloudsimple-virtual-machine.png)

4. Immettere le informazioni seguenti sulla macchina virtuale e quindi selezionare **Passaggio successivo: Dimensioni**.

    ![Creazione di una macchina virtuale CloudSimple - Generale](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | DESCRIZIONE |
    | ------------ | ------------- |
    | **Sottoscrizione** | Sottoscrizione di Azure associata al cloud privato.  |
    | **Gruppo di risorse** | Gruppo di risorse a cui verrà assegnata la macchina virtuale. È possibile selezionare un gruppo esistente o crearne uno nuovo. |
    | **Nome** | Nome che identifica la macchina virtuale.  |
    | **Posizione** | Area di Azure in cui è ospitata la macchina virtuale.  |
    | **Cloud privato** | Cloud privato di CloudSimple in cui si vuole creare la macchina virtuale. |
    | **ResourcePool** | Pool di risorse mappato per la macchina virtuale. Selezionare un pool di risorse tra quelli disponibili. |
    | **Modello vSphere** | Modello vSphere per la macchina virtuale.  |
    | **Nome utente** | Nome utente dell'amministratore della macchina virtuale (per i modelli di Windows).|
    | **Password** |  Password dell'amministratore della macchina virtuale (per i modelli di Windows). |
    | **Conferma password** | Password specificata nel campo precedente. |

5. Selezionare il numero di core e la capacità di memoria della macchina virtuale. Selezionare **Expose to Guest OS** (Esponi a sistema operativo guest) se si vuole esporre l'intera virtualizzazione della CPU al sistema operativo guest. Le applicazioni che richiedono la virtualizzazione hardware possono essere eseguite su macchine virtuali senza conversione binaria o paravirtualizzazione. Per altre informazioni, vedere l'articolo <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Esporre la virtualizzazione assistita mediante hardware VMware</a> sul sito Web VMware. Al termine, selezionare **Passaggio successivo: Configurazioni**.

    ![Creazione di una macchina virtuale CloudSimple - Dimensioni](media/create-cloudsimple-virtual-machine-size.png)

6. Configurare i dischi e le interfacce di rete come descritto nelle tabelle seguenti e quindi selezionare **Rivedi e crea**.

    ![Creazione di una macchina virtuale CloudSimple - Configurazioni](media/create-cloudsimple-virtual-machine-configurations.png)

    Per le interfacce di rete, selezionare **Add network interface** (Aggiungi interfaccia di rete) e quindi configurare le impostazioni seguenti:
    
    | Impostazione | DESCRIZIONE |
    | ------------ | ------------- |
    | **Nome** | Immettere un nome per identificare l'interfaccia.  |
    | **Rete** | Selezionare una voce dall'elenco dei gruppi di porte distribuite configurati nella piattaforma vSphere del cloud privato.  |
    | **Adapter** | Selezionare un adapter vSphere dall'elenco dei tipi disponibili configurati per la macchina virtuale. Per altre informazioni, vedere l'articolo <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Scelta di una scheda di rete per la macchina virtuale</a> sul sito Web VMware. |
    | **Power on at Boot** (Accendi all'avvio) | Scegliere se abilitare l'hardware NIC all'avvio della macchina virtuale. Il valore predefinito è **Abilita**. |

    Per i dischi, selezionare **Add disk** (Aggiungi disco) e quindi configurare le impostazioni seguenti:

    | Impostazione | DESCRIZIONE |
    | ------------ | ------------- |
    | **Nome** | Immettere un nome per identificare il disco.  |
    | **Dimensione** | Selezionare una delle dimensioni disponibili.  |
    | **Controller SCSI** | Selezionare un controller SCSI per il disco.  |
    | **Modalità** | La modalità specifica il modo in cui il disco fa parte degli snapshot. Scegliere una delle opzioni seguenti: <br> **Independent persistent** (Indipendente permanente): tutte le modifiche vengono scritte sul disco in modo permanente.<br> **Independent non-persistent** (Indipendente non permanente): le modifiche scritte su disco vengono eliminate quando si spegne o si ripristina la macchina virtuale. La modalità indipendente non permanente consente di riavviare la macchina virtuale sempre nello stesso stato. Per altre informazioni, vedere la <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentazione di VMware</a>.

7. Al termine della convalida, controllare le impostazioni e selezionare **Crea**. Per apportare modifiche, selezionare le schede in alto oppure selezionare **Passaggio precedente: Configurazioni**.

    ![Creazione di una macchina virtuale CloudSimple - Rivedi e crea](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare l'elenco di macchine virtuali CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Gestire le macchine virtuali CloudSimple da Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
