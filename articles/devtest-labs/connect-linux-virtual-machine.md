---
title: Connettersi alle macchine virtuali Linux in Azure DevTest Labs
description: Informazioni su come connettersi alla macchina virtuale Linux in un Lab (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86531650"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Connettersi a una VM Linux nel Lab (Azure DevTest Labs)
Questo articolo illustra come connettersi a una macchina virtuale Linux nel Lab. 

## <a name="connect-to-a-linux-vm"></a>Connettersi a una VM Linux
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra di ricerca cercare e selezionare **DevTest Labs**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="Cercare e selezionare DevTest Labs":::    
1. Dall'elenco dei Lab selezionare il **Lab**.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Cercare e selezionare DevTest Labs":::            
1. Nella home page per il Lab selezionare la VM Linux dall'elenco **macchine virtuali personali** . 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Cercare e selezionare DevTest Labs":::        
5. Nella pagina **Panoramica** è possibile visualizzare il nome di dominio completo (FQDN) o l'indirizzo IP della macchina virtuale. È anche possibile visualizzare la porta come illustrato nell'immagine seguente.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="Cercare e selezionare DevTest Labs":::    

    Si noti che il pulsante **Connetti** è disabilitato anche se la macchina virtuale è stata avviata. Si tratta di un progetto.
6.  Usare SSH per connettersi alla VM Linux. Nell'esempio seguente viene eseguita la connessione alla macchina virtuale con nome di dominio completo `mydtl07172452621450000.eastus.cloudapp.azure.com` , con nome utente `vmuser` e porta `51637` . Immettere la password per l'utente per connettersi alla macchina virtuale. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    È possibile usare strumenti come [Putty](https://www.putty.org/) o qualsiasi altro client SSH per connettersi alla macchina virtuale. 

    Dopo la connessione tramite SSH, è possibile installare e configurare un ambiente desktop ([Xfce](https://www.xfce.org)) e desktop remoto ([xrdp](http://xrdp.org)).  Per informazioni dettagliate, vedere [installare e configurare Desktop remoto per connettersi a una macchina virtuale Linux in Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Passaggi successivi
[Procedura: connettersi a una macchina virtuale Windows](connect-windows-virtual-machine.md)
