---
title: Soluzione Azure di VMware da CloudSimple Quickstart - usare le macchine virtuali VMware in Azure
description: Informazioni su come configurare e usare le macchine virtuali VMware dal portale di Azure usando Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3940adfaa42de8ac9c3f32a9eadc8f6d643ce3ce
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149480"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Guida introduttiva - usare le macchine virtuali VMware in Azure

Per creare una macchina virtuale nel portale di Azure, usare i modelli di macchina virtuale che l'amministratore CloudSimple ha abilitato per la sottoscrizione. Questi modelli di macchina virtuale si trovano sull'infrastruttura VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Creazione di una VM CloudSimple in Azure richiede un modello di macchina virtuale

Creare una macchina virtuale nel cloud privato da vCenter dell'interfaccia utente. Per creare un modello, seguire le istruzioni in [clonare una macchina virtuale a un modello nel Client Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store il modello di macchina virtuale in vCenter cloud privato.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure

1. Selezionare **Tutti i servizi**.

2. Cercare **macchine virtuali CloudSimple**.

3. Fare clic su **Aggiungi**.

    ![Crea macchina virtuale CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Immettere le informazioni di base selezionare **successiva: dimensione**.

    ![Creare la macchina virtuale di CloudSimple - nozioni di base](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | DESCRIZIONE |
    | ------------ | ------------- |
    | Sottoscrizione | Sottoscrizione di Azure associata del Cloud privato.  |
    | Gruppo di risorse | Gruppo di risorse a cui verrà assegnata della macchina virtuale. È possibile selezionare un gruppo esistente o crearne uno nuovo. |
    | NOME | Nome per identificare la macchina virtuale.  |
    | Località | Area di Azure in cui viene ospitata questa macchina virtuale.  |
    | Cloud privato | CloudSimple Private Cloud in cui si desidera creare la macchina virtuale. |
    | Pool di risorse | Il mapping del pool di risorse per la macchina virtuale. Selezionare i pool di risorse disponibili. |
    | vSphere modello | modello di vSphere per la macchina virtuale.  |
    | Nome utente | Nome utente dell'amministratore della macchina virtuale (per i modelli di Windows)|
    | Password |  Password per l'amministratore della macchina virtuale (per i modelli di Windows). |
    | Conferma password | Confermare la password |

5. Selezionare il numero di core e la capacità di memoria per la macchina virtuale e fare clic su **successiva: configurazioni**. Selezionare la casella di controllo se si vuole esporre la virtualizzazione completa della CPU per il sistema operativo guest. Applicazioni che richiedono la virtualizzazione hardware possono essere eseguite in macchine virtuali senza traduzione binaria o di paravirtualizzazione. Per altre informazioni, vedere l'articolo di VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">virtualizzazione assistita mediante Hardware di esporre VMware</a>.

    ![Creare la macchina virtuale di CloudSimple - dimensioni](media/create-cloudsimple-virtual-machine-size.png)

6. Configurare le interfacce di rete e i dischi come descritto nelle tabelle seguenti e fare clic su **revisione + Crea**.

    ![Creare la macchina virtuale di CloudSimple - configurazioni](media/create-cloudsimple-virtual-machine-configurations.png)

    Per le interfacce di rete, fare clic su **interfaccia di rete Add** e configurare le impostazioni seguenti.
    
    | Controllo | DESCRIZIONE |
    | ------------ | ------------- |
    | NOME | Immettere un nome per identificare l'interfaccia.  |
    | Rete | Selezionare dall'elenco del gruppo di porte distribuita configurato in vSphere il Cloud privato.  |
    | Adapter | Selezionare una scheda di vSphere nell'elenco dei tipi disponibili configurato per la macchina virtuale. Per altre informazioni, vedere l'articolo della knowledge base di VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">scelta di una scheda di rete per la macchina virtuale</a>. |
    | Accendere al riavvio del computer | Scegliere se abilitare l'hardware di interfaccia di rete quando la VM sarà avviata. Il valore predefinito è **abilitare**. |

    Per i dischi, fare clic su **Aggiungi disco** e configurare le impostazioni seguenti.

    | Elemento | DESCRIZIONE | 
    | ------------ | ------------- | 
    | NOME | Immettere un nome per identificare il disco.  | 
    | Dimensione | Selezionare una delle dimensioni disponibili.  | 
    | Controller SCSI | Selezionare un controller SCSI per il disco.  |
    | Mode | Determina come il disco fa parte di snapshot. Scegliere una delle opzioni seguenti: <br> -Independent permanente: Tutti i dati scritti sul disco vengono scritti in modo permanente.<br> -Independent non persistente: Le modifiche scritte sul disco vengono rimossi quando si spegne o ripristinare la macchina virtuale.  Modalità indipendente non permanente consente sempre il riavvio della macchina virtuale nello stesso stato. Per altre informazioni, vedere la <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentazione di VMware</a>.

7. Al termine della convalida, rivedere le impostazioni e fare clic su **Create**. Per apportare modifiche, fare clic sulle schede nella parte superiore oppure fare clic su.

    ![Crea macchina virtuale CloudSimple - rivedere](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Passaggi successivi

* [Visualizza l'elenco di macchine virtuali CloudSimple](https://docs.azure.cloudsimple.com/azure-manage-vm/)
* [Gestire la macchina virtuale CloudSimple da Azure](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)