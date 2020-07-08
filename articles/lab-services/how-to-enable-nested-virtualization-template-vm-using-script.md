---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services (script) | Microsoft Docs
description: Informazioni su come creare una macchina virtuale modello contenente più macchine virtuali.  In altre parole, abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a45257dffffc98c3f650c5b1753158edd9bfbede
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445747"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services usando uno script

La virtualizzazione annidata consente di creare un ambiente con più macchine virtuali all'interno della macchina virtuale modello di un lab. La pubblicazione del modello fornirà a ogni utente del lab una macchina virtuale configurata con più macchine virtuali al suo interno.  Per ulteriori informazioni sulla virtualizzazione annidata e Azure Lab Services, vedere [abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

La procedura descritta in questo articolo è incentrata sull'impostazione della virtualizzazione annidata per Windows Server 2016, Windows Server 2019 o Windows 10. Si userà uno script per configurare il computer modello con Hyper-V.  Nei passaggi seguenti viene illustrato come utilizzare gli [script Hyper-V di Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Per la creazione del lab, selezionare le dimensioni **Medium (virtualizzazione annidata)** o **Large (virtualizzazione annidata)** per la macchina virtuale.  In caso contrario, la virtualizzazione annidata non funzionerà.  

## <a name="run-script"></a>Esegui script

1. Se si utilizza Internet Explorer, potrebbe essere necessario aggiungere `https://github.com` all'elenco siti attendibili.
    1. Aprire Internet Explorer.
    1. Selezionare l'icona a forma di ingranaggio e scegliere **Opzioni Internet**.  
    1. Quando viene visualizzata la finestra di dialogo **Opzioni Internet** , selezionare **sicurezza**, **siti attendibili**, quindi fare clic sul pulsante **siti** .
    1. Quando viene visualizzata la finestra di dialogo **siti attendibili** , aggiungere `https://github.com` all'elenco siti Web attendibili e selezionare **Chiudi**.

        ![Siti attendibili](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Scaricare i file del repository Git come descritto nei passaggi seguenti.
    1. Passare a [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) .
    1. Fare clic sul pulsante **clona o Scarica** .
    1. Fare clic su **download zip**.
    1. Estrarre il file ZIP

    >[!TIP]
    >È anche possibile clonare il repository git in [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) .

1. Avviare **PowerShell** in modalità **amministratore** .
1. Nella finestra di PowerShell passare alla cartella con lo script scaricato. Se si esegue la navigazione dalla cartella principale dei file del repository, lo script si trova in `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` .
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

- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)