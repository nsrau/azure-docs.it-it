---
title: Ridistribuire una macchina virtuale in un lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come ridistribuire una macchina virtuale (spostamento da un nodo di Azure a un altro) in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454256"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Ridistribuire una macchina virtuale in un lab in Azure DevTest Labs
Se non è possibile connettersi a una macchina virtuale (VM) in un lab tramite una connessione desktop remoto, ridistribuire la macchina virtuale e riprovare a connettersi a esso. Quando si ridistribuisce una VM, DevTest Labs sposta la macchina virtuale dal nodo in cui è in esecuzione in un nuovo nodo dell'infrastruttura di Azure. La macchina virtuale viene avviata e tutte le opzioni di configurazione e le risorse associate vengono mantenute. Questa funzionalità consente di risparmiare il tempo impiegato per la risoluzione dei problemi di connessione desktop remoto o accesso all'applicazione per le macchine virtuali basate su Windows nel lab. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Passaggi per ridistribuire una macchina virtuale in un lab 
Per ridistribuire una macchina virtuale in un lab in Azure DevTest Labs, procedere come segue: 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco di lab selezionare il lab che include la macchina virtuale da ridistribuire.  
4. Nel pannello a sinistra, selezionare **Macchine virtuali personali**. 
5. Nell'elenco delle macchine virtuali selezionare una macchina virtuale.
6. Nella pagina della propria macchina virtuale selezionare **Ripetere la distribuzione** sotto **OPERAZIONI** nel menu a sinistra.

    ![Ripetere la distribuzione](media/devtest-lab-redeploy-vm/redeploy.png)
7. Leggere le informazioni nella pagina e selezionare il pulsante **Ridistribuire**. 9. Controllare lo stato dell'operazione di ridistribuzione nella finestra **Notifiche**.

    ![Ridistribuire lo stato](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come ridimensionare una macchina virtuale in Azure DevTest Labs, vedere [Ridimensionare una VM](devtest-lab-resize-vm.md).


