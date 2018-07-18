---
title: Azure Stack Quick Start - creare una macchina virtuale di Windows
description: Azure Stack Quick Start - creare una macchina virtuale Windows usando il portale
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 5776fc472483018eb2c9e4f8962d0b1e8bce8081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155582"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Guida introduttiva: creare una macchina virtuale di Windows server con il portale di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile creare una macchina virtuale di Windows Server 2016 mediante il portale di Azure Stack. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale.

## <a name="sign-in-to-the-azure-stack-portal"></a>Accedi al portale di Azure Stack

Accedere al portale di Azure Stack. L'indirizzo del portale di Azure Stack dipende dal prodotto con cui Azure Stack verrà eseguita la connessione:

* Per Azure Stack Development Kit (ASDK) passare a: https://portal.local.azurestack.external.
* Per un sistema integrato dello Stack di Azure, andare all'URL fornito l'operatore di Stack di Azure.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Fare clic su **nuova** > **calcolo** > **Windows Server 2016 Datacenter Eval** > **creare**. Se non viene visualizzato **Eval di Data Center di Windows Server 2016** voce, contattare l'operatore di Stack di Azure. Chiedere che essi aggiungerlo nel Marketplace come descritto nel [aggiungere l'immagine di macchina virtuale di Windows Server 2016 nel Marketplace Azure Stack](../azure-stack-add-default-image.md) articolo.

    ![Passaggi per creare una macchina virtuale Windows nel portale](media/azure-stack-quick-windows-portal/image01.png)
2. In **nozioni di base**, digitare un **nome**, **nome utente**, e **Password**. Scegliere una **Sottoscrizione**. Creare un **gruppo di risorse**, oppure selezionare un esistente, selezionare un **percorso**, quindi fare clic su **OK**.

    ![Configurare le impostazioni di base](media/azure-stack-quick-windows-portal/image02.png)
3. In **scegliere una dimensione**, fare clic su **D1 Standard** > **selezionare**.
    ![Scegliere la dimensione di macchina virtuale](media/azure-stack-quick-windows-portal/image03.png)
4. In **impostazioni**, accettare le impostazioni predefinite e fare clic su **OK**.
    ![Configurare le impostazioni della macchina virtuale](media/azure-stack-quick-windows-portal/image04.png)
5. In **riepilogo**, fare clic su **OK** per creare la macchina virtuale.
    ![Visualizzare il riepilogo e creare macchine virtuali](media/azure-stack-quick-windows-portal/image05.png)
6. Per visualizzare la nuova macchina virtuale, fare clic su **tutte le risorse**, cercare il nome della macchina virtuale e quindi fare clic sul relativo nome nei risultati della ricerca.
    ![Vedere macchina virtuale](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si è terminato di utilizzare la macchina virtuale, eliminare la macchina virtuale e le relative risorse. A tale scopo, selezionare il gruppo di risorse nella pagina della macchina virtuale e fare clic su **eliminare**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è distribuita una macchina virtuale di Windows Server base. Per ulteriori informazioni sulle macchine virtuali di Azure Stack, continuare a [considerazioni per le macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
