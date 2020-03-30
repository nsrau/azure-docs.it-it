---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services (Script) Documenti Microsoft
description: Informazioni su come creare una macchina virtuale modello con più macchine virtuali all'interno.  In other words, enable nested virtualization on a template VM in Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503036"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services usando uno scriptEnable nested virtualization on a template virtual machine in Azure Lab Services using a script

La virtualizzazione annidata consente di creare un ambiente con più macchine virtuali all'interno della macchina virtuale modello di un lab. La pubblicazione del modello fornirà a ogni utente del lab una macchina virtuale configurata con più macchine virtuali al suo interno.  Per altre informazioni sulla virtualizzazione annidata e sui servizi lab di Azure, vedere [Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services.For](how-to-enable-nested-virtualization-template-vm.md)more information about nested virtualization and Azure Lab Services, see Enable nested virtualization on a template virtual machine in Azure Lab Services.

I passaggi descritti in questo articolo si concentrano sulla configurazione della virtualizzazione annidata per Windows Server 2016 o Windows Server 2019.The steps in this article focus on setting up nested virtualization for Windows Server 2016 or Windows Server 2019. Si utilizzerà uno script per configurare la macchina modello con Hyper-V.  I passaggi seguenti illustrano come utilizzare gli [script Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)di Lab Services.

>[!IMPORTANT]
>Selezionare **Grande (virtualizzazione annidata)** o **Medio (virtualizzazione annidata)** per le dimensioni della macchina virtuale durante la creazione del lab.  La virtualizzazione annidata non funzionerà in caso contrario.  

## <a name="run-script"></a>Eseguire lo script

1. Se si utilizza Internet Explorer, potrebbe `https://github.com` essere necessario aggiungere all'elenco dei siti attendibili.
    1. Aprire Internet Explorer.
    1. Selezionare l'icona a forma di ingranaggio e scegliere **Opzioni Internet**.  
    1. Quando viene visualizzata la finestra di dialogo **Opzioni Internet,** selezionare **Sicurezza**, Selezionare **Siti attendibili**, fare clic sul pulsante **Siti.**
    1. Quando viene visualizzata la `https://github.com` finestra di dialogo Siti **attendibili,** aggiungere all'elenco Siti Web attendibili e selezionare **Chiudi**.

        ![Siti attendibili](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Scaricare i file del repository Git come descritto nei passaggi seguenti.
    1. Andare [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)a .
    1. Fare clic sul pulsante **Clona o Scarica.**
    1. Fare clic su **Scarica CAP**.
    1. Estrarre il file ZIP

    >[!TIP]
    >È inoltre possibile clonare [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)il repository Git all'indirizzo .

1. Avviare **PowerShell** in modalità **amministratore.**
1. Nella finestra di PowerShell passare alla cartella con lo script scaricato. Se ci si sposta dalla cartella superiore dei file del `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`repository, lo script si trova in .
1. Potrebbe essere necessario modificare i criteri di esecuzione per eseguire correttamente lo script. Eseguire il comando seguente:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Eseguire lo script:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Lo script potrebbe richiedere il riavvio del computer. Seguire le istruzioni dello script ed eseguire nuovamente lo script fino a quando **lo script non** viene visualizzato nell'output.
1. Non dimenticare di reimpostare i criteri di esecuzione. Eseguire il comando seguente:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusioni

A questo punto il computer modello è pronto per creare macchine virtuali Hyper-V. Per istruzioni su come creare macchine virtuali Hyper-V, vedere [Creare una macchina virtuale in Hyper-V.See Create a Virtual Machine in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) for instructions su how to create Hyper-V virtual machines. Vedere anche [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) per estrarre i sistemi operativi e il software disponibili.  

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni alla configurazione di qualsiasi lab.

- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Link di registrazione via e-mail agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)