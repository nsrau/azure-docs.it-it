---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services | Microsoft Docs
description: Informazioni su come abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services.
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
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981995"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services
Questo articolo illustra come configurare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services. La virtualizzazione nidificata viene utilizzata in Azure Lab Services quando ogni studente di una classe richiede più computer.
 
## <a name="considerations"></a>Considerazioni
Prima di configurare un Lab con la virtualizzazione nidificata, ecco alcuni aspetti da prendere in considerazione.

- Quando si crea un nuovo Lab, selezionare **media (virtualizzazione annidata)** o **grandi** dimensioni per le dimensioni della macchina virtuale. Queste dimensioni della macchina virtuale supportano la virtualizzazione nidificata. 
- Scegliere le dimensioni che forniranno prestazioni ottimali per le macchine virtuali host e client.  Tenere presente che quando si usa la virtualizzazione, le dimensioni scelte devono essere adeguate solo per un computer, ma anche per l'host e per tutti i computer client che devono essere eseguiti contemporaneamente.
- Le macchine virtuali client non avranno accesso alle risorse di Azure, ad esempio i server DNS nella rete virtuale di Azure.
- Per la macchina virtuale host è necessaria la configurazione per consentire al computer client di disporre della connettività Internet. 
- Le macchine virtuali client sono concesse in licenza come computer indipendenti. Per informazioni sulle licenze per i sistemi operativi e i prodotti Microsoft, vedere [Microsoft Licensing](https://www.microsoft.com/licensing/default) . Controllare i contratti di licenza per qualsiasi altro software usato prima di configurare il computer modello.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello
I passaggi descritti in questa sezione sono incentrati sulla configurazione della virtualizzazione annidata per Windows Server 2016 o Windows Server 2019. Si userà uno script per configurare il computer modello con Hyper-V. Per una soluzione automatizzata, vedere script negli [script Hyper-V di Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV). Nei passaggi seguenti viene illustrato come usare lo script.

1. Se si utilizza Internet Explorer, potrebbe essere necessario aggiungere `https://github.com` all'elenco siti attendibili. 
    1. Aprire Internet Explorer.
    1. Selezionare l'icona a forma di ingranaggio e scegliere **Opzioni Internet**.  
    1. Quando viene visualizzata la finestra di dialogo **Opzioni Internet** , selezionare **sicurezza**, **siti attendibili**, quindi fare clic sul pulsante **siti** .
    1. Quando viene visualizzata la finestra di dialogo **siti attendibili** , aggiungere `https://github.com` all'elenco siti Web attendibili e selezionare **Chiudi**.

        ![Siti attendibili](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  Scaricare i file del repository Git come descritto nei passaggi seguenti.  In alternativa, è possibile clonare il repository git da [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git). 
    1. Passare a [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Fare clic sul pulsante ***clona o Scarica** .
    1. Fare clic su **download zip**.
    1. Estrarre il file ZIP
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

## <a name="conclusion"></a>Conclusione
A questo punto il computer modello è pronto per la creazione di macchine virtuali Hyper-V. Per istruzioni su come creare macchine virtuali Hyper-V, vedere [creare una macchina virtuale in Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) . Vedere anche [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) per consultare i sistemi operativi e il software disponibili.  

## <a name="next-steps"></a>Passaggi successivi 
Vedere gli articoli seguenti:

- [Come abilitare la virtualizzazione annidata in una macchina virtuale di Azure](../../virtual-machines/windows/nested-virtualization.md) 
- [Installare il ruolo Hyper-V in Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Script di Hyper-V in Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
