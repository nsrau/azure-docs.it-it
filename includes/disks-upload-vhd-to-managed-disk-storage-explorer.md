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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013792"
---
Storage Explorer 1.10.0 consente agli utenti di caricare, scaricare e copiare dischi gestiti, nonché di creare snapshot. A causa di queste funzionalità aggiuntive, è possibile usare Storage Explorer per eseguire la migrazione dei dati da locale ad Azure ed eseguire la migrazione dei dati tra le aree di Azure.Because of these additional capabilities, you can use Storage Explorer to migrate data from on-premises to Azure, and migrate data across Azure regions.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, è necessario quanto segue:
- Una sottoscrizione di Azure.
- Uno o più dischi gestiti di Azure
- La versione più recente di [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure

Se Storage Explorer non è connesso ad Azure, non sarà possibile usarlo per gestire le risorse. Questa sezione passa alla connessione all'account Azure in modo da poter gestire le risorse usando Storage Explorer.This section goes over connecting it to your Azure account so that you can manage resources using Storage Explorer.

1. Avviare Esplora archivi di Azure e fare clic sull'icona del **plug-in** a sinistra.

    ![Fare clic sull'icona del plug-in](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Selezionare **Aggiungi un account Azure**e quindi fare clic su **Avanti**.

    ![Aggiungere un account Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Nella finestra di dialogo **Dividi di Azure** immettere le credenziali di Azure.In the Azure Sign in dialog box, enter your Azure credentials.

    ![Finestra di dialogo di accesso di AzureAzure sign in dialog](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Selezionare la sottoscrizione dall'elenco e quindi fare clic su **Applica**.

    ![Selezionare la propria sottoscrizione](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Caricare un disco gestito da un disco rigido virtuale precedenteUpload a managed disk from an on-prem VHD

1. Nel riquadro sinistro espandere **Dischi** e selezionare il gruppo di risorse in cui si vuole caricare il disco.

    ![Selezionare il gruppo di risorse 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selezionare **Carica**.

    ![Selezionare Carica](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. In **Carica disco rigido virtuale** specificare il disco rigido virtuale di origine, il nome del disco, il tipo di sistema operativo, l'area in cui si desidera caricare il disco e il tipo di account. In alcune aree Sono supportate le zone di disponibilità, per tali aree è possibile selezionare una zona di propria scelta.
1. Selezionare **Crea** per iniziare a caricare il disco.

    ![Finestra di dialogo Carica vhd](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Lo stato del caricamento verrà ora visualizzato in **Attività.**

    ![Stato caricamento](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Se il caricamento è terminato e il disco non è visualizzato nel riquadro di destra, selezionare **Aggiorna**.

## <a name="download-a-managed-disk"></a>Scaricare un disco gestitoDownload a managed disk

I passaggi seguenti illustrano come scaricare un disco gestito in un disco rigido virtuale precedente. Lo stato di un disco deve essere **Scollegato** per poter essere scaricato, non è possibile scaricare un disco **collegato.**

1. Nel riquadro sinistro, se non è già espanso, espandere **Dischi** e selezionare il gruppo di risorse da cui si vuole scaricare il disco.

    ![Selezionare il gruppo di risorse 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Nel riquadro destro selezionare il disco che si desidera scaricare.
1. Seleziona **Scarica** e quindi scegli dove vuoi salvare il disco.

    ![Scaricare un disco gestitoDownload a managed disk](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Selezionare **Salva** e il download del disco inizierà. Lo stato del download verrà visualizzato in **Attività.**

    ![Stato del download](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Con Storage Explorer è possibile copiare un disco con manging all'interno o tra aree diverse. Per copiare un disco:

1. Nell'elenco a discesa **Dischi** a sinistra selezionare il gruppo di risorse contenente il disco da copiare.

    ![Selezionare il gruppo di risorse 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Nel riquadro destro selezionare il disco da copiare e selezionare **Copia**.

    ![Copiare un disco gestito](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. Nel riquadro sinistro selezionare il gruppo di risorse in cui si vuole incollare il disco.

    ![Selezionare il gruppo di risorse 2Select resource group 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Selezionare **Incolla** nel riquadro di destra.

    ![Incollare un disco gestito](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. Nella finestra di dialogo **Incolla disco,** inserire i valori. È inoltre possibile specificare una zona di disponibilità nelle aree supportate.

    ![Finestra di dialogo Incolla disco](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Selezionare **Incolla** e il disco inizierà la copia, lo stato viene visualizzato in **Attività**.

    ![Copia stato incolla](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Creare uno snapshot

1. Nell'elenco a discesa **Dischi** a sinistra selezionare il gruppo di risorse contenente il disco da creare snapshot.

    ![Selezionare il gruppo di risorse 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. A destra, seleziona il disco che desideri creare un'istantanea e seleziona **Crea snapshot**.

    ![Creare uno snapshot](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. In **Crea snapshot**specificare il nome dello snapshot e il gruppo di risorse in cui si desidera crearlo. Quindi selezionare **Crea**.

    ![Finestra di dialogo Crea snapshot](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Dopo aver creato lo snapshot, è possibile selezionare Apri nel portale in Attività per visualizzare lo snapshot nel portale di Azure.Once the snapshot has been created, you can select **Open in Portal** in **Activities** to view the snapshot in the Azure portal.

    ![Aprire lo snapshot nel portaleOpen snapshot in portal](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Passaggi successivi
