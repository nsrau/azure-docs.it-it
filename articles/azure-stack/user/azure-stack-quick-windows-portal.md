---
title: Azure Stack Quick Start - creare una macchina virtuale Windows
description: Azure Stack Quick Start - creare una VM Windows tramite il portale
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: c23778a6c7fd864cf741bad41c455dbb3314563a
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243203"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Guida introduttiva: creare una macchina virtuale di Windows server con il portale di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile creare una macchina virtuale Windows Server 2016 usando il portale di Azure Stack. Seguire i passaggi descritti in questo articolo per creare e usare una macchina virtuale.

> [!NOTE]  
> Le immagini dello schermo in questo articolo vengono aggiornate di conseguenza l'interfaccia utente che è stata introdotta con la versione di Azure Stack 1808. 1808 aggiunge supporto per l'utilizzo *dischi gestiti* oltre ai dischi non gestiti. Se si usa una versione precedente, alcune immagini simile alla selezione del disco, sarà diverso rispetto a quello visualizzato in questo articolo.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Accedere al portale di Azure Stack

Accedere al portale di Azure Stack. L'indirizzo del portale di Azure Stack dipende dal quale prodotto di Azure Stack ci si connette a:

* Per Azure Stack Development Kit (ASDK) passare a: https://portal.local.azurestack.external.
* Per un sistema integrato Azure Stack, passare all'URL che ha fornito l'operatore di Azure Stack.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

1. Fare clic su **+ crea una risorsa** > **calcolo** > **Windows Server 2016 Datacenter: come si-pagamento**  >   **Creare**. Se non viene visualizzata **Windows Server 2016 Datacenter: come si-pagamento** voce, contattare l'operatore di Azure Stack. Chiedere a essi aggiungerlo a marketplace come spiegato nel [aggiungere l'immagine di macchina virtuale di Windows Server 2016 nel Marketplace di Azure Stack](../azure-stack-add-default-image.md) articolo.

    ![Passaggi per creare una macchina virtuale Windows nel portale](media/azure-stack-quick-windows-portal/image01.png)
2. Sotto **nozioni di base**, digitare un **Name**, **nome utente**, e **Password**. Scegliere una **Sottoscrizione**. Creare un **gruppo di risorse**, o selezionarne un esistente, selezionare una **posizione**, quindi fare clic su **OK**.

    ![Configurare le impostazioni di base](media/azure-stack-quick-windows-portal/image02.png)
3. Sotto **dimensioni** selezionate **D1 Standard**, quindi fare clic su **selezionare**.  
    ![Scegliere le dimensioni della macchina virtuale](media/azure-stack-quick-windows-portal/image03.png)

4. Nel **impostazioni** pagina, apportare le modifiche desiderate ai valori predefiniti.
   - Partire dalla versione di Azure Stack 1808, è possibile configurare **memorizzazione** in cui è possibile scegliere di usare *dischi gestiti*. Prima della versione 1808 sono utilizzabile solo dischi non gestiti.  
   ![Configurare le impostazioni della macchina virtuale](media/azure-stack-quick-windows-portal/image04.png)  
   Quando le configurazioni sono pronte, selezionare **OK** per continuare.

5. Sotto **Summary**, fare clic su **OK** per creare la macchina virtuale.
    ![Visualizza riepilogo e crea macchina virtuale](media/azure-stack-quick-windows-portal/image05.png)

6. Per visualizzare la nuova macchina virtuale, fare clic su **tutte le risorse**, cercare il nome della macchina virtuale e quindi fare clic sul relativo nome nei risultati della ricerca.
    ![Vedere macchina virtuale](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando hai finito con la macchina virtuale, eliminare la macchina virtuale e le relative risorse. A tale scopo, selezionare il gruppo di risorse nella pagina macchina virtuale e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata distribuita una macchina virtuale di Windows Server base. Per altre informazioni sulle macchine virtuali di Azure Stack, continuare [considerazioni sulle macchine virtuali in Azure Stack](azure-stack-vm-considerations.md).
