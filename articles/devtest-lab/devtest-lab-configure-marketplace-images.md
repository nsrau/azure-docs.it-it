---
title: Configurare le impostazioni dell'immagine di Azure Marketplace in Azure DevTest Labs | Documentazione Microsoft
description: Specificare le immagini di Azure Marketplace che possono essere usate per la creazione di una VM in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: v-craic
ms.openlocfilehash: 5f7c9be115b27d6033429c1224fa15fd7b844c1d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Configurare le impostazioni dell'immagine di Azure Marketplace in Azure DevTest Labs
Lab di sviluppo/test supporta la creazione di VM basate su immagini di Azure Marketplace in base alla configurazione delle immagini di Azure Marketplace da usare nel lab. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab. Ciò garantisce che il team abbia accesso solo alle immagini del Marketplace necessarie. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Selezionare le immagini di Azure Marketplace consentite per la creazione di una macchina virtuale
1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato. 
4. Nel pannello del lab selezionare **Configurazione e criteri**.
5. Nel pannello **Configuration and policies** (Configurazione e criteri) di lab in **Virtual Machine Bases** (Basi della macchina virtuale) selezionare **Marketplace images** (Immagini Marketplace).
6. Specificare se si desidera che tutte le immagini di Azure Marketplace qualificate siano disponibili per essere usate come base di una nuova macchina virtuale. Se si seleziona **Sì**, tutte le immagini di Azure Marketplace che soddisfano tutti i criteri seguenti saranno disponibili nel lab:
   
   * L'immagine crea una singola macchina virtuale **e**
   * L'immagine usa Azure Resource Manager per il provisioning delle macchine virtuali **e**
   * L'immagine non richiede l'acquisto di un piano di licenze aggiuntivo
     
    Se non si desidera autorizzare alcuna immagine o si desidera specificare le immagini che possono essere usate, selezionare **No**.
     
     ![Opzione per consentire l'uso di tutte le immagini di Marketplace come immagini di base per le macchine virtuali](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Se si seleziona **No** nel passaggio precedente, la casella di controllo **Immagini consentite/Seleziona tutto** viene abilitata. 
   È possibile usare questa opzione con la casella di ricerca per selezionare o deselezionare rapidamente tutti gli elementi visualizzati nell'elenco.
   * Selezionare le singole immagini di Azure Marketplace che si desidera rendere disponibili per la creazione di macchine virtuali selezionando la casella di controllo corrispondente all'immagine.
   * Non selezionare alcuna voce nell'elenco se non si desidera rendere disponibile alcuna immagine di Azure Marketplace per l'uso nel lab.
   
    ![È possibile specificare quali immagini di Azure Marketplace possono essere usate come immagini di base per le macchine virtuali](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere configurato le immagini di Azure Marketplace consentite per la creazione di una macchina virtuale, il passaggio successivo consiste nell'[aggiungere una macchina virtuale nel lab](devtest-lab-add-vm.md).

