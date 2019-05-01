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
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577721"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Guida introduttiva - usare le macchine virtuali VMware in Azure

Per creare una macchina virtuale nel portale di Azure, usare i modelli di macchina virtuale che l'amministratore CloudSimple ha abilitato per la sottoscrizione. Questi modelli di macchina virtuale si trovano sull'infrastruttura VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Creazione di una VM CloudSimple in Azure richiede un modello di macchina virtuale

Creare una macchina virtuale nel cloud privato da vCenter dell'interfaccia utente. Per creare un modello, seguire le istruzioni in [clonare una macchina virtuale a un modello nel Client Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store il modello di macchina virtuale in vCenter cloud privato.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure

1. Nel menu a sinistra, fare clic su **+** oppure **crea una risorsa**.

2. Nel menu a sinistra, fare clic su **Compute**, quindi fare clic su **macchina virtuale CloudSimple**.

3. Fare clic su **Confirm** per verificare che si desidera creare una nuova macchina virtuale.

4. Impostare la configurazione di base, come descritto nella tabella seguente e quindi fare clic su **successiva: Dimensioni**.

    | Campo | DESCRIZIONE |
    | ------------ | ------------- |
    | Sottoscrizione | Sottoscrizione di Azure associati alla distribuzione del cloud privato.  |
    | Gruppo di risorse | Gruppo di distribuzione a cui verrà assegnata della macchina virtuale. È possibile selezionare un gruppo esistente o crearne uno nuovo. |
    | NOME | Nome per identificare la macchina virtuale.  |
    | Località | Area di Azure in cui viene ospitata questa macchina virtuale.  |
    | Pool di risorse | Risorse fisiche per la macchina virtuale. Selezionare i pool di risorse disponibili. |
    | vSphere modello | Tipo di modello di sistema operativo per la macchina virtuale.  |
    | Nome utente | Nome utente dell'amministratore della macchina virtuale. |
    | Password di conferma password | Password per l'amministratore della macchina virtuale.  |

5. Selezionare il numero di core e la capacità di memoria per la macchina virtuale.

6. (Facoltativo) Se si desidera esporre la virtualizzazione completa della CPU per il sistema operativo guest, selezionare la **esporre al sistema operativo Guest** casella di controllo.
Questa selezione consente alle applicazioni che richiedono la virtualizzazione dell'hardware per l'esecuzione in macchine virtuali senza traduzione binaria o di paravirtualizzazione. Per altre informazioni, vedere l'articolo di VMware [virtualizzazione assistita mediante Hardware di esporre VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

7. Fare clic su **Avanti: Configurazione**.

8. Configurare le interfacce di rete e i dischi come descritto nelle tabelle seguenti.

    Per le interfacce di rete, fare clic su **interfaccia di rete Add** e configurare le impostazioni seguenti.

    | Controllo | DESCRIZIONE |
    | ------------ | ------------- |
    | NOME | Immettere un nome per identificare l'interfaccia.  |
    | Rete | Selezionare dall'elenco di reti configurate in vSphere il Cloud privato.  |
    | Adapter | Selezionare una scheda di vSphere nell'elenco dei tipi disponibili configurato per la macchina virtuale. Per altre informazioni, vedere l'articolo della knowledge base di VMware [scelta di una scheda di rete per la macchina virtuale](https://kb.vmware.com/s/article/1001805). |
    | Accendere al riavvio del computer | Scegliere se abilitare l'hardware di interfaccia di rete quando la VM sarà avviata. Il valore predefinito è **abilitare**. |

    Per i dischi, fare clic su **Aggiungi disco** e configurare le impostazioni seguenti.

    | Elemento | DESCRIZIONE |
    | ------------ | ------------- |
    | NOME | Immettere un nome per identificare il disco.  |
    | Dimensione | Selezionare una delle dimensioni disponibili.  |
    | Controller SCSI | Selezionare un controller SCSI. I controller disponibili variano per diversi sistemi operativi supportati.  |
    | Mode | Determina come il disco fa parte di snapshot. Scegliere una delle opzioni seguenti: <br> -Independent permanente: Tutti i dati scritti sul disco vengono scritti in modo permanente.<br> -Independent non persistente: Le modifiche scritte sul disco vengono rimossi quando si spegne o ripristinare la macchina virtuale.  Modalità indipendente non permanente consente sempre il riavvio della macchina virtuale nello stesso stato. Per altre informazioni, vedere il [documentazione di VMware.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. Rivedere le impostazioni. Per apportare modifiche, fare clic sulle schede nella parte superiore.

10. Fare clic su **Create** per salvare le impostazioni e creare la macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

* [Visualizza l'elenco di macchine virtuali CloudSimple](https://docs.azure.cloudsimple.com/azurelistvms/)
* [Gestire la macchina virtuale CloudSimple da Azure](https://docs.azure.cloudsimple.com/azureoverviewpage/)