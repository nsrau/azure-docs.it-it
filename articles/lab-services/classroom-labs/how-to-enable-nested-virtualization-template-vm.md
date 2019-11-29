---
title: Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services | Microsoft Docs
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
ms.openlocfilehash: 64097a5b3b62bcd5a84f4472a844bb95cf24cd6f
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555078"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Abilitare la virtualizzazione annidata in una macchina virtuale modello in Azure Lab Services

Attualmente, Azure Lab Services consente di configurare una macchina virtuale modello in un Lab e di rendere disponibile una singola copia per ogni utente. Un professore che insegna la rete, la sicurezza o le classi IT potrebbe dover fornire a ognuno degli studenti un ambiente in cui più macchine virtuali possono comunicare tra loro in una rete.

La virtualizzazione nidificata consente di creare un ambiente con più VM all'interno della macchina virtuale modello di un Lab. La pubblicazione del modello fornirà a ogni utente del Lab una macchina virtuale configurata con più macchine virtuali al suo interno.  Questo articolo illustra come configurare la virtualizzazione annidata in un computer modello in Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Che cos'è la virtualizzazione nidificata?

La virtualizzazione nidificata consente di creare macchine virtuali all'interno di una macchina virtuale. La virtualizzazione nidificata viene eseguita tramite Hyper-V ed è disponibile solo nelle macchine virtuali Windows.

Per ulteriori informazioni sulla virtualizzazione nidificata, vedere gli articoli seguenti:

- [Virtualizzazione annidata in Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Come abilitare la virtualizzazione annidata in una macchina virtuale di Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considerazioni

Prima di configurare un Lab con la virtualizzazione nidificata, ecco alcuni aspetti da prendere in considerazione.

- Quando si crea un nuovo Lab, selezionare dimensioni **medie (virtualizzazione annidata)** o **grandi (virtualizzazione nidificata)** per le dimensioni della macchina virtuale. Queste dimensioni della macchina virtuale supportano la virtualizzazione nidificata.
- Scegliere le dimensioni che forniranno prestazioni ottimali per le macchine virtuali host e client.  Tenere presente che quando si usa la virtualizzazione, le dimensioni scelte devono essere adeguate solo per un computer, ma anche per l'host e per tutti i computer client che devono essere eseguiti contemporaneamente.
- Le macchine virtuali client non avranno accesso alle risorse di Azure, ad esempio i server DNS nella rete virtuale di Azure.
- Per la macchina virtuale host è necessaria la configurazione per consentire al computer client di disporre della connettività Internet.
- Le macchine virtuali client sono concesse in licenza come computer indipendenti. Per informazioni sulle licenze per i sistemi operativi e i prodotti Microsoft, vedere [Microsoft Licensing](https://www.microsoft.com/licensing/default) . Controllare i contratti di licenza per qualsiasi altro software usato prima di configurare il computer modello.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Abilitare la virtualizzazione annidata in una VM modello

Questo articolo presuppone che siano stati creati un account Lab e un Lab.  Per ulteriori informazioni sulla creazione di un nuovo account Lab, vedere [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md). Per altre informazioni su come creare un Lab, vedere la pagina relativa alla [configurazione di un'esercitazione in aula Lab](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Selezionare **grande (virtualizzazione annidata)** o **media (virtualizzazione annidata)** per le dimensioni della macchina virtuale durante la creazione del Lab.  La virtualizzazione nidificata non funzionerà in caso contrario.  

Per connettersi al computer modello, vedere [creare e gestire un modello di classe](how-to-create-manage-template.md). 

I passaggi descritti in questa sezione sono incentrati sulla configurazione della virtualizzazione annidata per Windows Server 2016 o Windows Server 2019. Si userà uno script per configurare il computer modello con Hyper-V.  Nei passaggi seguenti viene illustrato come utilizzare gli [script Hyper-V di Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

1. Se si utilizza Internet Explorer, potrebbe essere necessario aggiungere `https://github.com` all'elenco siti attendibili.
    1. Aprire Internet Explorer.
    1. Selezionare l'icona a forma di ingranaggio e scegliere **Opzioni Internet**.  
    1. Quando viene visualizzata la finestra di dialogo **Opzioni Internet** , selezionare **sicurezza**, **siti attendibili**, quindi fare clic sul pulsante **siti** .
    1. Quando viene visualizzata la finestra di dialogo **siti attendibili** , aggiungere `https://github.com` all'elenco siti Web attendibili e selezionare **Chiudi**.

        ![Siti attendibili](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1. Scaricare i file del repository Git come descritto nei passaggi seguenti.
    1. Passare a [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Fare clic sul pulsante **clona o Scarica** .
    1. Fare clic su **download zip**.
    1. Estrarre il file ZIP

    >[!TIP]
    >È anche possibile clonare il repository git in [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git).

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
