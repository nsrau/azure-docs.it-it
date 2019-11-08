---
title: Accedere a un lab per le classi in Azure Lab Services | Microsoft Docs
description: Questa esercitazione descrive come accedere alle macchine virtuali in un lab per le classi impostato da un docente.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 30983d141f087a46e420f7ea457fba181956c28e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577765"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Esercitazione: Accedere a un lab per le classi in Azure Lab Services
Questa esercitazione descrive come uno studente può connettersi a una macchina virtuale (VM) in un lab per le classi. 

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Registrarsi al lab
> * Avviare la VM
> * Connettersi alla VM

## <a name="register-to-the-lab"></a>Registrarsi al lab

1. Passare all'**URL di registrazione** ricevuto dal docente. Non è necessario usare l'URL di registrazione dopo aver completato la registrazione. Usare invece l'URL [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
1. Accedere al servizio usando l'account dell'istituto di istruzione per completare la registrazione. 

    > [!NOTE]
    > Per l'uso di Azure Lab Services, è necessario un account Microsoft. Se si prova a usare un account non Microsoft, ad esempio Yahoo o Google, per accedere al portale, seguire le istruzioni per creare un account Microsoft che verrà collegato all'account non Microsoft. Quindi, seguire la procedura per completare il processo di registrazione. 
1. Al termine della registrazione, verificare che le macchine virtuali per i lab a cui si ha accesso siano disponibili. 
1. Attendere che la macchina virtuale sia pronta. Nel riquadro della macchina virtuale osservare i campi seguenti:
    1. Nella parte superiore del riquadro viene visualizzato il **nome del lab**.
    1. A destra viene visualizzata l'icona che rappresenta il **sistema operativo** della macchina virtuale. In questo esempio si tratta di Windows. 
    1. L'indicatore di stato nel riquadro indica il numero di ore usate rispetto al numero di [ore della quota](how-to-configure-student-usage.md#set-quotas-for-users) assegnate all'utente. Questo intervallo di tempo è il tempo aggiuntivo assegnato all'utente oltre al tempo pianificato per il lab. 
    1. Nella parte inferiore del riquadro vengono visualizzati i pulsanti/icone per avviare e arrestare la macchina virtuale, nonché per connettersi alla macchina virtuale. 
    1. A destra dei pulsanti viene visualizzato lo stato della macchina virtuale. Verificare che lo stato della macchina virtuale sia **Arrestato**. 

        ![Macchina virtuale nello stato Arrestato](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Avviare la VM
1. **Avviare** la macchina virtuale selezionando il primo pulsante, indicato nell'immagine seguente. L'esecuzione di questo processo richiede tempo.  

    ![Avviare la VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Verificare che lo stato della macchina virtuale sia **In esecuzione**. 

    ![Macchina virtuale nello stato In esecuzione](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Si noti che l'icona del primo pulsante cambia per rappresentare un'operazione di **arresto**. È possibile selezionare questo pulsante per arrestare la macchina virtuale. 

## <a name="connect-to-the-vm"></a>Connettersi alla VM

1. Selezionare il secondo pulsante indicato nell'immagine seguente per **connettersi** alla macchina virtuale del lab. 

    ![Connettersi alla macchina virtuale](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Eseguire uno dei passaggi seguenti: 
    1. Per le macchine virtuali **Windows**, salvare il file **RDP** nel disco rigido. Aprire il file RDP per connettersi alla macchina virtuale. Usare il **nome utente** e la **password** forniti dal docente per accedere alla macchina virtuale. 
    3. Per connettersi alle macchine virtuali **Linux**, è possibile usare **SSH** o **RDP** (se è abilitato). Per altre informazioni, vedere [Abilitare una connessione Desktop remoto per i computer Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come eseguire l'accesso a un lab per le classi usando il collegamento di registrazione ottenuto dal professore/docente.

In quanto proprietario del lab, si vuole vedere chi ha eseguito la registrazione per il lab e tenere traccia dell'utilizzo delle macchine virtuali. Passare all'esercitazione successiva per informazioni su come tenere traccia dell'uso del lab:

> [!div class="nextstepaction"]
> [Tenere traccia dell'utilizzo di un lab](tutorial-track-usage.md) 
