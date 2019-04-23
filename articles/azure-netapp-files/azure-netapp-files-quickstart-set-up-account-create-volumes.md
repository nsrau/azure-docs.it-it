---
title: Configurare Azure NetApp Files e creare un volume | Microsoft Docs
description: Descrive come configurare rapidamente Azure NetApp Files e creare un volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: 634f23cf3161fff09f21c79fd8300cb269dcc5b7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546577"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Configurare Azure NetApp Files e creare un volume 

Questo articolo illustra come configurare rapidamente Azure NetApp Files e creare un volume. 

## <a name="before-you-begin"></a>Prima di iniziare 

È necessario far parte del programma di anteprima pubblica ed essere nell'elenco degli elementi consentiti per l'accesso al provider di risorse Microsoft.NetApp di Azure. Per informazioni dettagliate sulla partecipazione al programma di anteprima pubblica, vedere la [pagina di iscrizione all'anteprima pubblica di Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Effettuare la registrazione ad Azure NetApp Files e del provider di risorse NetApp

1. Nel portale di Azure fare clic sull'icona di Azure Cloud Shell nell'angolo superiore destro.

      ![Icona di Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Specificare la sottoscrizione inserita nell'elenco di elementi consentiti per Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. Registrare il provider di risorse di Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Il completamento del processo di registrazione può richiedere del tempo.

## <a name="create-a-netapp-account"></a>Creare un account di NetApp

1. Nella casella di ricerca del portale di Azure immettere **Azure NetApp Files** e quindi selezionare **Azure NetApp Files (anteprima)** dall'elenco che viene visualizzato.

      ![Selezionare Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Fare clic su **+ Aggiungi** per creare un nuovo account di NetApp.

     ![Creare un nuovo account NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Nella finestra New NetApp Account (Nuovo account NetApp) specificare le informazioni seguenti: 
   1. Immettere **myaccount1** nel campo relativo al nome dell'account. 
   2. Selezionare la propria sottoscrizione.
   3. Selezionare **Crea nuovo** per creare un nuovo gruppo di risorse. Immettere **myRG1** nel campo relativo al nome del gruppo di risorse. Fare clic su **OK**. 
   4. Selezionare la località dell'account.  

      ![Finestra New NetApp Account (Nuovo account NetApp)](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Finestra Gruppo di risorse](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Fare clic su **Crea** per creare il nuovo account NetApp.

## <a name="set-up-a-capacity-pool"></a>Configurare un pool di capacità

1. Nel pannello di gestione di Azure NetApp Files selezionare l'account di NetApp appena creato (**myaccount1**).

    ![Selezione dell'account di NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Nell'area del pannello di gestione di Azure NetApp Files relativa al proprio account NetApp selezionare **Pool di capacità**.

    ![Fare clic su Pool di capacità](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Fare clic su **+ Aggiungi pool**. 

    ![Fare clic su Aggiungi pool](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Specificare le informazioni relative al pool di capacità: 
    1. Immettere **mypool1** come nome del pool.
    2. Selezionare **Premium** come livello di servizio. 
    3. Specificare **4 (TiB)** come dimensione del pool. 

5. Fare clic su **OK**.

## <a name="create-a-volume-for-azure-netapp-files"></a>Creare un volume per Azure NetApp Files

1. Nell'area del pannello di gestione di Azure NetApp Files relativa al proprio account NetApp selezionare **Volumi**.

    ![Fare clic su Volumi](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Fare clic su **+ Aggiungi volume**.

    ![Fare clic su Aggiungi volume](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Nella finestra Crea un volume specificare le informazioni relative al volume: 
   1. Immettere **myvol1** come nome del volume. 
   2. Immettere **myfilepath1** come percorso del file che verrà usato per creare il percorso di esportazione per il volume.
   3. Selezionare il pool di capacità appena creato (**mypool1**).
   4. Usare il valore predefinito per la quota. 
   5. Nella rete virtuale fare clic su **Crea nuovo** per creare una nuova rete virtuale.  Specificare quindi le informazioni seguenti:
       * Immettere **myvnet1** come nome della rete virtuale.
       * Specificare uno spazio indirizzi per la configurazione, ad esempio 10.7.0.0/16
       * Immettere **myANFsubnet** come nome della subnet.
       * Specificare l'intervallo di indirizzi della subnet, ad esempio 10.7.0.0/24. Non è possibile condividere una subnet dedicata con altre risorse.
       * Selezionare **Microsoft.NetApp/volumes** come delega per la subnet.
       * Fare clic su **OK** per creare la rete virtuale.
   6. Nella subnet selezionare la rete virtuale appena creata (**myvnet1**) come subnet di delega.

      ![Finestra Crea un volume](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Finestra Crea rete virtuale](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Fare clic su **Rivedi e crea**.

    ![Finestra Rivedi e crea](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Verificare le informazioni relative al volume e quindi fare clic su **Crea**.  
    Il volume creato viene visualizzato nel pannello Volumi.

    ![Volume creato](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>Passaggi successivi  

* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Gestione dei volumi tramite Azure NetApp Files](azure-netapp-files-manage-volumes.md) 
