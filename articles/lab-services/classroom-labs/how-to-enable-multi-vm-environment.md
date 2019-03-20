---
title: Abilita gli ambienti con più VM in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare un ambiente con più macchine virtuali all'interno di una macchina virtuale di modello in un lab per le classi di Azure Lab Services.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190556"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Creare un ambiente con più macchine virtuali all'interno di un modello di macchina virtuale di un lab per le classi
Attualmente Azure Lab Services consente di configurare un modello macchina virtuale in un lab e rendere disponibili per ogni utente una sola copia. Ma se si è professore insegnamento di una classe IT su come configurare i firewall o server, potrebbe essere necessario specificare tutti gli studenti con un ambiente in cui più macchine virtuali possono comunicare tra loro in rete.

La virtualizzazione annidata consente di creare un ambiente multi-VM macchina virtuale del modello di lab. Pubblicare il modello fornirà ogni utente nel lab con una macchina virtuale configurato con più macchine virtuali in esso contenuti.

## <a name="what-is-nested-virtualization"></a>Che cos'è la virtualizzazione annidata?
La virtualizzazione annidata consente di creare macchine virtuali all'interno di una macchina virtuale. La virtualizzazione annidata viene eseguita tramite Hyper-V e disponibile solo nelle macchine virtuali di Windows.

Per altre informazioni sulla virtualizzazione nidificata, vedere gli articoli seguenti:

- [Virtualizzazione annidata in Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Come abilitare la virtualizzazione annidata in una VM di Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Usare la virtualizzazione annidata in Azure Lab Services
Vengono descritti i passaggi fondamentali:

1. Creare un **grandi** ridimensionato **Windows** macchina di modello per l'ambiente lab. 
2. Connettersi ad esso e [abilitare la virtualizzazione annidata](../../virtual-machines/windows/nested-virtualization.md).


La procedura seguente contiene i passaggi dettagliati: 

1. Se si non è già disponibile, creare un account di laboratorio. Per istruzioni, vedere [esercitazione: Configurare un account di laboratorio con Azure Lab Services](tutorial-setup-lab-account.md).
2. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). 
3. Selezionare **Accedi** e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
4. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab. 
    2. Specificare il **numero massimo di macchine virtuali** nel lab. È possibile aumentare o diminuire il numero di VM dopo aver creato il lab oppure in un lab esistente. Per altre informazioni, vedere [Aggiornare il numero di VM in un lab](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Selezionare **Salva**.

        ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Nella pagina **Select virtual machine specifications** (Seleziona le specifiche delle macchine virtuali) eseguire le operazioni seguenti:
    1. Selezionare **grande** per le dimensioni delle macchine virtuali (VM) da creare nel lab. Attualmente, solo la dimensione grande supporta la virtualizzazione nidificata.
    2. Scegliere un'immagine di macchina virtuale che è un **immagine di Windows**. La virtualizzazione annidata è disponibile solo nei computer Windows. 
    3. Selezionare **Avanti**.

        ![Immettere le specifiche delle macchine virtuali](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
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
8. Nel **Configura modello** pagina, selezionare **Connect** per connettersi al modello di macchina virtuale per configurare la virtualizzazione annidata. È anche possibile configurare in un secondo momento dopo aver completato i passaggi descritti in questa procedura guidata. 
9. All'interno della macchina virtuale di modello, configurare la virtualizzazione annidata e configurare una rete virtuale con più macchine virtuali. Per istruzioni dettagliate, vedere [come abilitare la virtualizzazione annidata in una VM di Azure](../../virtual-machines/windows/nested-virtualization.md). Ecco un breve riepilogo dei passaggi: 
    1. Abilitare la funzionalità di Hyper-V nella macchina virtuale modello.
    2. Configurare una rete virtuale interna con connettività internet per le macchine virtuali annidate
    3. Creare macchine virtuali tramite la gestione di Hyper-V
    4. Assegnare un indirizzo IP alle macchine virtuali
10. Selezionare **Next** (Avanti) nella pagina del modello. 
11. Nella pagina **Publish the template** (Pubblica il modello) eseguire le azioni seguenti. 
    1. Per pubblicare il modello immediatamente, selezionare **Publish** (Pubblica).  

        > [!WARNING]
        > L'operazione di pubblicazione non può essere annullata. 
    2. Per eseguire la pubblicazione in un secondo momento, selezionare **Save for later** (Salva per dopo). È possibile pubblicare la macchina virtuale modello al termine della procedura guidata. Per informazioni dettagliate su come configurare e pubblicare il modello al termine della procedura guidata, vedere la sezione [Pubblicare il modello](how-to-create-manage-template.md#publish-the-template-vm) nell'articolo [Gestire i lab per le classi in Azure Lab Services](how-to-manage-classroom-labs.md).

        ![Pubblicare il modello](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. Viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Al termine dell'operazione di pubblicazione del modello, viene visualizzata la pagina seguente. Selezionare **Operazione completata**.

    ![Pubblicazione del modello - operazione completata](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verrà visualizzato il **dashboard** del lab. 
    
    ![Dashboard del lab per le classi](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>Passaggi successivi

A questo punto, ogni utente ottiene una singola macchina virtuale che include un ambiente di più macchine Virtuali in esso contenuti. Per informazioni su come aggiungere utenti nel lab e inviare loro collegamento di iscrizione, vedere l'articolo seguente: [Aggiungere gli utenti nel lab](tutorial-setup-classroom-lab.md#add-users-to-the-lab).