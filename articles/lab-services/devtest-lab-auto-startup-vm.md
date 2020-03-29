---
title: Configurare le impostazioni di avvio automatico per una macchina virtuale in Azure DevTest Labs . Documenti Microsoft
description: Informazioni su come configurare le impostazioni di avvio automatico per le macchine virtuali in un lab. Questa impostazione consente l'avvio automatico delle macchine virtuali nel lab in base a una pianificazione.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807914"
---
# <a name="auto-startup-lab-virtual-machines"></a>Macchine virtuali del lab di avvio automatico  
Azure DevTest Labs consente di configurare le macchine virtuali nel lab per l'avvio e l'arresto automatici in base a una pianificazione. Per informazioni sulla configurazione delle impostazioni di arresto automatico, vedere Gestire i criteri di [arresto automatico per un lab in Azure DevTest Labs.](devtest-lab-auto-shutdown.md) 

A differenza dell'arresto automatico, in cui tutte le macchine virtuali sono incluse quando il criterio è attivato, il criterio di avvio automatico richiede che un utente del lab selezioni in modo esplicito una macchina virtuale e acconsenti esplicitamente a questa pianificazione. In questo modo, non si incorrerà facilmente nella situazione in cui le macchine virtuali indesiderate vengono avviate automaticamente e causano spese impreviste.

Questo articolo illustra come configurare i criteri di avvio automatico per un lab.

## <a name="configure-autostart-settings-for-a-lab"></a>Configurare le impostazioni di avvio automatico per un labConfigure autostart settings for a lab 
1. Passare alla home page del lab. 
2. Selezionare **Configurazione e criteri** nel menu a sinistra. 

    ![Menu Configurazione e criteri](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Nella pagina **Configurazione e criteri** eseguire le operazioni seguenti:On the Configuration and policies page, do the following steps:
    
    1. Selezionare **Attivato** per **Consenti pianificazione alle macchine virtuali per l'avvio automatico** per abilitare la funzionalità di avvio automatico per questo lab. 
    2. Selezionare un'ora di inizio (ad esempio: 8:00:00 AM) per il campo **Inizio programmazione.** 
    3. Selezionare un **fuso orario** da utilizzare. 
    4. Selezionare i **giorni della settimana** in cui le macchine virtuali devono essere avviate automaticamente. 
    5. Quindi, selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. 

        ![Impostazioni di avvio automatico](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Questo criterio non applica automaticamente l'avvio automatico alle macchine virtuali nel lab. Per **acconsentire esplicitamente** alle singole macchine virtuali, passare alla pagina della macchina virtuale e abilitare l'avvio **automatico** per la macchina virtuale.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Abilitare l'avvio automatico per una macchina virtuale nel labEnable autostart for a VM in the lab
La procedura seguente illustra i passaggi per l'attivazione di una macchina virtuale nei criteri di avvio automatico del lab. 

1. Nella home page del lab selezionare la **macchina virtuale** nell'elenco Macchine **virtuali personali.** 

    ![Menu Configurazione e criteri](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Nella pagina **Macchina virtuale** selezionare **Avvio automatico** nel menu a sinistra o nell'elenco **Pianificazioni.** 

    ![Selezionare il menu di avvio automatico](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Nella pagina **Avvio automatico** selezionare **Attivato** per l'opzione Consenti pianificazione **per avvio automatico della macchina virtuale.**

    ![Abilitare l'avvio automatico per la macchina virtualeEnable autostart for the VM](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Quindi, selezionare **Salva** sulla barra degli strumenti per salvare l'impostazione. 


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui criteri di arresto automatico della configurazione per un lab, vedere Gestire i criteri di arresto automatico per un lab in Azure DevTest LabsTo learn about configuration autoshutdown policy for a lab, see [Manage autoshutdown policies for a lab in Azure DevTest Labs](devtest-lab-auto-shutdown.md)
