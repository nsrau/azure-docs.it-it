---
title: Avvio rapido - Configurare Azure NetApp Files e creare un volume NFS | Microsoft Docs
description: Avvio rapido - Descrive come configurare rapidamente Azure NetApp Files e creare un volume.
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
ms.date: 4/16/2019
ms.author: b-juche
ms.openlocfilehash: 4ea511bec75557bc6f7d37b1724b4b0db65ba9cc
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299445"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Guida introduttiva: Configurare Azure NetApp Files e creare un volume NFS 

Questo articolo illustra come configurare rapidamente Azure NetApp Files e creare un volume. 

In questo argomento di avvio rapido si configureranno gli elementi seguenti:

- La registrazione per Azure NetApp Files e il provider di risorse NetApp
- Un account NetApp
- Un pool di capacità
- Un volume NFS per Azure NetApp Files

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare 

> [!IMPORTANT] 
> È necessario disporre dell'accesso al servizio Azure NetApp Files.  Per richiedere l'accesso al servizio, vedere la [pagina per l'invio della richiesta di inserimento nella lista di attesa per Azure NetApp Files](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  Prima di continuare, è necessario attendere un messaggio di posta elettronica di conferma ufficiale dal team di Azure NetApp Files. 

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Effettuare la registrazione ad Azure NetApp Files e del provider di risorse NetApp

1. Nel portale di Azure fare clic sull'icona di Azure Cloud Shell nell'angolo superiore destro.

    ![Icona di Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell-window.png)

2. Specificare la sottoscrizione inserita nell'elenco di elementi consentiti per Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. Registrare il provider di risorse di Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait  

    Il completamento del processo di registrazione può richiedere del tempo.

## <a name="create-a-netapp-account"></a>Creare un account di NetApp

1. Nella casella di ricerca del portale di Azure immettere **Azure NetApp Files** e quindi selezionare **Azure NetApp Files** dall'elenco visualizzato.

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

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Creare un volume NFS per Azure NetApp Files

1. Nell'area del pannello di gestione di Azure NetApp Files relativa al proprio account NetApp selezionare **Volumi**.

    ![Fare clic su Volumi](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Fare clic su **+ Aggiungi volume**.

    ![Fare clic su Aggiungi volume](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Nella finestra Crea un volume specificare le informazioni relative al volume: 
   1. Immettere **myvol1** come nome del volume. 
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

4. Fare clic su **Protocollo** e quindi selezionare **NFS** come tipo di protocollo per il volume.   

    Immettere **myfilepath1** come percorso del file che verrà usato per creare il percorso di esportazione per il volume. 

    ![Specificare il protocollo NFS per l'avvio rapido](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Fare clic su **Rivedi e crea**.

    ![Finestra Rivedi e crea](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

5. Verificare le informazioni relative al volume e quindi fare clic su **Crea**.  
    Il volume creato viene visualizzato nel pannello Volumi.

    ![Volume creato](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, se si vuole, è possibile eliminare il gruppo di risorse. L'azione di eliminazione di un gruppo di risorse è irreversibile.  

> [!IMPORTANT]
> Eliminerà in modo definitivo tutte le risorse all'interno dei gruppi di risorse e non potrà essere annullata. 

1. Nella casella di ricerca del portale di Azure immettere **Azure NetApp Files** e quindi selezionare **Azure NetApp Files** dall'elenco visualizzato.

2. Nell'elenco delle sottoscrizioni fare clic sul gruppo di risorse (myRG1) che si vuole eliminare. 

    ![Passare ai gruppi di risorse](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Nella pagina del gruppo di risorse fare clic su **Elimina gruppo di risorse**.

    ![Eliminare un gruppo di risorse](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Si aprirà una finestra con un avviso relativo alle risorse che verranno eliminate con il gruppo di risorse.

4. Immettere il nome del gruppo di risorse (myRG1) per confermare che si intende eliminarlo in modo definitivo con tutte le risorse in esso contenute e quindi fare clic su **Elimina**.

    ![Eliminare un gruppo di risorse](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

## <a name="next-steps"></a>Passaggi successivi  

> [!div class="nextstepaction"]
> [Gestione dei volumi tramite Azure NetApp Files](azure-netapp-files-manage-volumes.md)  
