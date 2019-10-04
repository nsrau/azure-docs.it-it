---
title: Come accedere a un lab per le classi in Azure Lab Services | Microsoft Docs
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
ms.date: 09/19/2019
ms.author: spelluru
ms.openlocfilehash: 10813600621293e9f559129230193f81ea0c505a
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161524"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Come accedere a un lab per le classi in Azure Lab Services
Questo articolo descrive come registrarsi a un lab per la classe, visualizzare tutti i lab a cui è possibile accedere, avviare e arrestare una macchina virtuale nel lab e connettersi alla macchina virtuale. 

## <a name="register-to-the-lab"></a>Registrarsi al lab

1. Passare all'**URL di registrazione** ricevuto dal docente. Non è necessario usare l'URL di registrazione dopo aver completato la registrazione. Usare invece l'URL [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
1. Accedere al servizio usando l'account dell'istituto di istruzione per completare la registrazione. 

    > [!NOTE]
    > Per l'uso di Azure Lab Services, è necessario un account Microsoft. Se si prova a usare un account non Microsoft, ad esempio Yahoo o Google, per accedere al portale, seguire le istruzioni per creare un account Microsoft che verrà collegato all'account non Microsoft. Quindi, seguire la procedura per completare il processo di registrazione. 
1. Al termine della registrazione, verificare che le macchine virtuali per i lab a cui si ha accesso siano disponibili. 
1. Attendere che la macchina virtuale sia pronta. Nel riquadro della macchina virtuale osservare i campi seguenti:
    1. Nella parte superiore del riquadro viene visualizzato il **nome del lab**.
    1. A destra viene visualizzata l'icona che rappresenta il **sistema operativo** della macchina virtuale. In questo esempio si tratta di Windows. 
    1. Nella parte inferiore del riquadro vengono visualizzati i pulsanti/icone per avviare e arrestare la macchina virtuale, nonché per connettersi alla macchina virtuale. 
    1. A destra dei pulsanti viene visualizzato lo stato della macchina virtuale. Verificare che lo stato della macchina virtuale sia **Arrestato**.

        ![Macchina virtuale nello stato Arrestato](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Avviare o arrestare la macchina virtuale
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
    1. Se si usa un **Mac** per connettersi alla macchina virtuale lab, seguire le istruzioni riportate nella sezione successiva. 

## <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Connettersi a una macchina virtuale usando RDP in un Mac
Questa sezione illustra come uno studente può connettersi a una macchina virtuale da un Mac usando RDP.

### <a name="step-1-install-microsoft-remote-desktop-on-a-mac"></a>Passaggio 1: Installare Desktop remoto Microsoft in un Mac
1. Aprire l'App Store nel Mac e cercare **Desktop remoto Microsoft.**

    ![Desktop remoto Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installare la versione più recente di Desktop remoto Microsoft. 

### <a name="step-2-access-the-vm-from-your-mac-using-rdp"></a>Passaggio 2: Accedere alla macchina virtuale dal Mac usando RDP
1. Aprire il file **RDP** scaricato nel computer in cui è installato **Desktop remoto Microsoft**. Dovrebbe iniziare a connettersi alla macchina virtuale. 

    ![Connettersi alla macchina virtuale](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Selezionare **Continua** se viene visualizzato il seguente avviso. 

    ![Avviso del certificato](../media/how-to-use-classroom-lab/certificate-error.png)
1. Verrà visualizzata la macchina virtuale. 

    > [!NOTE]
    > L'esempio seguente è relativo a una VM Linux CentOS. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)

## <a name="progress-bar"></a>Indicatore di stato 
L'indicatore di stato nel riquadro indica il numero di ore usate rispetto al numero di [ore della quota](how-to-configure-student-usage.md#set-quotas-for-users) assegnate all'utente. Questo intervallo di tempo è il tempo aggiuntivo assegnato all'utente oltre al tempo pianificato per il lab. Il colore dell'indicatore di stato e il testo sotto di esso variano in base agli scenari seguenti:

- Se è in corso una classe (nell'intervallo di tempo pianificato per la classe), l'indicatore di stato viene visualizzato in grigio per indicare che le ore della quota non vengono usate. 

    ![Indicatore di stato di colore grigio](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Se non è assegnata una quota (zero ore), viene visualizzato il testo **Available during classes only** (Disponibile solo durante le classi) al posto dell'indicatore di stato. 
    
    ![Stato quando non è impostata alcuna quota](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Se la **quota è esaurita**, il colore dell'indicatore di stato è **rosso**. 

    ![Indicatore di stato di colore rosso](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Il colore dell'indicatore di stato è **blu** quando il tempo pianificato per il lab è terminato ed è stata usata una parte di ore della quota. 

    ![Indicatore di stato di colore blu](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Visualizzare tutti i lab della classe
Dopo la registrazione ai lab, è possibile visualizzare tutti i lab per le classi attenendosi alla procedura seguente: 

1. Passare a [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
2. Accedere al servizio con l'account utente usato per la registrazione al lab. 
3. Verificare che siano visualizzati tutti i lab a cui si ha accesso. 

    ![Visualizzare tutti i lab](../media/how-to-use-classroom-lab/all-labs.png)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
 