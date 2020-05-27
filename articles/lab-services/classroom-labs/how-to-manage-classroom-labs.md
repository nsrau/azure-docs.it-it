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
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 7a60f761e4ee575e3196bb1ccd3baa42f27221f8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588174"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gestire i lab per le classi in Azure Lab Services 
Questo articolo descrive come creare ed eliminare un lab per le classi, nonché come visualizzare tutti i lab per le classi in un account lab. 

## <a name="prerequisites"></a>Prerequisiti
Per configurare un lab per le classi in un account del lab, è necessario essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare un account del lab viene aggiunto automaticamente a questo ruolo. Un proprietario del lab può aggiungere altri utenti al ruolo Autore di laboratori seguendo i passaggi descritti nell'articolo seguente: [Aggiungere un utente al ruolo di creatore di lab](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
2. Selezionare **Accedi** e immettere le credenziali. Selezionare o immettere un **ID utente** appartenente al ruolo **Autore di laboratori** nell'account lab e quindi immettere la password. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Selezionare **New lab** (Nuovo lab). 
    
    ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab. 
    2. Selezionare le **dimensioni delle macchine virtuali** necessarie per la classe. Per l'elenco delle dimensioni disponibili, vedere la sezione [dimensioni delle macchine virtuali](#vm-sizes). 
    3. Selezionare l'**immagine della macchina virtuale** che si vuole usare per il lab per le classi. Se si seleziona un'immagine di Linux, viene visualizzata un'opzione per abilitare la connessione Desktop remoto. Per informazioni, vedere [Abilitare Connessione desktop remoto per Linux](how-to-enable-remote-desktop-linux.md).

        Se è stato eseguito l'accesso con le credenziali del proprietario dell'account lab, viene visualizzata un'opzione per abilitare più immagini per il lab. Per altre informazioni, vedere [Abilitare le immagini al momento della creazione del lab](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    4. Vedere il **prezzo totale all'ora** visualizzato nella pagina. 
    6. Selezionare **Salva**.

        ![Finestra New lab](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Viene visualizzata un'opzione per selezionare una località del lab se l'account lab è stato configurato con l'opzione [Consenti all'autore del lab di selezionare la località del lab](allow-lab-creator-pick-lab-location.md). 
4. Nella pagina **Virtual machine credentials** (Credenziali macchina virtuale) specificare le credenziali predefinite per tutte le VM del lab.
    1. Specificare il **nome dell'utente** per tutte le macchine virtuali del lab.
    2. Specificare la **password** dell'utente. 

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Disabilitare l'opzione **Usa la stessa password per tutte le macchine virtuali** se si vuole che gli studenti impostino le proprie password. Questo passaggio è **facoltativo**. 

        Un docente può scegliere di usare la stessa password per tutte le macchine virtuali del lab o consentire agli studenti di impostare le password per le proprie macchine virtuali. Per impostazione predefinita, questa impostazione è abilitata per tutte le immagini del sistema operativo Windows e Linux, ad eccezione di Ubuntu. Se si seleziona una macchina virtuale **Ubuntu**, questa impostazione è disabilitata, per cui agli studenti verrà richiesto di impostare una password quando accedono per la prima volta.  

        ![Finestra New lab](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Selezionare quindi **Avanti** nella pagina **Credenziali della macchina virtuale**. 
5. Nella pagina **Criteri del lab** procedere come segue:
    1. Immettere il numero di ore allocate per ogni utente (**quota per ogni utente**) al di fuori del tempo pianificato per il lab. 
    2. Per l'opzione **Arresto automatico delle macchine virtuali** specificare se si vuole che la macchina virtuale venga arrestata automaticamente quando l'utente si disconnette. È anche possibile specificare per quanto tempo la macchina virtuale deve attendere che l'utente si riconnetta prima di arrestarsi automaticamente. Per altre informazioni, vedere [Abilitare l'arresto automatico delle macchine virtuali al termine della connessione](how-to-enable-shutdown-disconnect.md).
    3. Quindi selezionare **Fine**. 

        ![Quota per ogni utente](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Dovrebbe essere visualizzata la schermata seguente, che mostra lo stato di creazione della VM modello. La creazione del modello nel lab richiede fino a 20 minuti. 

    ![Stato di creazione della VM modello](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Nella pagina **Template** (Modello) procedere come segue: Questi passaggi sono **facoltativi** per l'esercitazione.

    2. Connettersi alla macchina virtuale modello selezionando **Connect** (Connetti). Se si tratta di una macchina virtuale modello di Linux, scegliere se si vuole effettuare la connessione con SSH o RDP (se RDP è abilitato).
    1. Selezionare **Reset password** (Reimposta password) per reimpostare la password per la macchina virtuale. 
    1. Installare e configurare il software nella macchina virtuale modello. 
    1. **Arrestare** la VM.  
    1. Immettere una **descrizione** per il modello.
10. Sulla barra degli strumenti della pagina **Template** (Modello) selezionare **Publish** (Pubblica). 

    ![Pulsante per la pubblicazione del modello](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > L'operazione di pubblicazione non può essere annullata. 
8. Nella pagina **Publish template** (Pubblica modello) immettere il numero di macchine virtuali da creare nel lab, quindi selezionare **Publish** (Pubblica). 

    ![Pubblicazione del modello: numero di VM](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Nella pagina viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Passare alla pagina **Pool di macchine virtuali** scegliendo Macchine virtuali dal menu sinistro oppure selezionando il riquadro Macchine virtuali. Verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    In questa pagina vengono eseguite le attività seguenti, ma non completarle per questa esercitazione. Questi passaggi hanno uno scopo unicamente informativo: 
    
    1. Per cambiare la capacità del lab, ossia il numero di VM al suo interno, selezionare **Lab capacity** (Capacità del lab) sulla barra degli strumenti.
    2. Per avviare tutte le VM contemporaneamente, selezionare **Start all** (Avvia tutte) sulla barra degli strumenti. 
    3. Per avviare una specifica VM, selezionare la freccia in già in **Status** (Stato) e quindi selezionare **Start** (Avvia). Per avviare una VM, è anche possibile selezionarla nella prima colonna e quindi selezionare **Start** (Avvia) sulla barra degli strumenti.                

### <a name="vm-sizes"></a>Dimensioni delle macchine virtuali  

| Dimensione | Core | RAM | Descrizione | 
| ---- | ----- | --- | ----------- | 
| Piccolo | 2 | 3,5 GB | Queste dimensioni sono più indicate per la riga di comando, l'apertura del Web browser, server Web con traffico ridotto, database di piccole e medie dimensioni. |
| Media | 4 | 7 GB | Queste dimensioni sono più indicate per database relazionali, memorizzazione nella cache in memoria e analisi | 
| Medium (virtualizzazione annidata) | 4 | 16 GB | Queste dimensioni sono più indicate per database relazionali, memorizzazione nella cache in memoria e analisi. Queste dimensioni supportano anche la virtualizzazione annidata. <p>Queste dimensioni possono essere usate in scenari in cui ogni studente necessita di più macchine virtuali. I docenti possono usare la virtualizzazione annidata per configurare alcune macchine virtuali annidate di piccole dimensioni all'interno della macchina virtuale. </p> |
| GPU small (calcolo) | 6 | 56 GB | <p>Queste dimensioni sono più indicate per applicazioni a elevato utilizzo di calcolo e di rete quali intelligenza artificiale e Deep Learning.</p><p>Azure Lab Services installa e configura automaticamente i driver GPU necessari quando si crea un lab con le immagini GPU. </p> | 
| GPU small (visualizzazione) | 6 | 56 GB | Queste dimensioni sono più indicate per la visualizzazione remota, lo streaming, i giochi e la codifica con framework quali OpenGL e DirectX. | 
| large | 8 | 16 GB | Queste dimensioni sono più indicate per le applicazioni che richiedono CPU più veloci, prestazioni del disco locale migliori, database di grandi dimensioni, cache di memoria di dimensioni elevate. |
| Large (virtualizzazione annidata) | 8 | 32 GB | Queste dimensioni sono più indicate per le applicazioni che richiedono CPU più veloci, prestazioni del disco locale migliori, database di grandi dimensioni, cache di memoria di dimensioni elevate. Queste dimensioni supportano anche la virtualizzazione annidata. |  
| GPU medium (visualizzazione) | 12 | 112 GB | Queste dimensioni sono più indicate per la visualizzazione remota, lo streaming, i giochi e la codifica con framework quali OpenGL e DirectX. | 

> [!NOTE]
> È possibile che alcune di queste dimensioni di macchina virtuale non vengano visualizzate nell'elenco durante la creazione di un lab per le classi. L'elenco viene popolato in base alla capacità corrente della località del lab. Se l'autore dell'account lab [consente agli autori del lab di selezionare una località per il lab](allow-lab-creator-pick-lab-location.md), è possibile provare a scegliere una località diversa per il lab e verificare se le dimensioni della macchina virtuale sono disponibili. 


## <a name="view-all-classroom-labs"></a>Visualizzare tutti i lab per le classi
1. Accedere al [portale di Azure Lab Services](https://labs.azure.com).
2. Fare clic su **Accedi**. Selezionare o immettere un **ID utente** appartenente al ruolo **Autore di laboratori** nell'account lab e quindi immettere la password. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Confermare che sia possibile visualizzare tutti i lab dell'account del lab selezionato. Nel riquadro del lab viene visualizzato il numero di macchine virtuali nel lab e la quota per ogni utente (al di fuori del tempo pianificato).

    ![Tutti i lab](../media/how-to-manage-classroom-labs/all-labs.png)
3. Dall'elenco a discesa nella parte superiore, selezionare un account del lab differente. Tutti i lab dell'account selezionato sono visibili. 

## <a name="delete-a-classroom-lab"></a>Eliminare un lab per le classi
1. Nel riquadro del lab, selezionare l'icona con tre punti (...) nell'angolo e quindi selezionare **Elimina**. 

    ![Pulsante Elimina](../media/how-to-manage-classroom-labs/delete-button.png)
3. Nella finestra di dialogo **Elimina lab** selezionare **Elimina** per continuare con l'eliminazione. 

## <a name="switch-to-another-classroom-lab"></a>Passare a un altro lab per le classi
Per passare a un altro lab per le classi, in quello corrente selezionare l'elenco a discesa di lab nell'account del lab nella parte superiore.

![Selezionare il lab dall'elenco a discesa nella parte superiore](../media/how-to-manage-classroom-labs/switch-lab.png)

È anche possibile creare un nuovo lab usando l'opzione **Nuovo lab** in questo elenco a discesa. 

> [!NOTE]
> Per gestire i lab è anche possibile usare il modulo di PowerShell Az.LabServices (anteprima). Per altre informazioni, vedere la [home page di Az.LabServices su GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Per passare a un account lab diverso, selezionare l'elenco a discesa accanto all'account lab e selezionare l'altro account lab. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)

