---
title: Configurare le impostazioni di avvio automatico per una macchina virtuale in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come configurare le impostazioni di avvio automatico per le macchine virtuali in un Lab. Questa impostazione consente di avviare automaticamente le macchine virtuali nel Lab in base a una pianificazione.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 83e7b0836273a59eaaf66471bd0cb42d63ccf1c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328481"
---
# <a name="auto-startup-lab-virtual-machines"></a>Macchine virtuali del Lab di avvio automatico  
Azure DevTest Labs consente di configurare le macchine virtuali nel Lab in modo che vengano avviate e arrestate automaticamente in base a una pianificazione. Per informazioni sulla configurazione delle impostazioni di arresto automatico, vedere [gestire i criteri di arresto automatico per un Lab in Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

A differenza di AutoShutdown, in cui tutte le macchine virtuali sono incluse quando il criterio è attivato, il criterio di avvio automatico richiede a un utente del Lab di selezionare in modo esplicito una macchina virtuale e acconsentire esplicitamente a questa pianificazione. In questo modo, non si verificherà facilmente la situazione in cui le macchine virtuali indesiderate vengono accidentalmente avviate automaticamente e si verificano imprevisti.

Questo articolo illustra come configurare i criteri di avvio automatico per un Lab.

## <a name="configure-autostart-settings-for-a-lab"></a>Configurare le impostazioni di avvio automatico per un Lab 
1. Passare alla home page per il Lab. 
2. Selezionare **configurazione e criteri** dal menu a sinistra. 

    ![Screenshot che mostra il menu "configurazione e criteri" in DevTest Lab.](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Nella pagina **configurazione e criteri** seguire questa procedura:
    
    1. Selezionare **on** per **consentire alle macchine virtuali di essere pianificate per l'avvio automatico** per abilitare la funzionalità di avvio automatico per questo Lab. 
    2. Selezionare un'ora di inizio (ad esempio: 8:00:00 AM) per il campo di **avvio della pianificazione** . 
    3. Selezionare un **fuso orario** da usare. 
    4. Selezionare **i giorni della settimana** in cui devono essere avviate automaticamente le macchine virtuali. 
    5. Quindi, selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni. 

        ![Impostazioni di avvio automatico](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Questo criterio non applica automaticamente l'avvio automatico alle macchine virtuali nel Lab. Per **scegliere** le singole macchine virtuali, passare alla pagina della macchina virtuale e abilitare l' **avvio automatico** per tale macchina virtuale.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Abilitare l'avvio automatico per una macchina virtuale nel Lab
La procedura seguente illustra i passaggi per la scelta di una macchina virtuale nei criteri di avvio automatico del Lab. 

1. Nella home page per il Lab selezionare la **VM** nell'elenco **macchine virtuali personali** . 

    ![Menu configurazione e criteri](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Nella pagina **macchina virtuale** selezionare **avvio automatico** dal menu a sinistra o nell'elenco **pianificazioni** . 

    ![Selezionare il menu avvio automatico](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Nella pagina **avvio** automatico selezionare **on** per l'opzione **Consenti la pianificazione della macchina virtuale per l'avvio automatico** .

    ![Abilitare l'avvio automatico per la macchina virtuale](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Quindi, selezionare **Salva** sulla barra degli strumenti per salvare l'impostazione. 


## <a name="next-steps"></a>Passaggi successivi
Per informazioni sui criteri di arresto automatico della configurazione per un Lab, vedere [gestire i criteri di arresto automatico per un Lab in Azure DevTest Labs](devtest-lab-auto-shutdown.md)
