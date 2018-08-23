---
title: Azure Stack Quick Start - creare una macchina virtuale Windows
description: Azure Stack Quick Start - creare una VM Windows tramite il portale
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: mabrigg
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: efe6213e5c0261fb26ac40e74c2b0f6e0c9252dd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139706"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Guida introduttiva: creare una macchina virtuale di Windows server con il portale di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile creare una macchina virtuale Windows Server 2016 usando il portale di Azure Stack. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale.

## <a name="sign-in-to-the-azure-stack-portal"></a>Accedere al portale di Azure Stack

Accedere al portale di Azure Stack. L'indirizzo del portale di Azure Stack dipende dal quale prodotto di Azure Stack ci si connette a:

* Per Azure Stack Development Kit (ASDK) passare a: https://portal.local.azurestack.external.
* Per un sistema integrato Azure Stack, passare all'URL che ha fornito l'operatore di Azure Stack.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Fare clic su **nuove** > **calcolo** > **Windows Server 2016 Datacenter Eval** > **creare**. Se non viene visualizzata **Windows Server 2016 Datacenter Eval** voce, contattare l'operatore di Azure Stack. Chiedere a essi aggiungerlo a marketplace come spiegato nel [aggiungere l'immagine di macchina virtuale di Windows Server 2016 nel Marketplace di Azure Stack](../azure-stack-add-default-image.md) articolo.

    ![Passaggi per creare una macchina virtuale Windows nel portale](media/azure-stack-quick-windows-portal/image01.png)
2. Sotto **nozioni di base**, digitare un **Name**, **nome utente**, e **Password**. Scegliere una **Sottoscrizione**. Creare un **gruppo di risorse**, o selezionarne un esistente, selezionare una **posizione**, quindi fare clic su **OK**.

    ![Configurare le impostazioni di base](media/azure-stack-quick-windows-portal/image02.png)
3. Sotto **Scegli una dimensione**, fare clic su **D1 Standard** > **selezionare**.
    ![Scegliere le dimensioni della macchina virtuale](media/azure-stack-quick-windows-portal/image03.png)
4. Sotto **le impostazioni**, accettare le impostazioni predefinite e fare clic su **OK**.
    ![Configurare le impostazioni della macchina virtuale](media/azure-stack-quick-windows-portal/image04.png)
5. Sotto **Summary**, fare clic su **OK** per creare la macchina virtuale.
    ![Visualizza riepilogo e crea macchina virtuale](media/azure-stack-quick-windows-portal/image05.png)
6. Per visualizzare la nuova macchina virtuale, fare clic su **tutte le risorse**, cercare il nome della macchina virtuale e quindi fare clic sul relativo nome nei risultati della ricerca.
    ![Vedere macchina virtuale](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando hai finito con la macchina virtuale, eliminare la macchina virtuale e le relative risorse. A tale scopo, selezionare il gruppo di risorse nella pagina macchina virtuale e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata distribuita una macchina virtuale di Windows Server base. Per altre informazioni sulle macchine virtuali di Azure Stack, continuare [considerazioni sulle macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
