---
title: Configurare un lab per le classi con Azure Lab Services | Microsoft Docs
description: Questa esercitazione descrive come configurare un lab da usare in una classe.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 163763bf1203a045326c7163b5f6da9aa417d8cf
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081857"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Esercitazione: Configurare un lab per le classi 
Questa esercitazione descrive come configurare un lab per le classi con macchine virtuali usate dagli studenti nella classe.  

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un lab per le classi
> * Configurare il lab per le classi
> * Inviare un collegamento di registrazione agli studenti

## <a name="prerequisites"></a>Prerequisiti
Per configurare un lab per le classi in un account del lab, è necessario essere un membro del ruolo **Autore di laboratori** nell'account del lab. Un proprietario del lab può aggiungere un utente al ruolo Autore di laboratori seguendo i passaggi nell'articolo seguente: [Aggiungere un utente al ruolo di Autore di laboratori](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com).
2. Selezionare **Accedi** e immettere le credenziali. 
3. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab per le classi. 
    2. Selezionare le **dimensioni** della macchina virtuale che si intende usare nella classe.
    3. Selezionare l'**immagine** da usare per creare la macchina virtuale.
    4. Specificare le **credenziali predefinite** per la registrazione alle macchine virtuali nel lab. 
    7. Selezionare **Salva**.

        ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Verrà visualizzato il **dashboard** del lab. 
    
    ![Dashboard del lab per le classi](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurare i criteri di utilizzo

1. Selezionare **Usage policy** (Criteri di utilizzo). 
2. Nelle impostazioni di **Usage policy** (Criteri di utilizzo) immettere il **numero di utenti** a cui è consentito usare il lab.
3. Selezionare **Salva**. 

    ![Criteri di utilizzo](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)


## <a name="set-up-the-template"></a>Configurare il modello 
Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello con tutte le caratteristiche che si vuole fornire agli utenti del lab. È possibile specificare un nome e una descrizione del modello che verranno visualizzati dagli utenti del lab. Pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello. 

### <a name="set-title-and-description"></a>Impostare titolo e descrizione
1. Nella sezione **Modello** selezionare **Modifica** (icona a forma di matita) per il modello. 
2. Nella finestra **User view** (Visualizzazione utente) immettere un **titolo** per il modello.
3. Immettere una **descrizione** per il modello.
4. Selezionare **Salva**.

    ![Descrizione del lab per le classi](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="set-up-the-template-vm"></a>Configurare la macchina virtuale modello
 Connettersi alla macchina virtuale modello e installarvi tutto il software necessario prima di renderla disponibile agli studenti. 

1. Attendere che la macchina virtuale modello sia pronta. Una volta pronta, viene abilitato il pulsante **Avvia**. Per avviare la macchina virtuale, selezionare **Avvia**.

    ![Avviare la macchina virtuale modello](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Per connettersi alla macchina virtuale, selezionare **Connetti** e seguire le istruzioni. 

    ![Connettersi alla macchina virtuale modello](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Installare tutto il software necessario agli studenti per eseguire il lab, ad esempio, Visual Studio, Azure Storage Explorer e così via. 
2. Disconnettersi dalla macchina virtuale modello, ovvero chiudere la sessione di accesso remota. 
3. **Arrestare** la macchina virtuale modello selezionando **Arresta**. 

    ![Arrestare la macchina virtuale modello](../media/tutorial-setup-classroom-lab/stop-template-vm.png)

### <a name="publish-the-template"></a>Pubblicare il modello 
Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello. 

1. Selezionare **Pubblica** nella sezione **Modello**. 

    ![Pubblicare la macchina virtuale modello](../media/tutorial-setup-classroom-lab/public-access.png)
1. Nella finestra **Pubblica** selezionare l'opzione **Pubblicato**. 
2. Selezionare ora il pulsante **Pubblica**. Questo processo potrebbe richiedere alcuni minuti a seconda del numero di macchine virtuali da creare, che è uguale al numero di utenti consentiti nel lab.
    
    > [!IMPORTANT]
    > Non è possibile annullare la pubblicazione di un modello, dopo che questo è stato pubblicato. 
4. Passare alla pagina **Macchine virtuali** e verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. 

    ![Macchine virtuali](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Attendere finché le macchine virtuali non vengono create. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>Inviare un collegamento di registrazione agli studenti

1. Passare alla visualizzazione **Dashboard**. 
2. Selezionare il riquadro **User registration** (Registrazione utente).

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Copia**. Il collegamento viene copiato negli Appunti. Incollarlo in un editor di posta elettronica e inviare un messaggio di posta elettronica allo studente. 

    ![Collegamento di registrazione dello studente](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Chiudi**. 


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un lab per le classi e come configurare il lab. Per informazioni su come uno studente può accedere a una VM nel lab usando il collegamento di registrazione, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Connettersi a una VM nel lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)

