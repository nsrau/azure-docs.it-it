---
title: Gestire i lab per le classi in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare e configurare un lab per le classi, visualizzare tutti i lab per le classi, condividere il collegamento di registrazione con un utente del lab o eliminare un lab.
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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 6ba41132c93ebdb2578bafb100416ca3fe579298
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123289"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gestire i lab per le classi in Azure Lab Services 
Questo articolo descrive come creare ed eliminare un lab per le classi, nonché come visualizzare tutti i lab per le classi in un account lab. 

## <a name="prerequisites"></a>Prerequisiti
Per configurare un lab per le classi in un account del lab, è necessario essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare un account del lab viene aggiunto automaticamente a questo ruolo. Un proprietario del lab può aggiungere altri utenti al ruolo Autore di laboratori seguendo i passaggi descritti nell'articolo seguente: [Aggiungere un utente al ruolo di creatore di lab](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
2. Fare clic su **Accedi**. Selezionare o immettere un **ID utente** appartenente al ruolo **Autore di laboratori** nell'account lab e quindi immettere la password. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab. 
    2. Specificare il **numero massimo di macchine virtuali** nel lab. È possibile aumentare o ridurre il numero di macchine virtuali nel lab in un secondo momento. 
    6. Selezionare **Salva**.

        ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Nella pagina **Select virtual machine specifications** (Seleziona le specifiche delle macchine virtuali) eseguire le operazioni seguenti:
    1. Selezionare le **dimensioni** per le macchine virtuali create nel lab. Attualmente **piccole**, **medium**, **medium (virtualizzazione)** , **grandi**, e **GPU** sono dimensioni è consentito. Per informazioni dettagliate, vedere la [dimensioni delle macchine Virtuali](#vm-sizes) sezione.
    1. Selezionare l'**area** in cui si vogliono creare le macchine virtuali. 
    1. Selezionare l'**immagine della macchina virtuale** da usare per creare macchine virtuali nel lab. Se si seleziona un'immagine di Linux, viene visualizzata un'opzione per abilitare la connessione Desktop remoto. Per informazioni, vedere [Abilitare Connessione desktop remoto per Linux](how-to-enable-remote-desktop-linux.md).
    1. Selezionare **Avanti**.

        ![Immettere le specifiche delle macchine virtuali](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Nella pagina **Set credentials** (Imposta le credenziali) specificare le credenziali predefinite per tutte le macchine virtuali del lab. 
    1. Specificare il **nome dell'utente** per tutte le macchine virtuali del lab.
    2. Specificare la **password** dell'utente. 

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Disabilitare **Usa la stessa password per tutte le macchine virtuali** opzione se si desidera che gli studenti per impostare le proprie password. Questo passaggio è **facoltativo**. 

        Un insegnante può scegliere di usare la stessa password per tutte le macchine virtuali nel lab oppure consentire agli studenti di impostare le password per le proprie macchine virtuali. Per impostazione predefinita, questa impostazione è abilitata per le immagini di tutti i Windows e Linux, ad eccezione di Ubuntu. Quando si seleziona **Ubuntu** macchina virtuale, questa impostazione è disabilitata, in modo che gli studenti verranno richiesto di impostare una password quando accedono per la prima volta.
    1. Selezionare **Create**. 

        ![Impostare le credenziali](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Nella pagina **Configure template** (Configura modello) viene visualizzato lo stato del processo di creazione del lab. La creazione del modello nel lab richiede fino a 20 minuti. Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello con tutte le caratteristiche che si vuole fornire agli utenti del lab.  

    ![Configurare il modello](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Dopo aver completato la configurazione del modello verrà visualizzata la pagina seguente: 

    ![Pagina di configurazione del modello al termine dell'operazione](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. I passaggi seguenti sono facoltativi in questa esercitazione: 
    2. Connettersi alla macchina virtuale modello selezionando **Connect** (Connetti). Se si tratta di una macchina virtuale modello di Linux, scegliere se si vuole effettuare la connessione con SSH o RDP (se RDP è abilitato).
    1. Selezionare **la reimpostazione della password** per reimpostare la password per la macchina virtuale. 
    1. Installare e configurare il software nella macchina virtuale modello. 
    1. **Arrestare** la VM.  
    1. Immettere una **descrizione** per il modello.
9. Selezionare **Next** (Avanti) nella pagina del modello. 
10. Nella pagina **Publish the template** (Pubblica il modello) eseguire le azioni seguenti. 
    1. Per pubblicare immediatamente il modello, selezionare la casella di controllo *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Sono consapevole che il modello non sarà modificabile dopo la pubblicazione. Questo processo può essere eseguito una sola volta e richiede fino a un'ora), quindi selezionare **Publish** (Pubblica).  Pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello.

        > [!WARNING]
        > L'operazione di pubblicazione non può essere annullata. 
    2. Per eseguire la pubblicazione in un secondo momento, selezionare **Save for later** (Salva per dopo). È possibile pubblicare la macchina virtuale modello al termine della procedura guidata. Per informazioni dettagliate su come eseguire la configurazione e la pubblicazione al termine della procedura guidata, consultare la sezione Pubblicare il modello dell'articolo [Gestire i lab per le classi](how-to-manage-classroom-labs.md).

        ![Pubblicare il modello](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Al termine dell'operazione di pubblicazione del modello, viene visualizzata la pagina seguente. Selezionare **Operazione completata**.

    ![Pubblicazione del modello - operazione completata](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verrà visualizzato il **dashboard** del lab. 
    
    ![Dashboard del lab per le classi](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Passare alla pagina **Macchine virtuali** e verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>Dimensioni delle macchine virtuali  

| Dimensione | Core | RAM | Descrizione | 
| ---- | ----- | --- | ----------- | 
| Small | 2 | 3,5 GB | Queste dimensioni sono ideale per la riga di comando, aprire web browser, server web con traffico ridotto, piccoli database medio. |
| Media | 4 | 7 GB | Queste dimensioni sono ideale per i database relazionali, la memorizzazione nella cache in memoria e analitica | 
| Medium (virtualizzazione nidificata) | 4 | 16 GB | Queste dimensioni sono ideali per i database relazionali, la memorizzazione nella cache in memoria e analitica. Queste dimensioni supporta anche la virtualizzazione annidata. <p>Queste dimensioni sono utilizzabile negli scenari in cui ogni studente necessarie più macchine virtuali. Gli insegnanti possono usare la virtualizzazione annidata per configurare alcune macchine virtuali di dimensioni ridotte annidate all'interno della macchina virtuale. </p> |
| Large | 8 | 32 GB | Queste dimensioni sono ideali per applicazioni che richiedono CPU più veloci, prestazioni migliori per i dischi locali, database di grandi dimensioni, le cache di grandi quantità di memoria. Queste dimensioni supporta anche la virtualizzazione annidata |  
| GPU | 12 | 112 GB | Queste dimensioni sono ideali per carichi di lavoro a elevato utilizzo di calcolo a elevato utilizzo di grafica e visualizzazione | 

## <a name="view-all-classroom-labs"></a>Visualizzare tutti i lab per le classi
1. Accedere al [portale di Azure Lab Services](https://labs.azure.com).
2. Fare clic su **Accedi**. Selezionare o immettere un **ID utente** appartenente al ruolo **Autore di laboratori** nell'account lab e quindi immettere la password. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Confermare che sia possibile visualizzare tutti i lab dell'account del lab selezionato. 

    ![Tutti i lab](../media/how-to-manage-classroom-labs/all-labs.png)
3. Dall'elenco a discesa nella parte superiore, selezionare un account del lab differente. Tutti i lab dell'account selezionato sono visibili. 

## <a name="delete-a-classroom-lab"></a>Eliminare un lab per le classi
1. Nel riquadro del lab, selezionare l'icona con tre punti (...) nell'angolo. 

    ![Selezionare il lab](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Selezionare **Elimina**. 

    ![Pulsante Elimina](../media/how-to-manage-classroom-labs/delete-button.png)
3. Nella finestra di dialogo **Delete lab**(Elimina lab), selezionare **Elimina**. 

    ![Finestra di dialogo Elimina](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Passare a un altro lab per le classi
Per passare a un altro lab per le classi, in quello corrente selezionare l'elenco a discesa di lab nell'account del lab nella parte superiore.

![Selezionare il lab dall'elenco a discesa nella parte superiore](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)

