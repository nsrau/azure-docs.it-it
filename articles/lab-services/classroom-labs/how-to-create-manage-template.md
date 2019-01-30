---
title: Gestire un modello di un lab per le classi in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare e gestire un modello di un lab per le classi in Azure Lab Services.
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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 2f34b6c71c448f7273ba7477f18f5abb8f89cdec
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391192"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Creare e gestire un modello per le classi in Azure Lab Services
Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello con tutte le caratteristiche che si vuole fornire agli utenti del lab. È possibile specificare un nome e una descrizione del modello che verranno visualizzati dagli utenti del lab. Quindi, pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello. Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello.

Questo articolo descrive come creare e gestire un modello di macchina virtuale in un lab per le classi di Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Pubblicare un modello durante la creazione di un lab per le classi
In primo luogo, è possibile configurare e pubblicare un modello durante la creazione di un lab per le classi.

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). 
2. Selezionare **Accedi** e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab. 
    2. Specificare il **numero di utenti** massimo consentito nel lab. 
    6. Selezionare **Salva**.

        ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Nella pagina **Select virtual machine specifications** (Seleziona le specifiche delle macchine virtuali) eseguire le operazioni seguenti:
    1. Selezionare le **dimensioni** per le macchine virtuali create nel lab. 
    2. Selezionare l'**area** in cui si vogliono creare le macchine virtuali. 
    3. Selezionare l'**immagine della macchina virtuale** da usare per creare macchine virtuali nel lab. 
    4. Selezionare **Avanti**.

        ![Immettere le specifiche delle macchine virtuali](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Nella pagina **Set credentials** (Imposta le credenziali) specificare le credenziali predefinite per tutte le macchine virtuali del lab. 
    1. Specificare il **nome dell'utente** per tutte le macchine virtuali del lab.
    2. Specificare la **password** dell'utente. 

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Selezionare **Create**. 

        ![Impostare le credenziali](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Nella pagina **Configure template** (Configura modello) viene visualizzato lo stato del processo di creazione del lab. La creazione del modello nel lab richiede fino a 20 minuti. 

    ![Configurare il modello](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Dopo aver completato la configurazione del modello verrà visualizzata la pagina seguente: 

    ![Pagina di configurazione del modello al termine dell'operazione](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. I passaggi seguenti sono facoltativi in questa esercitazione: 
    1. Avviare la macchina virtuale modello selezionando **Start** (Avvia).
    2. Connettersi alla macchina virtuale modello selezionando **Connect** (Connetti). 
    3. Installare e configurare il software nella macchina virtuale modello. 
    4. **Arrestare** la VM.  
    5. Immettere una **descrizione** per il modello.

        ![Pulsante Next (Avanti) nella pagina di configurazione del modello](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Selezionare **Next** (Avanti) nella pagina del modello. 
10. Nella pagina **Publish the template** (Pubblica il modello) eseguire le azioni seguenti. 
    1. Per pubblicare immediatamente il modello, selezionare la casella di controllo *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Sono consapevole che il modello non sarà modificabile dopo la pubblicazione. Questo processo può essere eseguito una sola volta e richiede fino a un'ora), quindi selezionare **Publish** (Pubblica).  

        > [!WARNING]
        > L'operazione di pubblicazione non può essere annullata. 
    2. Per eseguire la pubblicazione in un secondo momento, selezionare **Save for later** (Salva per dopo). È possibile pubblicare la macchina virtuale modello al termine della procedura guidata. Per informazioni dettagliate su come eseguire la configurazione e la pubblicazione al termine della procedura guidata, vedere la sezione [Pubblicare il modello](#publish-the-template) nell'articolo [Gestire i lab per le classi](how-to-manage-classroom-labs.md).

        ![Pubblicare il modello](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Al termine dell'operazione di pubblicazione del modello, viene visualizzata la pagina seguente. Selezionare **Operazione completata**.

    ![Pubblicazione del modello - operazione completata](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verrà visualizzato il **dashboard** del lab. 
    
    ![Dashboard del lab per le classi](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

 
## <a name="set-or-update-template-title-and-description"></a>Impostare o aggiornare il titolo e la descrizione del modello
Seguire questa procedura per impostare il titolo e la descrizione per la prima volta e aggiornarli in un secondo momento. 

1. Nella sezione **Modello** spostare il puntatore del mouse sul **nome** o sulla **descrizione** del modello e selezionarlo. 
2. Immettere il **nuovo nome** o la **nuova descrizione** del modello e premere **INVIO**.

    ![Nome e descrizione del modello](../media/how-to-create-manage-template/template-name-description.png)

## <a name="set-up-or-update-a-template-vm"></a>Configurare o aggiornare una macchina virtuale modello
 Connettersi alla macchina virtuale modello e installarvi tutto il software necessario prima di renderla disponibile agli studenti. Usare questa procedura per configurare una macchina virtuale modello per la prima volta o aggiornare la macchina virtuale. 

1. Attendere che la macchina virtuale modello sia pronta. Una volta pronta, viene abilitato il pulsante **Avvia**. Per avviare la macchina virtuale, selezionare **Avvia**.

    ![Avviare la macchina virtuale modello](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Esaminare l'avviso e selezionare **Avvia**. 

    ![Avvio modello - Avviso](../media/how-to-create-manage-template/start-template-warning.png)
1. Dopo l'avvio, per connettersi alla macchina virtuale, selezionare **Connetti** e seguire le istruzioni. 
1. Installare tutto il software necessario agli studenti per eseguire il lab, ad esempio, Visual Studio, Azure Storage Explorer e così via. 
2. Disconnettersi dalla macchina virtuale modello, ovvero chiudere la sessione di accesso remota. 
3. **Arrestare** la macchina virtuale modello selezionando **Arresta**. 

## <a name="publish-the-template-vm"></a>Pubblicare la macchina virtuale modello  
Se si non pubblica il modello durante la creazione del lab, è possibile pubblicarlo in un secondo momento. Prima della pubblicazione, è possibile connettersi al modello di macchina virtuale e aggiornarlo con qualsiasi software. Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello. 

1. Selezionare **Pubblica** nella sezione **Modello**. 

    ![Pubblicare la macchina virtuale modello](../media/tutorial-setup-classroom-lab/public-access.png)
1. Nella finestra di messaggio **Pubblicare il modello** esaminare il messaggio e selezionare **Pubblica**. Questo processo può richiedere tempo a seconda del numero di macchine virtuali che vengono create.
    
    > [!IMPORTANT]
    > Non è possibile annullare la pubblicazione di un modello, dopo che questo è stato pubblicato. Tuttavia, è possibile ripubblicare il modello. 
4. Attendere che lo stato del modello si imposti su **Pubblicato**. 

    ![Stato della pubblicazione](../media/how-to-create-manage-template/publish-status.png)
1. Passare alla pagina **Macchine virtuali** e verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Attendere finché le macchine virtuali non vengono create. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>Ripubblicare il modello 
Dopo aver pubblicato un modello, è comunque possibile connettersi al modello di macchina virtuale, eseguirne l'aggiornamento e quindi ripubblicarla. Quando si ripubblica un modello di macchina virtuale, tutte le macchine virtuali degli utenti vengono disconnesse e vengono ricreate in base al modello aggiornato. 

1. Nella pagina della dashboard del lab selezionare **Ripubblicare** nella sezione Modello. 

    ![Pulsante Ripubblicare](../media/how-to-create-manage-template/republish-button.png)
2. Nella finestra di messaggio **Ripubblicare il modello** esaminare il testo e selezionare **Ripubblicare** per continuare. In caso contrario, selezionare **Annulla**. 

    ![Messaggio Ripubblicare il modello](../media/how-to-create-manage-template/republish-template-message.png)
3. Visualizzare lo stato della ripubblicazione nel riquadro della sezione **Modello**.

    ![Stato di ripubblicazione](../media/how-to-create-manage-template/republish-status-publishing.png)
4. Dopo aver pubblicato il modello, lo stato è impostato su **Pubblicato**. 

    ![Ripubblicazione riuscita](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
