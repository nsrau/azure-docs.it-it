---
title: Abilitare un'immagine concessa in licenza nel lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come abilitare un'immagine concessa in licenza in Azure DevTest Labs usando il portale di Azure
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: v-craic
ms.openlocfilehash: 3c969495454db2cd301fc985e512531ef0d4b103
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Abilitare un'immagine concessa in licenza nel lab in Azure DevTest Labs

In Azure DevTest Labs, un'immagine concessa in licenza include le condizioni, generalmente di terzi, che devono essere accettate prima che l'immagine diventi accessibile agli utenti nel lab. Le sezioni seguenti descrivono come rendere disponibili le immagini concesse in licenza per la creazione di macchine virtuali.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Determinare se rendere disponibile agli utenti un'immagine concessa in licenza
Il primo passaggio per consentire agli utenti di creare macchine virtuali da un'immagine concessa in licenza è assicurarsi che siano state accettate le condizioni per tale immagine. I passaggi seguenti illustrano come visualizzare lo stato dell'offerta di un'immagine concessa in licenza e, se necessario, accettare le condizioni dell'immagine.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.  

1. Nel pannello sinistro selezionare **Configurazione e criteri** in **IMPOSTAZIONI**.

1. Nel pannello sinistro in **BASI DELLE MACCHINE VIRTUALI** selezionare **Immagini del Marketplace**. 

    ![Voce di menu Immagini del Marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Verrà visualizzato un elenco di tutte le immagini disponibili nel Marketplace con lo **stato dell'offerta** per ogni immagine.

    ![Elenco delle immagini disponibili nel Marketplace con lo stato dell'offerta per ogni immagine](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Un'immagine concessa in licenza visualizza un stato dell'offerta di 
    
    - **Terms accepted** (Condizioni accettate): l'immagine concessa in licenza è a disposizione degli utenti per la creazione di macchine virtuali. 
    - **Terms review needed** (Esame condizioni necessario): l'immagine concessa in licenza non è attualmente a disposizione degli utenti. È necessario accettare le condizioni della licenza prima che gli utenti del lab possano usare l'immagine per creare macchine virtuali. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Rende disponibile un'immagine concessa in licenza agli utenti del lab
Per assicurarsi che un'immagine concessa in licenza sia disponibile per gli utenti, il proprietario di un lab con autorizzazioni di amministratore deve prima accettare le condizioni relative all'immagine concessa in licenza. Con l'abilitazione della distribuzione a livello di codice per la sottoscrizione associata a un'immagine concessa in licenza si accettano automaticamente le condizioni e informative sulla privacy relative all'immagine. [Working with Marketplace Images on Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) (Uso di immagini del Marketplace in Azure Resource Manager) fornisce altre informazioni sulla distribuzione delle immagini del Marketplace a livello di codice.

È possibile abilitare la distribuzione a livello di codice per un'immagine concessa in licenza seguendo questa procedura:

1. Nell'elenco delle **immagini del Marketplace** nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) identificare un'immagine concessa in licenza che si vuole rendere disponibile agli utenti ma per la quale non sono state ancora accettate le condizioni. Può essere ad esempio presente una macchina virtuale di data science con lo stato **Terms accepted** (Condizioni accettate) o **Terms review needed** (Esame condizioni necessario).

    ![Finestra Configura distribuzione a livello di codice](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Le macchine virtuali di data science sono immagini di macchine virtuali di Azure preinstallate, configurate e testate con alcuni degli strumenti più diffusi usati comunemente per analisi dei dati, Machine Learning e training per l'intelligenza artificiale. [Introduzione alla macchina virtuale data science di Azure per Linux e Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) offre molte informazioni sulle macchine virtuali di data science.
   >
   >

1. Nella colonna **STATO OFFERTA** relativa all'immagine, selezionare **Terms review needed** (Esame condizioni necessario).

1. Nella finestra Configura distribuzione a livello di codice selezionare **Abilita**.

    ![Finestra Configura distribuzione a livello di codice](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Possono essere elencate più sottoscrizioni nella finestra Configura distribuzione a livello di codice. Verificare che si sta abilitando la distribuzione a livello di codice solo per la sottoscrizione desiderata.
   >
   >


1. Selezionare **Salva**. Nell'elenco delle immagini del Marketplace, l'immagine visualizza ora **Terms accepted** (Condizioni accettate) ed è a disposizione degli utenti per la creazione di macchine virtuali.

## <a name="related-blog-posts"></a>Post di blog correlati

- [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copia di immagini personalizzate tra istanze di Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere una macchina virtuale all'ambiente lab](devtest-lab-add-vm.md)
