---
title: Azure VMware Solution by CloudSimple - Manage Private Cloud VMs in Azure
description: Descrive come gestire le macchine virtuali CloudSimple Private Cloud nel portale di Azure, inclusa l'aggiunta di dischi, la modifica della capacità delle macchine virtuali e l'aggiunta di interfacce di rete
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014998"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Gestire le macchine virtuali CloudSimple Private Cloud in AzureManage your CloudSimple Private Cloud virtual machines in Azure

Per gestire le macchine virtuali [create per il cloud privato CloudSimple](azure-create-vm.md), accedere al portale di [Azure.](https://portal.azure.com) Cercare e selezionare il virtuale (cercare in **Tutti i servizi** o Macchine **virtuali** nel menu laterale).

## <a name="control-virtual-machine-operation"></a>Controllare il funzionamento della macchina virtualeControl virtual machine operation

I controlli seguenti sono disponibili nella pagina **Panoramica** della macchina virtuale selezionata.

| Controllo | Descrizione |
| ------------ | ------------- |
| Connessione | Connettersi alla macchina virtuale specificata.  |
| Inizia | Avviare la macchina virtuale specificata.  |
| Riavvio | Arrestare e accendere la macchina virtuale specificata.  |
| Arresto | Arrestare la macchina virtuale specifica.  |
| Acquisizione | Acquisire un'immagine della macchina virtuale specificata in modo che possa essere usata come immagine per creare altre macchine virtuali. Vedere [Creare un'immagine gestita di una macchina virtuale generalizzata in Azure.See Create a managed image of a generalized VM in Azure.](../virtual-machines/windows/classic/capture-image.md)   |
| Spostamento | Passare alla macchina virtuale specificata.  |
| Delete | Rimuovere la macchina virtuale specificata.  |
| Aggiorna | Aggiornare i dati sul display.  |

### <a name="view-performance-information"></a>Visualizzare le informazioni sulle prestazioni

I grafici nell'area inferiore della pagina **Panoramica** presentano i dati sul rendimento per l'intervallo selezionato (l'ultima ora alla fine di 30 giorni; l'impostazione predefinita è l'ultima ora). All'interno di ogni grafico, è possibile visualizzare i valori numerici per qualsiasi momento all'interno dell'intervallo spostando il cursore avanti e indietro sul grafico.

Vengono visualizzati i grafici seguenti.

| Elemento | Descrizione |
| ------------ | ------------- |
| CPU (media) | Utilizzo medio della CPU in percentuale nell'intervallo selezionato.   |
| Rete | Traffico in uscita e in uscita dalla rete (MB) nell'intervallo selezionato.  |
| Byte su disco | Dati totali letti dal disco e scritti su disco (MB) nell'intervallo selezionato.  |
| Operazioni su disco | Frequenza media delle operazioni su disco (operazioni/secondo) nell'intervallo selezionato. |

## <a name="manage-vm-disks"></a>Gestire i dischi delle macchine virtualiManage VM disks

Per aggiungere un disco VM, aprire la pagina **Dischi** per la macchina virtuale selezionata. Per aggiungere un disco, fare clic su **Aggiungi disco**. Configurare ciascuna delle seguenti impostazioni immettendo o selezionando un'opzione in linea. Fare clic su **Salva**.

   | Elemento | Descrizione |
   | ------------ | ------------- |
   | Nome | Immettere un nome per identificare il disco.  |
   | Dimensione | Selezionare una delle dimensioni disponibili.  |
   | Controller SCSI | Selezionare un controller SCSI. I controller disponibili variano in base ai diversi sistemi operativi supportati.  |
   | Mode | Determina la modalità di partecipazione del disco alle istantanee. Scegliere una delle opzioni seguenti: <br> - Permanente indipendente: tutti i dati scritti sul disco vengono scritti in modo permanente.- Independent persistent: All data written to the disk is written permanently.<br> - Indipendente, non persistente: le modifiche scritte sul disco vengono eliminate quando si spegne o si reimposta la macchina virtuale.- Independent, non-persistent: Changes written to the disk are discarded when you off or reset the virtual machine.  Questa modalità consente di riavviare sempre la macchina virtuale nello stesso stato. Per altre informazioni, vedere la [documentazione di VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Per eliminare un disco, selezionarlo e fare clic su **Elimina**.

## <a name="change-the-capacity-of-the-vm"></a>Modificare la capacità della macchina virtualeChange the capacity of the VM

Per modificare la capacità della macchina virtuale, aprire la pagina **Dimensioni** per la macchina virtuale selezionata. Specificare una delle seguenti opzioni e fare clic su **Salva**.

| Elemento | Descrizione |
| ------------ | ------------- |
| Numero di core | Numero di core assegnati alla macchina virtuale.  |
| Virtualizzazione hardware | Selezionare la casella di controllo per esporre la virtualizzazione hardware al sistema operativo guest. Vedere l'articolo di VMware [Expose VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Dimensione della memoria | Selezionare la quantità di memoria da allocare alla macchina virtuale.  

## <a name="manage-network-interfaces"></a>Gestire le interfacce di rete

Per aggiungere un'interfaccia, fare clic su **Aggiungi interfaccia**di rete . Configurare ciascuna delle seguenti impostazioni immettendo o selezionando un'opzione in linea. Fare clic su **Salva**.

   | Controllo | Descrizione |
   | ------------ | ------------- |
   | Nome | Immettere un nome per identificare l'interfaccia.  |
   | Rete | Selezionare dall'elenco delle reti configurate nel cloud privato vSphere.  |
   | Adattatore | Selezionare un adattatore vSphere dall'elenco dei tipi disponibili configurati per la macchina virtuale. Per ulteriori informazioni, vedere l'articolo della Knowledge Base VMware [Sulla scelta di una scheda di rete per la macchina virtuale.](https://kb.vmware.com/s/article/1001805) |
   | Power on at Boot (Accendi all'avvio) | Scegliere se abilitare l'hardware NIC all'avvio della macchina virtuale. Il valore predefinito è **Abilita**. |

Per eliminare un'interfaccia di rete, selezionarla e fare clic su **Elimina**.
