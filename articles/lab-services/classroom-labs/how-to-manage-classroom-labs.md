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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 705209becff7c8ad20e7d09f056aa1ae1577b7ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659486"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gestire i lab per le classi in Azure Lab Services 
Questo articolo illustra come creare e configurare un lab per le classi, visualizzare tutti i lab per le classi o eliminare un lab per le classi.

## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com).
2. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab per le classi. 
    2. Selezionare le **dimensioni** della macchina virtuale che si intende usare nella classe.
    3. Selezionare l'**immagine** da usare per creare la macchina virtuale.
    4. Specificare le **credenziali predefinite** da usare per accedere alle macchine virtuali nel lab.
    7. Selezionare **Salva**.

        ![Creare un lab per le classi](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. Viene visualizzata la **home page** del lab. 
    
    ![Home page del lab per le classi](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurare i criteri di utilizzo

1. Selezionare **Usage policy** (Criteri di utilizzo). 
2. Nelle impostazioni di **Usage policy** (Criteri di utilizzo) immettere il **numero di utenti** a cui è consentito usare il lab.
3. Selezionare **Salva**. 

    ![Criteri di utilizzo](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configurare il modello
Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello con tutte le caratteristiche che si vuole fornire agli utenti del lab. È possibile specificare un nome e una descrizione del modello che verranno visualizzati dagli utenti del lab. Impostare la visibilità del modello su Pubblico per rendere disponibili agli utenti del lab le istanze del modello di macchina virtuale.  

### <a name="set-template-title-and-description"></a>Impostare titolo e descrizione del modello
1. Nella sezione **Modello** selezionare **Modifica** (icona a forma di matita) per il modello. 
2. Nella finestra **User view** (Visualizzazione utente) immettere un **titolo** per il modello.
3. Immettere una **descrizione** per il modello.
4. Selezionare **Salva**.

    ![Descrizione del lab per le classi](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Rendere pubbliche le istanze del modello 
Dopo aver impostato o stato della visibilità di un modello su **Pubblico**, Azure Lab Services crea le VM nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello.  

1. Selezionare **Visibilità** nella sezione **Modello**. 
2. Nella pagina **Disponibilità** selezionare lo stato **Pubblico**.
    
    > [!IMPORTANT]
    > Quando un modello è disponibile pubblicamente, l'accesso non può essere modificato su privato. 
3. Selezionare **Salva**.

    ![Disponibilità](../media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Inviare un collegamento di registrazione agli studenti

1. Selezionare il riquadro **User registration** (Registrazione utente).
2. Nella finestra di dialogo **User registration** (Registrazione utente) selezionare il pulsante **Copia**. Il collegamento viene copiato negli Appunti. Incollarlo in un editor di posta elettronica e inviare un messaggio di posta elettronica allo studente. 

    ![Collegamento di registrazione dello studente](../media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Visualizzare tutti i lab per le classi
1. Accedere al [portale di Azure Lab Services](https://labs.azure.com).
2. Confermare che sia possibile visualizzare tutti i lab dell'account del lab selezionato. 

    ![Tutti i lab](../media/how-to-manage-classroom-labs/all-labs.png)
3. Dall'elenco a discesa nella parte superiore, selezionare un account del lab differente. Tutti i lab dell'account selezionato sono visibili. 

## <a name="delete-a-classroom-lab"></a>Eliminare un lab per le classi
1. Nel riquadro del lab, selezionare l'icona con tre punti (...) nell'angolo. 

    ![Selezionare il lab](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Selezionare **Elimina**. 

    ![Pulsante Elimina](../media/how-to-manage-classroom-labs/delete-button.png)
3. Nella finestra di dialogo **Delete lab**(Elimina lab), selezionare **Elimina**. 

    ![Finestra di dialogo Elimina](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Passaggi successivi
Introduzione alla configurazione di un lab usando Azure Lab Services:

- [Configurare un lab per le classi](how-to-manage-classroom-labs.md)
- [Configurare un lab](../tutorial-create-custom-lab.md)
