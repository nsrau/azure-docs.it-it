---
title: Creare una macchina virtuale di test nello Stack di Azure | Documenti Microsoft
description: Informazioni su come eseguire il provisioning di una macchina virtuale nello Stack di Azure come operatore di cloud di test.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: erikje
ms.openlocfilehash: 233cf4df53af6a49e5fe4c5d51e112d8196a7530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>Creare una macchina virtuale di test nello Stack di Azure

*Si applica a: Azure Stack Development Kit*

Come operatore di Stack di Azure, è possibile creare una macchina virtuale di test per convalidare il [Azure Stack](azure-stack-poc.md) deployment Kit per sviluppatori.

> [!NOTE]
> È possibile eseguire il provisioning di macchine virtuali, è necessario [aggiungere l'immagine di valutazione di Windows Server 2016 nel Marketplace Azure Stack](azure-stack-add-default-image.md).
> 
> 

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
1. Nell'host del Kit di sviluppo dello Stack di Azure, [Accedi](azure-stack-connect-azure-stack.md) al portale di amministrazione (`https://adminportal.local.azurestack.external`), quindi fare clic su **New** > **calcolo**  >  **Windows Server 2016 Datacenter Eval** > **creare**.  
2. Nel **nozioni di base** pannello, digitare un **nome**, **nome utente**, e **Password**. Scegliere una **Sottoscrizione**. Creare un **gruppo di risorse**, o selezionarne uno esistente e quindi fare clic su **OK**.  
3. Nel **scegliere una dimensione** pannello, fare clic su **A1 Standard**, quindi fare clic su **selezionare**.  
4. Nel **impostazioni** pannello, accettare le impostazioni predefinite e fare clic su **OK**
5. Nel **riepilogo** pannello, fare clic su **OK** per creare la macchina virtuale.  
6. Per visualizzare la nuova macchina virtuale, fare clic su **tutte le risorse**, quindi cercare la macchina virtuale e fare clic sul relativo nome.
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>Passaggi successivi
[Tramite i portali di amministratore e utente nello Stack di Azure](azure-stack-manage-portals.md)
