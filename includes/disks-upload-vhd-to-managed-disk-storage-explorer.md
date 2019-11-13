---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013792"
---
Storage Explorer 1.10.0 consente agli utenti di caricare, scaricare e copiare dischi gestiti, nonché creare snapshot. A causa di queste funzionalità aggiuntive, è possibile usare Storage Explorer per eseguire la migrazione dei dati dall'ambiente locale ad Azure ed eseguire la migrazione dei dati tra le aree di Azure.

## <a name="prerequisites"></a>prerequisiti

Per completare questo articolo, è necessario quanto segue:
- Una sottoscrizione di Azure
- Uno o più dischi gestiti di Azure
- La versione più recente di [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure.

Se la Storage Explorer non è connessa ad Azure, non sarà possibile usarla per gestire le risorse. Questa sezione passa alla connessione al tuo account Azure per poter gestire le risorse usando Storage Explorer.

1. Avviare Azure Storage Explorer e fare clic sull'icona del **plug-in** a sinistra.

    ![Fare clic sull'icona del plug-in](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Selezionare **Aggiungi un account Azure**e quindi fare clic su **Avanti**.

    ![Aggiungere un account Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Nella finestra **di dialogo Azure Sign in (accesso ad Azure** ) immettere le credenziali di Azure.

    ![Finestra di dialogo di accesso di Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Selezionare la sottoscrizione dall'elenco e quindi fare clic su **Applica**.

    ![Selezionare la propria sottoscrizione](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Caricare un disco gestito da un disco rigido virtuale locale

1. Nel riquadro sinistro espandere **dischi** e selezionare il gruppo di risorse in cui si vuole caricare il disco.

    ![Selezionare il gruppo di risorse 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selezionare **Carica**.

    ![Selezionare Carica](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. In **carica VHD** specificare il disco rigido virtuale di origine, il nome del disco, il tipo di sistema operativo, l'area in cui si vuole caricare il disco e il tipo di account. In alcune aree sono supportate le zone di disponibilità, per tali aree è possibile selezionare una zona di propria scelta.
1. Selezionare **Crea** per iniziare a caricare il disco.

    ![Finestra di dialogo Carica VHD](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Lo stato del caricamento verrà ora visualizzato nelle **attività**.

    ![Stato caricamento](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Se il caricamento è terminato e il disco non viene visualizzato nel riquadro destro, selezionare **Aggiorna**.

## <a name="download-a-managed-disk"></a>Scaricare un disco gestito

I passaggi seguenti illustrano come scaricare un disco gestito in un disco rigido virtuale locale. Lo stato di un disco deve essere **disconnesso** per poter essere scaricato, non è possibile scaricare un disco **collegato** .

1. Nel riquadro sinistro, se non è già espanso, espandere **dischi** e selezionare il gruppo di risorse da cui si vuole scaricare il disco.

    ![Selezionare il gruppo di risorse 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Nel riquadro destro selezionare il disco che si vuole scaricare.
1. Selezionare **Scarica** e quindi scegliere il percorso in cui salvare il disco.

    ![Scaricare un disco gestito](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Selezionare **Salva** per avviare il download del disco. Lo stato del download viene visualizzato nelle **attività**.

    ![Stato del download](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Con Storage Explorer è possibile copiare un disco gestito all'interno o tra più aree. Per copiare un disco:

1. Dall'elenco a discesa **dischi** a sinistra selezionare il gruppo di risorse che contiene il disco che si vuole copiare.

    ![Selezionare il gruppo di risorse 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Nel riquadro destro selezionare il disco che si vuole copiare e selezionare **copia**.

    ![Copiare un disco gestito](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. Nel riquadro sinistro selezionare il gruppo di risorse in cui si vuole incollare il disco.

    ![Selezionare il gruppo di risorse 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Selezionare **Incolla** nel riquadro destro.

    ![Incollare un disco gestito](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. Nella finestra di dialogo **Incolla disco** compilare i valori. È anche possibile specificare una zona di disponibilità nelle aree supportate.

    ![Finestra di dialogo Incolla disco](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Selezionare **Incolla** per avviare la copia del disco, lo stato verrà visualizzato in **attività**.

    ![Copia stato incolla](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Creare uno snapshot

1. Dall'elenco a discesa **dischi** a sinistra selezionare il gruppo di risorse che contiene il disco che si vuole snapshot.

    ![Selezionare il gruppo di risorse 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. A destra selezionare il disco per cui si vuole eseguire lo snapshot e selezionare **Crea snapshot**.

    ![Creare uno snapshot](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. In **Crea snapshot**specificare il nome dello snapshot e il gruppo di risorse in cui si vuole crearlo. Selezionare quindi **Crea**.

    ![Finestra di dialogo Crea snapshot](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Una volta creato lo snapshot, è possibile selezionare **Apri nel portale** in **attività** per visualizzare lo snapshot nella portale di Azure.

    ![Apri snapshot nel portale](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Passaggi successivi
