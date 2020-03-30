---
title: Soluzione Di Azure VMware di CloudSimple - Creare una macchina virtuale in Azure con modelli di macchine virtualiAzure VMware Solution by CloudSimple - Create a virtual machine in Azure with VM templates
description: Descrive come creare macchine virtuali in Azure usando i modelli di macchina virtuale nell'infrastruttura VMware per il cloud privato CloudSimpleDescribes how to create virtual machines in Azure using VM templates on the VMware infrastructure for your CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244693"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Creare una macchina virtuale in Azure usando i modelli di macchina virtuale nell'infrastruttura VMwareCreate a virtual machine in Azure using VM templates on the VMware infrastructure

È possibile creare una macchina virtuale nel portale di Azure usando i modelli di macchina virtuale nell'infrastruttura VMware abilitata dall'amministratore CloudSimple per la sottoscrizione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale](https://portal.azure.com)di Azure .

## <a name="create-cloudsimple-virtual-machine"></a>Creare una macchina virtuale CloudSimpleCreate CloudSimple virtual machine

1. Selezionare **Tutti i servizi**.

2. Cercare **Macchine virtuali CloudSimple**.

3. Fare clic su **Aggiungi**.

    ![Creare una macchina virtuale CloudSimpleCreate CloudSimple virtual machine](media/create-cloudsimple-virtual-machine.png)

4. Immettere le informazioni di base, fare clic su **Avanti:Dimensioni**.

    > [!NOTE]
    > La creazione di macchine virtuali CloudSimple in Azure richiede un modello di macchina virtuale.  Questo modello di macchina virtuale deve essere presente nel centro di private cloud.  Creare una macchina virtuale nel cloud privato dall'interfaccia utente di vCenter con il sistema operativo e le configurazioni desiderate.  Utilizzando le istruzioni riportate in [Clonare una macchina virtuale in un modello nel client Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html), creare un modello.

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
    | Nome utente | Nome utente dell'amministratore della macchina virtuale (per i modelli di Windows)|
    | Password <br>Conferma password | Password per l'amministratore della macchina virtuale (per i modelli di Windows).  |

5. Selezionare il numero di core e la capacità di memoria per la macchina virtuale e fare clic su **Avanti:Configurazioni**. Selezionare la casella di controllo se si desidera esporre la virtualizzazione completa della CPU al sistema operativo guest in modo che le applicazioni che richiedono la virtualizzazione hardware possano essere eseguite su macchine virtuali senza traduzione binaria o paravirtualizzazione. Per altre informazioni, vedere l'articolo [Esporre la virtualizzazione assistita mediante hardware VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html) sul sito Web VMware.

    ![Crea macchina virtuale CloudSimple - dimensioni](media/create-cloudsimple-virtual-machine-size.png)

6. Configurare le interfacce di rete e i dischi come descritto nelle tabelle seguenti e fare clic su **Revisione e creazione**.

    ![Creare una macchina virtuale CloudSimple - configurazioniCreate CloudSimple virtual machine - configurations](media/create-cloudsimple-virtual-machine-configurations.png)

    Per le interfacce di rete, fare clic su **Aggiungi interfaccia** di rete e configurare le impostazioni seguenti.

    | Controllo | Descrizione |
    | ------------ | ------------- |
    | Nome | Immettere un nome per identificare l'interfaccia.  |
    | Rete | Selezionare dall'elenco di gruppo di porte distribuite configurato in VSphere di Private Cloud.  |
    | Adattatore | Selezionare un adattatore vSphere dall'elenco dei tipi disponibili configurati per la macchina virtuale. Per ulteriori informazioni, vedere l'articolo della Knowledge Base VMware [Sulla scelta di una scheda di rete per la macchina virtuale.](https://kb.vmware.com/s/article/1001805) |
    | Power on at Boot (Accendi all'avvio) | Scegliere se abilitare l'hardware NIC all'avvio della macchina virtuale. Il valore predefinito è **Abilita**. |

    Per i dischi, fare clic su **Aggiungi disco** e configurare le impostazioni seguenti.

    | Elemento | Descrizione |
    | ------------ | ------------- |
    | Nome | Immettere un nome per identificare il disco.  |
    | Dimensione | Selezionare una delle dimensioni disponibili.  |
    | Controller SCSI | Selezionare un controller SCSI per il disco.  |
    | Mode | Determina la modalità di partecipazione del disco alle istantanee. Scegliere una delle opzioni seguenti: <br> - Permanente indipendente: tutti i dati scritti sul disco vengono scritti in modo permanente.- Independent persistent: All data written to the disk is written permanently.<br> - Indipendente non persistente: le modifiche scritte sul disco vengono eliminate quando si spegne o si reimposta la macchina virtuale.- Independent non-persistent: Changes written to the disk are discarded when you off or reset the virtual machine.  La modalità indipendente non permanente consente di riavviare la macchina virtuale sempre nello stesso stato. Per altre informazioni, vedere la [documentazione di VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Al termine della convalida, rivedere le impostazioni e fare clic su **Crea**. Per apportare modifiche, fare clic sulle schede nella parte superiore o fare clic.

    ![Creare una macchina virtuale CloudSimple - revisioneCreate CloudSimple virtual machine - review](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Visualizzare l'elenco di macchine virtuali CloudSimple

1. Selezionare **Tutti i servizi**.

2. Cercare **Macchine virtuali CloudSimple**.

3. Selezionare l'oggetto in cui è stato creato il cloud privato.

    ![Elenco delle macchine virtuali CloudSimple](media/list-cloudsimple-virtual-machines.png)

L'elenco delle macchine virtuali CloudSimple include macchine virtuali create dal portale di Azure.List of CloudSimple virtual machines includes virtual machines created from Azure portal.  Le macchine virtuali create in Private Cloud vCenter nel pool di risorse vCenter mappato verranno visualizzate nell'elenco.  
