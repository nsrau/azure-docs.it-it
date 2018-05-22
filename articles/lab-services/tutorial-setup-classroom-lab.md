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
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 3e9f8d118c4413ec93b7dffcfa41b6ad4381b052
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Esercitazione: Configurare un lab per le classi 
Questa esercitazione descrive come configurare un lab per le classi con un set di macchine virtuali usate dagli studenti nella classe.  

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Creare un lab per le classi
> * Configurare il lab per le classi
> * Inviare un collegamento di registrazione agli studenti

## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Passare al [sito Web di Azure Lab Services](https://labs.azure.com).
2. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab per le classi. 
    2. Selezionare le **dimensioni** della macchina virtuale che si intende usare nella classe.
    3. Selezionare l'**immagine** da usare per creare la macchina virtuale.
    4. Specificare le **credenziali predefinite** per la registrazione alle macchine virtuali nel lab. 
    7. Selezionare **Salva**.

        ![Creare un lab per le classi](./media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Viene visualizzata la **home page** del lab. 
    
    ![Home page del lab per le classi](./media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurare i criteri di utilizzo

1. Selezionare **Usage policy** (Criteri di utilizzo). 
2. Nelle impostazioni di **Usage policy** (Criteri di utilizzo) immettere il **numero di utenti** a cui è consentito usare il lab.
3. Selezionare **Salva**. 

    ![Criteri di utilizzo](./media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configurare il modello 
Nel modello di un lab vengono create le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello con tutte le caratteristiche che si vuole fornire agli utenti del lab. È possibile specificare un nome e una descrizione del modello che gli utenti del lab visualizzeranno e rendere pubblica la visibilità in modo che le istanze della VM modello siano disponibili agli utenti del lab. 

### <a name="set-title-and-description"></a>Impostare titolo e descrizione
1. Nella sezione **Modello** selezionare **Modifica** (icona a forma di matita) per il modello. 
2. Nella finestra **User view** (Visualizzazione utente) immettere un **titolo** per il modello.
3. Immettere una **descrizione** per il modello.
4. Selezionare **Salva**.

    ![Descrizione del lab per le classi](./media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Rendere pubbliche le istanze del modello
Dopo aver impostato o stato della visibilità di un modello su **Pubblico**, Azure Lab Services crea le VM nel lab usando il modello. Il numero di VM create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello. 

1. Selezionare **Visibilità** nella sezione **Modello**. 
2. Nella pagina **Disponibilità** selezionare lo stato **Pubblico**.
    
    > [!IMPORTANT]
    > Quando un modello è disponibile pubblicamente, l'accesso non può essere modificato su privato. 
3. Selezionare **Salva**.

    ![Disponibilità](./media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>Inviare un collegamento di registrazione agli studenti

1. Selezionare il riquadro **User registration** (Registrazione utente).
2. Nella finestra di dialogo **User registration** (Registrazione utente) seleziona il pulsante **Copia**. Il collegamento viene copiato negli Appunti. Incollarlo in un editor di posta elettronica e inviare un messaggio di posta elettronica allo studente. 

    ![Collegamento di registrazione dello studente](./media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come creare un lab per le classi e come configurare il lab. Per informazioni su come uno studente può accedere a una VM nel lab usando il collegamento di registrazione, passare alla prossima esercitazione:

> [!div class="nextstepaction"]
> [Connettersi a una VM nel lab per le classi](tutorial-connect-virtual-machine-classroom-lab.md)

