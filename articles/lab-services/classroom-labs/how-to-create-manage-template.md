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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: f0fc1e143ce7d271d5faaa8dda0eb40cdfc9e006
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332765"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Creare e gestire un modello per le classi in Azure Lab Services
Un modello in un lab è un'immagine della macchina virtuale di base da cui vengono create tutte le macchine virtuali di tutti gli utenti. Configurare la macchina virtuale modello con tutte le caratteristiche che si vuole fornire agli utenti del lab. È possibile specificare un nome e una descrizione del modello che verranno visualizzati dagli utenti del lab. Quindi, pubblicare il modello per rendere disponibili agli utenti del lab le istanze della macchina virtuale modello. Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo corrisponde al numero massimo di utenti consentiti nel lab, che è possibile impostare nei criteri di utilizzo del lab. Tutte le macchine virtuali hanno la stessa configurazione del modello.

Questo articolo descrive come creare e gestire un modello di macchina virtuale in un lab per le classi di Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Pubblicare un modello durante la creazione di un lab per le classi
Per informazioni su come pubblicare un modello durante la creazione di un Lab in aula, vedere [creare un Lab in aula](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>Impostare o aggiornare il titolo e la descrizione del modello
Seguire questa procedura per impostare il titolo e la descrizione per la prima volta e aggiornarli in un secondo momento. 

1. Nella pagina **modello** immettere il nuovo **titolo** per il Lab.  
2. Immettere la nuova **Descrizione** del modello. Quando si sposta lo stato attivo dalla casella di testo, questo viene salvato automaticamente. 

    ![Nome e descrizione del modello](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Aggiornare una macchina virtuale modello
Usare la procedura seguente per aggiornare una macchina virtuale modello.  

1. Nella pagina **modello** selezionare **Personalizza modello** sulla barra degli strumenti. 

    ![Pulsante Personalizza modello](../media/how-to-create-manage-template/customize-template-button.png)
2. Nella finestra di dialogo **Personalizza modello** selezionare **continua**. Una volta avviato il modello e apportato le modifiche, non avrà più la stessa configurazione delle macchine virtuali pubblicate per l'ultima volta agli utenti. Le modifiche ai modelli non verranno applicate alle macchine virtuali esistenti degli utenti finché non si esegue di nuovo la pubblicazione.

    ![Finestra di dialogo Personalizza](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Selezionare il pulsante **Connetti al modello** sulla barra degli strumenti per connettersi alla macchina virtuale del modello e seguire le istruzioni. Se si tratta di un computer Windows, verrà visualizzata un'opzione per scaricare il file RDP. 

    ![Connettersi alla macchina virtuale modello](../media/how-to-create-manage-template/connect-template-vm.png)
1. Installare tutto il software necessario agli studenti per eseguire il lab, ad esempio, Visual Studio, Azure Storage Explorer e così via. 
2. Disconnettersi dalla macchina virtuale modello, ovvero chiudere la sessione di accesso remota. 
3. **Arrestare** la macchina virtuale del modello selezionando **Arresta modello**. 
4. Seguire i passaggi della sezione successiva per **pubblicare** la macchina virtuale del modello aggiornata. 

## <a name="publish-the-template-vm"></a>Pubblicare la macchina virtuale modello  
Se si non pubblica il modello durante la creazione del lab, è possibile pubblicarlo in un secondo momento. Prima della pubblicazione, è possibile connettersi al modello di macchina virtuale e aggiornarlo con qualsiasi software. Quando si pubblica un modello, Azure Lab Services crea le macchine virtuali nel lab usando il modello. Il numero di macchine virtuali create in questo processo è il numero di macchine virtuali specificate durante la pubblicazione per la prima volta o per quanto specificato nella pagina del pool di macchine virtuali. Tutte le macchine virtuali hanno la stessa configurazione del modello. 

1. Nella pagina **modello** selezionare **pubblica** sulla barra degli strumenti. 
1. Nella finestra di messaggio **Pubblicare il modello** esaminare il messaggio e selezionare **Pubblica**. Questo processo può richiedere tempo a seconda del numero di macchine virtuali che vengono create.

    ![Pulsante Publish](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Non è possibile annullare la pubblicazione di un modello, dopo che questo è stato pubblicato. Tuttavia, è possibile ripubblicare il modello. 
4. È possibile visualizzare lo stato del processo di pubblicazione nella pagina modello. Attendere che lo stato del modello si imposti su **Pubblicato**. 

    ![Stato della pubblicazione](../media/how-to-create-manage-template/publish-status.png)
1. Passare alla pagina **Macchine virtuali** e verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Attendere finché le macchine virtuali non vengono create. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
