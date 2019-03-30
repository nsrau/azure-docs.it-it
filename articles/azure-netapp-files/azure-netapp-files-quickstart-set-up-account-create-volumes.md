---
title: Impostare i file di Azure NetApp e creare un volume | Microsoft Docs
description: Viene descritto come configurare i file di Azure NetApp rapidamente e creare un volume.
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
ms.topic: quickstarts
ms.date: 2/20/2019
ms.author: b-juche
ms.openlocfilehash: e2b9b3cdcb712fcf6c415f574dc687e80ae9ee3b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660511"
---
# <a name="set-up-azure-netapp-files-and-create-a-volume"></a>Configurare Azure NetApp Files e creare un volume 

Questo articolo illustra come impostare i file di Azure NetApp rapidamente e creare un volume. 

## <a name="before-you-begin"></a>Prima di iniziare 

È necessario far parte del programma di anteprima pubblica ed essere nell'elenco degli elementi consentiti per l'accesso al provider di risorse Microsoft.NetApp di Azure. Per informazioni dettagliate sulla partecipazione al programma di anteprima pubblica, vedere la [pagina di iscrizione all'anteprima pubblica di Azure NetApp Files](https://aka.ms/nfspublicpreview). 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Eseguire la registrazione per i file di Azure di NetApp e Provider di risorse di NetApp

1. Dal portale di Azure, fare clic sull'icona Azure Cloud Shell in alto a destra.

      ![Icona di Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Specificare la sottoscrizione a cui è stato inserito nella whitelist per file di Azure NetApp:
    
        az account set --subscription <subscriptionId>

3. Registrare il Provider di risorse di Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Il completamento del processo di registrazione può richiedere del tempo.

## <a name="create-a-netapp-account"></a>Creare un account di NetApp

1. Nella casella di ricerca del portale di Azure, immettere **file di Azure NetApp** e quindi selezionare **Azure NetApp file (anteprima)** dall'elenco visualizzato.

      ![Selezionare i file di Azure NetApp](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Fare clic su **+ Aggiungi** per creare un nuovo account di NetApp.

     ![Crea nuovo account di NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Nella finestra Nuovo Account di NetApp, fornire le informazioni seguenti: 
   1. Immettere **myaccount1** per il nome dell'account. 
   2. Selezionare la propria sottoscrizione.
   3. Selezionare **Crea nuovo** creare nuovo gruppo di risorse. Immettere **myRG1** per il nome del gruppo di risorse. Fare clic su **OK**. 
   4. Selezionare la località dell'account.  

      ![Finestra Nuovo Account di NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Finestra di gruppo di risorse](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Fare clic su **Create** per creare il nuovo account di NetApp.

## <a name="set-up-a-capacity-pool"></a>Configurare un pool di capacità

1. Dal Pannello di gestione file di Azure NetApp, selezionare l'account di NetApp (**myaccount1**).

    ![Selezionare account NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Dal Pannello di gestione file di NetApp Azure dell'account di NetApp, fare clic su **pool di capacità**.

    ![Fare clic su pool di capacità](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Fare clic su **+ Aggiungi pool**. 

    ![Fare clic su Aggiungi pool](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Forniscono informazioni per il pool di capacità: 
    1. Immettere **mypool1** come il nome del pool.
    2. Selezionare **Premium** del livello di servizio. 
    3. Specificare **4 (TiB)** come le dimensioni del pool. 

5. Fare clic su **OK**.

## <a name="create-a-volume-for-azure-netapp-files"></a>Creare un volume per Azure NetApp Files

1. Dal Pannello di gestione file di NetApp Azure dell'account di NetApp, fare clic su **volumi**.

    ![Fare clic su volumi](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Fare clic su **+ Aggiungi volume**.

    ![Fare clic su Aggiungi volumi](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Nella casella Crea una finestra del Volume, forniscono informazioni per il volume: 
   1. Immettere **myvol1** come il nome del volume. 
   2. Immettere **myfilepath1** come percorso del file che verrà utilizzato per creare il percorso di esportazione per il volume.
   3. Selezionare il pool di capacità (**mypool1**).
   4. Usare il valore predefinito per la quota. 
   5. Nella rete virtuale, fare clic su **Crea nuovo** per creare una nuova rete virtuale di Azure (Vnet).  Compilare quindi le informazioni seguenti:
       * Immettere **myvnet1** come nome della rete virtuale.
       * Specificare uno spazio indirizzi per l'impostazione, ad esempio, 10.7.0.0/16
       * Immettere **myANFsubnet** come il nome della subnet.
       * Specificare l'intervallo di indirizzi di subnet, ad esempio, 10.7.0.0/24. Si noti che non è possibile condividere la subnet dedicata con altre risorse.
       * Selezionare **Microsoft.NetApp/volumes** per la delega di subnet.
       * Fare clic su **OK** creare la rete virtuale.
   6. Nella subnet, selezionare la rete virtuale appena creata (**myvnet1**) come subnet del delegato.

      ![Creare una finestra del volume](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Creare la finestra di rete virtuale](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Fare clic su **Rivedi e crea**.

    ![Esaminare e creare la finestra](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Esaminare le informazioni per il volume, quindi fare clic su **Create**.  
    Il volume creato viene visualizzato nel pannello volumi.

    ![Volume creato](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="next-steps"></a>Passaggi successivi  

* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Gestire i volumi con file di Azure NetApp](azure-netapp-files-manage-volumes.md) 
