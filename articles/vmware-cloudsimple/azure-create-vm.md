---
title: 'Soluzione VMware di Azure di CloudSimple: creare una macchina virtuale in Azure con i modelli di VM'
description: Descrive come creare macchine virtuali in Azure usando i modelli di VM nell'infrastruttura VMware per il cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576836"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Creare una macchina virtuale in Azure usando i modelli di VM nell'infrastruttura VMware

È possibile creare una macchina virtuale nell'portale di Azure usando i modelli di VM nell'infrastruttura VMware abilitata dall'amministratore di CloudSimple per la sottoscrizione.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Creare una macchina virtuale CloudSimple

1. Selezionare **Tutti i servizi**.

2. Cercare **Macchine virtuali CloudSimple**.

3. Fare clic su **Aggiungi**.

    ![Creare una macchina virtuale CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Immettere le informazioni di base fare clic su **Avanti: dimensioni**.

    > [!NOTE]
    > Per la creazione di macchine virtuali CloudSimple in Azure è necessario un modello di macchina virtuale.  Questo modello di macchina virtuale deve esistere nel cloud privato vCenter.  Creare una macchina virtuale nel cloud privato dall'interfaccia utente di vCenter con il sistema operativo e le configurazioni desiderate.  Usando le istruzioni in [clonare una macchina virtuale in un modello nel client Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html), creare un modello.

    ![Creare una macchina virtuale CloudSimple-nozioni di base](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrizione |
    | ------------ | ------------- |
    | Sottoscrizione | Sottoscrizione di Azure associata al cloud privato.  |
    | Gruppo di risorse | Gruppo di risorse a cui verrà assegnata la macchina virtuale. È possibile selezionare un gruppo esistente o crearne uno nuovo. |
    | Name | Nome per identificare la macchina virtuale.  |
    | Location | Area di Azure in cui è ospitata questa macchina virtuale.  |
    | Cloud privato | CloudSimple cloud privato in cui si vuole creare la macchina virtuale. |
    | Pool di risorse | Pool di risorse mappato per la macchina virtuale. Selezionare un pool di risorse tra quelli disponibili. |
    | Modello vSphere | modello vSphere per la macchina virtuale.  |
    | Nome utente | Nome utente dell'amministratore della VM (per i modelli di Windows)|
    | Password <br>Conferma password | Password per l'amministratore della VM (per i modelli di Windows).  |

5. Selezionare il numero di core e la capacità di memoria per la macchina virtuale e fare clic su **Avanti: configurazioni**. Selezionare la casella di controllo se si vuole esporre la virtualizzazione completa della CPU al sistema operativo guest in modo che le applicazioni che richiedono la virtualizzazione hardware possano essere eseguite in macchine virtuali senza conversione binaria o paravirtualizzazione. Per altre informazioni, vedere l'articolo [Esporre la virtualizzazione assistita mediante hardware VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html) sul sito Web VMware.

    ![Crea macchina virtuale CloudSimple-dimensioni](media/create-cloudsimple-virtual-machine-size.png)

6. Configurare le interfacce di rete e i dischi come descritto nelle tabelle seguenti e fare clic su **Verifica + crea**.

    ![Creare una macchina virtuale CloudSimple-configurazioni](media/create-cloudsimple-virtual-machine-configurations.png)

    Per le interfacce di rete, fare clic su **Aggiungi interfaccia di rete** e configurare le impostazioni seguenti.

    | Control | Descrizione |
    | ------------ | ------------- |
    | NOME | Immettere un nome per identificare l'interfaccia.  |
    | Rete | Selezionare dall'elenco di gruppi di porte distribuite configurate nel cloud privato vSphere.  |
    | Adapter | Selezionare un adattatore vSphere dall'elenco dei tipi disponibili configurato per la macchina virtuale. Per ulteriori informazioni, vedere l'articolo della Knowledge Base VMware [scelta di una scheda di rete per la macchina virtuale](https://kb.vmware.com/s/article/1001805). |
    | Accensione all'avvio | Scegliere se abilitare l'hardware NIC all'avvio della macchina virtuale. Il valore predefinito è **Abilita**. |

    Per i dischi, fare clic su **Aggiungi disco** e configurare le impostazioni seguenti.

    | Elemento | Descrizione |
    | ------------ | ------------- |
    | Name | Immettere un nome per identificare il disco.  |
    | Dimensione | Selezionare una delle dimensioni disponibili.  |
    | Controller SCSI | Selezionare un controller SCSI per il disco.  |
    | Modalità | Determina la modalità di partecipazione del disco negli snapshot. Scegli una delle seguenti opzioni: <br> -Permanente indipendente: Tutti i dati scritti sul disco vengono scritti in modo permanente.<br> -Indipendente non persistente: le modifiche scritte su disco vengono eliminate quando si spegne o si ripristina la macchina virtuale.  La modalità indipendente non permanente consente di riavviare la macchina virtuale sempre nello stesso stato. Per altre informazioni, vedere la [documentazione di VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Al termine della convalida, rivedere le impostazioni e fare clic su **Crea**. Per apportare modifiche, fare clic sulle schede nella parte superiore o fare clic su.

    ![Creare una macchina virtuale CloudSimple-Revisione](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Visualizza l'elenco delle macchine virtuali CloudSimple

1. Selezionare **Tutti i servizi**.

2. Cercare **Macchine virtuali CloudSimple**.

3. Selezionare la in cui è stato creato il cloud privato.

    ![Elenco di macchine virtuali CloudSimple](media/list-cloudsimple-virtual-machines.png)

L'elenco delle macchine virtuali CloudSimple include le macchine virtuali create da portale di Azure.  Le macchine virtuali create nel cloud privato vCenter nel pool di risorse vCenter mappate verranno visualizzate nell'elenco.  
