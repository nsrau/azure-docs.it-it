---
title: Connettersi alle macchine virtuali Windows in Azure DevTest Labs
description: Informazioni su come connettersi alla macchina virtuale Windows in un Lab (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540722"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>Connettersi a una macchina virtuale Windows nel Lab (Azure DevTest Labs)
Questo articolo illustra come connettersi a una macchina virtuale Windows nel Lab. 

## <a name="connect-to-a-windows-vm"></a>Connettersi a una macchina virtuale Windows
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra di ricerca cercare e selezionare **DevTest Labs**. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="Cercare e selezionare DevTest Labs":::    
1. Dall'elenco dei Lab selezionare il **Lab**.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="Selezionare il lab":::            
1. Nella home page per il Lab selezionare la VM Windows dall'elenco **macchine virtuali personali** . 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Selezionare la VM Windows":::                
1. Nella pagina **macchina virtuale** per la VM selezionare **Connetti** sulla barra degli strumenti. Se la macchina virtuale è arrestata, selezionare **Avvia** prima per avviare la macchina virtuale.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="Selezionare Connetti sulla barra degli strumenti":::                    
1. Se si usa il browser Microsoft Edge, viene visualizzato il collegamento al **file RDP scaricato** nella parte inferiore del browser. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="RDP scaricato":::                        
1. Aprire il file RDP e immettere le credenziali della VM digitate durante la creazione della macchina virtuale. È necessario essere connessi alla macchina virtuale Windows. 

## <a name="next-steps"></a>Passaggi successivi
[Procedura: connettersi a una VM Linux](connect-linux-virtual-machine.md)
