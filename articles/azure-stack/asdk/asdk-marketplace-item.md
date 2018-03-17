---
title: Aggiungere un elemento del marketplace Azure Stack da Azure | Documenti Microsoft
description: Viene descritto come aggiungere un'immagine di macchina virtuale di Azure basato su Windows Server a Azure Marketplace dello Stack.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>Esercitazione: aggiunta di un elemento del marketplace Azure Stack da Azure

Un operatore di Stack di Azure, la prima operazione da eseguire per consentire agli utenti di distribuire una macchina virtuale (VM) consiste nell'aggiungere un'immagine di macchina virtuale nel Marketplace dello Stack di Azure. Per impostazione predefinita, viene pubblicato nulla nel Marketplace dello Stack di Azure, ma è possibile scaricare gli elementi dalla [un elenco di elementi di Azure marketplace curato](.\.\azure-stack-marketplace-azure-items.md) cui è stato pre-verificate per l'esecuzione nello Stack di Azure. Utilizzare questa opzione se si opera in uno scenario connesso e registrato in Azure l'istanza dello Stack di Azure.

In questa esercitazione è aggiungere l'immagine di macchina virtuale di Windows Server 2016 da Azure marketplace nel Marketplace dello Stack di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un elemento del marketplace Stack Azure della macchina virtuale di Windows Server
> * Verificare che l'immagine di macchina virtuale è disponibile 
> * Immagine di macchina virtuale di test

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Installare il [i moduli di Azure Stack-compatibile con Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)
- Scaricare il [gli strumenti di Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools)
- [Registrare il ASDK](asdk-register.md) con la sottoscrizione di Azure

## <a name="add-a-windows-server-vm-image"></a>Aggiungere un'immagine di macchina virtuale Windows Server
È possibile aggiungere un'immagine di Windows Server 2016 nel Marketplace dello Stack di Azure, scaricare l'immagine da Azure marketplace. Utilizzare questa opzione se si sta operando in un scenario connesso e si dispone già [registrata l'istanza di Azure Stack](asdk-register.md) con Azure.

1. Accedi ai [portale di amministrazione di ASDK](https://adminportal.local.azurestack.external).

2. Selezionare **più servizi** > **gestione Marketplace** > **Aggiungi da Azure**. 

   ![Aggiungere da Azure](media/asdk-marketplace-item/azs-marketplace.png)

3. Trovare o cercare il **Windows Server 2016 Datacenter** immagine.

4. Selezionare il **Data Center di Windows Server 2016** immagine e quindi selezionare **scaricare**.

   ![Scaricare l'immagine da Azure](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> Sono necessari circa un'ora per scaricare l'immagine di macchina virtuale e pubblicarla nel Marketplace dello Stack di Azure. 

Al termine del download, l'immagine sarà pubblicato e disponibile sotto **Marketplace gestione**. L'immagine è disponibile per **calcolo** per creare nuove macchine virtuali.

## <a name="verify-the-marketplace-item-is-available"></a>Verificare che sia disponibile l'elemento del marketplace
Utilizzare questi passaggi per verificare che la nuova immagine di macchina virtuale è disponibile in marketplace dello Stack di Azure.

1. Accedi ai [portale di amministrazione di ASDK](https://adminportal.local.azurestack.external).

2. Selezionare **altri servizi** > **Marketplace gestione**. 

3. Verificare che l'immagine di macchina virtuale di Windows Server 2016 Data Center è stato aggiunto correttamente.

   ![Immagine scaricata da Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Immagine di macchina virtuale di test
Un operatore di Stack di Azure, è possibile usare il [portale dell'amministratore](https://adminportal.local.azurestack.external) per creare un test di convalidare l'immagine della macchina virtuale è stata resa disponibile correttamente. 

1. Accedi ai [portale di amministrazione di ASDK](https://adminportal.local.azurestack.external).

2. Fare clic su **nuove** > **calcolo** > **Data Center di Windows Server 2016** > **creare**.  
 ![Creare una macchina virtuale dall'immagine del marketplace](media/asdk-marketplace-item/new-compute.png)

3. Nel **nozioni di base** blade, immettere le informazioni seguenti e quindi fare clic su **OK**:
  - **Nome**: test-vm-1
  - **Nome utente**: AdminTestUser
  - **Password**: AzS TestVM01
  - **Sottoscrizione**: accettare la sottoscrizione di Provider predefinito
  - **Gruppo di risorse**: test-vm-rg
  - **Percorso**: locale

4. Nel **scegliere una dimensione** pannello, fare clic su **A1 Standard**, quindi fare clic su **selezionare**.  

5. Nel **impostazioni** pannello, accettare le impostazioni predefinite e fare clic su **OK**

6. Nel **riepilogo** pannello, fare clic su **OK** per creare la macchina virtuale.  
> [!NOTE]
> Distribuzione della macchina virtuale richiede alcuni minuti.

7. Per visualizzare per la nuova macchina virtuale, fare clic su **macchine virtuali**, quindi cercare **test-vm-1** e fare clic sul relativo nome.
    ![Immagine di macchina virtuale di test prima visualizzata](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Dopo aver correttamente eseguito l'accesso alla macchina virtuale e verificato il corretto funzionamento dell'immagine di test, è necessario eliminare il gruppo di risorse di test. Ciò consente di liberare risorse limitate disponibili per le installazioni ASDK a nodo singolo.

Quando non è più necessario, eliminare il gruppo di risorse, macchina virtuale e tutte le risorse correlate procedendo come segue:

1. Accedi ai [portale di amministrazione di ASDK](https://adminportal.local.azurestack.external).
2. Fare clic su **gruppi di risorse** > **test-vm-rg** > **Elimina gruppo di risorse**.
3. Tipo di **test-vm-rg** come nome del gruppo di risorse e quindi fare clic su **eliminare**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere un elemento del marketplace Stack Azure della macchina virtuale di Windows Server
> * Verificare che l'immagine di macchina virtuale è disponibile 
> * Immagine di macchina virtuale di test

Passare alla prossima esercitazione per imparare a creare un'offerta di Azure Stack e un piano.

> [!div class="nextstepaction"]
> [Offerta di servizi IaaS di Azure Stack](asdk-offer-services.md)