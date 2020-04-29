---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services (script) | Microsoft Docs
description: Informazioni su come creare una VM modello con più macchine virtuali all'interno di.  In altre parole, abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503036"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services usando uno script

La virtualizzazione nidificata consente di creare un ambiente con più VM all'interno della macchina virtuale modello di un Lab. La pubblicazione del modello fornirà a ogni utente del Lab una macchina virtuale configurata con più macchine virtuali al suo interno.  Per ulteriori informazioni sulla virtualizzazione annidata e Azure Lab Services, vedere [abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

La procedura descritta in questo articolo è incentrata sull'impostazione della virtualizzazione annidata per Windows Server 2016 o Windows Server 2019. Si userà uno script per configurare il computer modello con Hyper-V.  Nei passaggi seguenti viene illustrato come utilizzare gli [script Hyper-V di Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Selezionare **grande (virtualizzazione annidata)** o **media (virtualizzazione annidata)** per le dimensioni della macchina virtuale durante la creazione del Lab.  La virtualizzazione nidificata non funzionerà in caso contrario.  

## <a name="run-script"></a>Eseguire lo script

1. Se si utilizza Internet Explorer, potrebbe essere necessario aggiungere `https://github.com` all'elenco siti attendibili.
    1. Aprire Internet Explorer.
    1. Selezionare l'icona a forma di ingranaggio e scegliere **Opzioni Internet**.  
    1. Quando viene visualizzata la finestra di dialogo **Opzioni Internet** , selezionare **sicurezza**, **siti attendibili**, quindi fare clic sul pulsante **siti** .
    1. Quando viene visualizzata la finestra di dialogo **siti attendibili** , aggiungere `https://github.com` all'elenco siti Web attendibili e selezionare **Chiudi**.

        ![Siti attendibili](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Scaricare i file del repository Git come descritto nei passaggi seguenti.
    1. Passare a [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Fare clic sul pulsante **clona o Scarica** .
    1. Fare clic su **download zip**.
    1. Estrarre il file ZIP

    >[!TIP]
    >È anche possibile clonare il repository git [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)in.

1. Avviare **PowerShell** in modalità **amministratore** .
1. Nella finestra di PowerShell passare alla cartella con lo script scaricato. Se si esegue la navigazione dalla cartella principale dei file del repository, lo script si trova in `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Potrebbe essere necessario modificare i criteri di esecuzione per eseguire correttamente lo script. Eseguire il comando seguente:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Eseguire lo script:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > È possibile che lo script richieda il riavvio del computer. Seguire le istruzioni dello script ed eseguire di nuovo lo script fino a quando non viene visualizzato lo **script completato** nell'output.
1. Non dimenticare di reimpostare i criteri di esecuzione. Eseguire il comando seguente:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusioni

A questo punto il computer modello è pronto per la creazione di macchine virtuali Hyper-V. Per istruzioni su come creare macchine virtuali Hyper-V, vedere [creare una macchina virtuale in Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) . Vedere anche [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) per consultare i sistemi operativi e il software disponibili.  

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)