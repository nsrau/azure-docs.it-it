---
title: Collegare o scollegare un disco dati a/da una macchina virtuale in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come collegare o scollegare un disco dati a/da una macchina virtuale in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: v-craic
ms.openlocfilehash: c1f83097fe4e5da3a46f693d37b001dbb6831527
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Collegare o scollegare un disco dati a/da una macchina virtuale in Azure DevTest Labs
[Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) consente di gestire gli account di archiviazione associati ai dischi dati delle macchine virtuali. Dopo che aver collegato un nuovo disco dati a una macchina virtuale e specificato il tipo e le dimensioni del disco necessario, il disco verrà creato e gestito automaticamente in Azure. Il disco dati può quindi essere scollegato dalla macchina virtuale e successivamente ricollegato alla stessa macchina virtuale o a una macchina virtuale diversa appartenente allo stesso utente.

Questa funzionalità è utile per la gestione delle risorse di archiviazione o software esterne a ciascuna macchina virtuale. Se le risorse di archiviazione o software esistono già in un disco dati, il disco può essere collegato, scollegato e ricollegato in modo semplice e rapido a qualsiasi macchina virtuale di proprietà dell'utente proprietario del disco dati stesso.

## <a name="attach-a-data-disk"></a>Collegamento di un disco dati
Prima di collegare un disco dati a una macchina virtuale, esaminare i suggerimenti seguenti:

- La dimensione della macchina virtuale determina il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- È solo possibile collegare un disco dati a una macchina virtuale in esecuzione. Verificare che la macchina virtuale sia in esecuzione prima di provare a collegare un disco dati.

### <a name="attach-a-new-disk"></a>Collegare un nuovo disco
Eseguire la procedura seguente per creare e collegare un nuovo disco dati gestito a una macchina virtuale in Azure DevTest Labs.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
1. Nell'elenco dei lab selezionare il lab desiderato. 
1. Nell'elenco **Macchine virtuali personali** selezionare una macchina virtuale in esecuzione.
1. Scegliere **Dischi** dal menu a sinistra.

    ![Selezionare i dischi dati per una macchina virtuale](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Scegliere **Collega nuovo** per creare un nuovo disco dati e collegarlo alla macchina virtuale.

    ![Collegare un nuovo disco dati a una macchina virtuale](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Completare il riquadro **Collega un nuovo disco** immettendo il nome, il tipo e le dimensioni del disco dati.

    ![Completare il modulo "Collega un nuovo disco"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Selezionare **OK**.

Dopo qualche istante il nuovo disco dati viene creato e collegato alla macchina virtuale e quindi visualizzato nell'elenco **Dischi dati** associato alla macchina virtuale specifica.

### <a name="attach-an-existing-disk"></a>Collegare un disco esistente
Eseguire la procedura seguente per ricollegare un disco dati disponibile esistente a una macchina virtuale in esecuzione. 

1. Selezionare una macchina virtuale in esecuzione alla quale si desidera ricollegare un disco dati.
1. Scegliere **Dischi** dal menu a sinistra.
1. Scegliere **Collega esistente** per collegare un disco dati disponibile alla macchina virtuale.

    ![Collegare un disco dati esistente a una macchina virtuale](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Nel riquadro **Collega un disco esistente** scegliere OK.

    ![Collegare un disco dati esistente a una macchina virtuale](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Dopo qualche istante il disco dati viene collegato alla macchina virtuale e quindi visualizzato nell'elenco **Dischi dati** associato alla macchina virtuale specifica.

## <a name="detach-a-data-disk"></a>Scollegare un disco dati
Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Lo scollegamento rimuove il disco dalla macchina virtuale, ma lo conserva in memoria per un uso successivo.

Se si vogliono riusare i dati presenti nel disco, è possibile ricollegare il disco alla stessa macchina virtuale o collegarlo a una nuova.

### <a name="detach-from-the-vms-management-pane"></a>Scollegare un disco dati dal riquadro di gestione della macchina virtuale
1. Dall'elenco di macchine virtuali selezionare una macchina virtuale a cui è collegato un disco dati.
1. Scegliere **Dischi** dal menu a sinistra.

    ![Selezionare i dischi dati per una macchina virtuale](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. Dall'elenco **Dischi dati** selezionare il disco di dati che si desidera scollegare.
1. Selezionare **collega** nella parte superiore del riquadro dei dettagli del disco.

    ![Scollegare un disco dati](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Selezionare **Sì** per confermare che si desidera scollegare il disco dati.

Il disco viene scollegato e risulta quindi disponibile per poter essere collegato a un'altra macchina virtuale. 
### <a name="detach-from-the-labs-main-pane"></a>Scollegare un disco dati dal riquadro principale del lab
1. Nel riquadro principale dell'ambiente di test selezionare **Dischi dati personali**.

    ![Accedere ai dischi di dati dell'ambiente di test](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Fare clic con il pulsante destro del mouse sul disco dati che si desidera scollegare oppure fare clic sui relativi puntini di sospensione (...) e quindi scegliere **Scollega**.

    ![Scollegare un disco dati](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Selezionare **Sì** per confermare che si desidera scollegare il disco dati.

   > [!NOTE]
   > Se un disco dati è già scollegato, è possibile scegliere di rimuoverlo dall'elenco dei dischi disponibili selezionando **Elimina**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Aggiornare un disco dati non gestito
Se è presente una macchina virtuale che usa dischi dati non gestiti, è possibile impostarla con facilità in modo che usi i dischi gestiti. Questo processo consente di convertire sia il disco del sistema operativo che eventuali dischi dati collegati.

Per aggiornare un disco dati non gestito, eseguire la procedura descritta in questo articolo per [scollegare il disco dati](#detach-a-data-disk) da una macchina virtuale non gestita. [Ricollegare quindi il disco](#attach-an-existing-disk) a una macchina virtuale gestita per eseguire l'aggiornamento automatico del disco dati da disco non gestito a disco gestito.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come gestire i dischi di dati per [macchine virtuali richiedibili](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

