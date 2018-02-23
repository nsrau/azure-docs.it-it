---
title: Azure Stack Quick Start - creare una macchina virtuale di Windows
description: Azure Stack Quick Start - creare una macchina virtuale Windows usando il portale
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/15/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: 9df976fd26318038ab4152863638e262d7f284aa
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>Creare una macchina virtuale Windows con il portale di Azure Stack

È possibile creare una macchina virtuale Windows usando il portale di Azure Stack. Il portale è un'interfaccia utente basata su browser, in cui è possibile creare, configurare e gestire le risorse.

## <a name="sign-in-to-the-azure-stack-portal"></a>Accedi al portale di Azure Stack

Accedere al portale di Azure Stack. L'indirizzo del portale di Azure Stack varia a seconda del prodotto cui Azure Stack si è connessi:

* Per il Kit di sviluppo dello Stack (ASDK) Azure, passare a: https://portal.local.azurestack.external.
* Per un sistema integrato dello Stack di Azure, andare all'URL fornito l'operatore di Stack di Azure.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Fare clic su **nuova** > **calcolo** > **Windows Server 2016 Datacenter Eval** > **creare**. Se non viene visualizzato **Eval di Data Center di Windows Server 2016** voce, contattare l'operatore di Stack di Azure. Chiedere che essi aggiungerlo nel Marketplace come descritto nel [aggiungere l'immagine di macchina virtuale di Windows Server 2016 nel Marketplace Azure Stack](../azure-stack-add-default-image.md) articolo. 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. In **nozioni di base**, digitare un **nome**, **nome utente**, e **Password**. Scegliere una **Sottoscrizione**. Creare un **gruppo di risorse**, oppure selezionare un esistente, selezionare un **percorso**, quindi fare clic su **OK**.

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. In **scegliere una dimensione**, fare clic su **D1 Standard** > **selezionare**.
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. In **impostazioni**, accettare le impostazioni predefinite e fare clic su **OK**.
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. In **riepilogo**, fare clic su **OK** per creare la macchina virtuale. 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. Per visualizzare la nuova macchina virtuale, fare clic su **tutte le risorse**, quindi cercare la macchina virtuale e fare clic sul relativo nome.
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario la macchina virtuale, eliminare il gruppo di risorse, macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse da una pagina della macchina virtuale e fare clic su **eliminare**.

## <a name="next-steps"></a>Passaggi successivi
In questa Guida introduttiva, aver distribuito una semplice macchina virtuale di Windows. Per ulteriori informazioni sulle macchine virtuali di Azure Stack, continuare a [considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
