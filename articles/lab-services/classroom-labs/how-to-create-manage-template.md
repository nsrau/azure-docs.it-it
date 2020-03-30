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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: fcf31fcc266358911612c25e0b73a0a9de696b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539025"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Creare e gestire un modello per le classi in Azure Lab Services
Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello con tutte le caratteristiche che si vuole fornire agli utenti del lab. È possibile specificare un nome e una descrizione del modello che verranno visualizzati dagli utenti del lab. Quindi, pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello. Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello.

Questo articolo descrive come creare e gestire un modello di macchina virtuale in un lab per le classi di Azure Lab Services. 

## <a name="set-or-update-template-title-and-description"></a>Impostare o aggiornare il titolo e la descrizione del modello
Seguire questa procedura per impostare il titolo e la descrizione per la prima volta e aggiornarli in un secondo momento. 

1. Nella pagina **Modello** immettere il nuovo **titolo** per il lab.  
2. Immettere la nuova **descrizione** per il modello. Quando si sposta lo stato attivo all'esterno della casella di testo, questo viene salvato automaticamente. 

    ![Nome e descrizione del modello](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Aggiornare una macchina virtuale modelloUpdate a template VM
Usare la procedura seguente per aggiornare una macchina virtuale modello.  

1. Attendere l'avvio della macchina virtuale del modello e quindi selezionare **Connetti al modello** sulla barra degli strumenti per connettersi alla macchina virtuale del modello e seguire le istruzioni. Se si tratta di un computer Windows, verrà visualizzata un'opzione per scaricare il file RDP. 
1. Una volta che ci si connette al modello e si apportano modifiche, non dirà più la stessa configurazione delle macchine virtuali pubblicate per l'ultima volta per gli utenti. Le modifiche ai modelli non si rifletteranno sulle macchine virtuali esistenti degli utenti fino a dopo la pubblicazione.

    ![Connettersi alla macchina virtuale modello](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Installare tutto il software necessario agli studenti per eseguire il lab, ad esempio, Visual Studio, Azure Storage Explorer e così via. 
1. Disconnettersi dalla macchina virtuale modello, ovvero chiudere la sessione di accesso remota. 
1. **Arrestare** la macchina virtuale del modello selezionando **Arresta modello**. 
1. Seguire i passaggi nella sezione successiva per **pubblicare** la macchina virtuale del modello aggiornato. 

## <a name="publish-the-template-vm"></a>Pubblicare la macchina virtuale modello  
In questo passaggio verrà pubblicata la VM modello. Quando si pubblica la macchina virtuale del modello, Azure Lab Services crea macchine virtuali nel lab usando il modello. Tutte le macchine virtuali hanno la stessa configurazione del modello.


1. Sulla barra degli strumenti della pagina **Modello** selezionare **Pubblica**. 

    ![Pulsante per la pubblicazione del modello](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > L'operazione di pubblicazione non può essere annullata. 
2. Nella pagina **Publish template** (Pubblica modello) immettere il numero di macchine virtuali da creare nel lab, quindi selezionare **Publish** (Pubblica). 

    ![Pubblicazione del modello: numero di VM](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Nella pagina viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Attendere il completamento della pubblicazione e quindi passare alla pagina **Pool di macchine virtuali** scegliendo **Macchine virtuali** dal menu sinistro oppure selezionando il riquadro **Macchine virtuali**. Verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
