---
title: Ridimensionare una macchina virtuale in un lab in Azure DevTest Labs
description: Informazioni su come modificare le dimensioni di una macchina virtuale (VM) in Azure DevTest Labs in base alle esigenze di prestazioni di CPU, rete o disco.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bf7c425766a97aaa1d143133f04502a0aa3c36cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756178"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Ridimensionare una macchina virtuale in un lab in Azure DevTest Labs
Una delle funzionalità importanti delle macchine virtuali di Azure è la possibilità di modificare le dimensioni di una macchina virtuale (VM) in base alle esigenze per CPU, rete o prestazioni del disco. Azure DevTest Labs ora supporta questa funzionalità per le macchine virtuali in un lab. La funzione di ridimensionamento è conforme ai criteri di lab per le dimensioni delle macchine virtuali consentite nel lab. Vale a dire, è possibile modificare le dimensioni di una macchina virtuale solo per le dimensioni consentite nel lab. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Passaggi per ridimensionare una macchina virtuale in un lab 
Per ridimensionare una macchina virtuale in un lab in Azure DevTest Labs, procedere come segue: 

> [!NOTE]
> Se si è connessi alla macchina virtuale tramite una sessione desktop remoto (RDP), salvare il lavoro e disconnettersi dalla macchina virtuale prima di eseguire il ridimensionamento.

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
3. Nell'elenco dei lab selezionare il lab che include la macchina virtuale da ridimensionare.  
4. Nel pannello a sinistra, selezionare **Macchine virtuali personali**. 
5. Nell'elenco delle macchine virtuali selezionare una macchina virtuale.
6. Selezionare **Arresta** sulla barra degli strumenti se la macchina virtuale è in esecuzione. Controllare lo stato dell'operazione nella finestra **Notifiche**. Attendere che la macchina virtuale si arresti e quindi chiudere la finestra **Notifiche**. 

    ![Arrestare la VM](media/devtest-lab-resize-vm/stop-vm.png)
1. Nella pagina della propria macchina virtuale selezionare **Dimensioni** sotto **IMPOSTAZIONI** nel menu a sinistra.

    ![Menu di scelta delle dimensioni](media/devtest-lab-resize-vm/size-menu.png)
1. Nella finestra **Scegli una dimensione** sfogliare e selezionare una dimensione per la macchina virtuale e fare clic su **Seleziona**.     
1. Controllare lo stato dell'operazione di ridimensionamento nella finestra **Notifiche**.

    ![Stato del ridimensionamento](media/devtest-lab-resize-vm/resize-status.png)
10. Dopo una corretta operazione di ridimensionamento, chiudere la finestra **Notifiche**. 
11. Selezionare **Panoramica** nel menu a sinistra e scegliere **Riavvia** sulla barra degli strumenti per riavviare la macchina virtuale. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sulla funzionalità di ridimensionamento supportata dalle macchine virtuali di Azure, vedere [Ridimensionare le macchine virtuali](https://azure.microsoft.com/blog/resize-virtual-machines/).


