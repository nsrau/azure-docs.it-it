---
title: Abilitare gli ambienti con più macchine virtuali in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare un ambiente con più macchine virtuali all'interno di una macchina virtuale modello in un Lab della classe Azure Lab Services.
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332363"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Creare un ambiente con più macchine virtuali all'interno di una macchina virtuale modello di un Lab della classe
Attualmente Azure Lab Services consente di configurare una macchina virtuale modello in un Lab e di rendere disponibile una singola copia per ogni utente. Tuttavia, se si è un professore che insegna una classe IT su come configurare i firewall o i server, potrebbe essere necessario fornire a ognuno degli studenti un ambiente in cui più macchine virtuali possono comunicare tra loro in una rete.

La virtualizzazione nidificata consente di creare un ambiente con più VM all'interno della macchina virtuale modello di un Lab. La pubblicazione del modello fornirà a ogni utente del Lab una macchina virtuale configurata con più macchine virtuali al suo interno.

## <a name="what-is-nested-virtualization"></a>Che cos'è la virtualizzazione nidificata?
La virtualizzazione nidificata consente di creare macchine virtuali all'interno di una macchina virtuale. La virtualizzazione nidificata viene eseguita tramite Hyper-V ed è disponibile solo nelle macchine virtuali Windows.

Per ulteriori informazioni sulla virtualizzazione nidificata, vedere gli articoli seguenti:

- [Virtualizzazione annidata in Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Come abilitare la virtualizzazione annidata in una macchina virtuale di Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Usare la virtualizzazione annidata in Azure Lab Services
I passaggi importanti sono:

1. Creare un computer modello **Windows** di **grandi** dimensioni per il Lab. 
2. Connettersi e abilitare la [virtualizzazione annidata](../../virtual-machines/windows/nested-virtualization.md).


Nella procedura riportata di seguito vengono illustrati i passaggi dettagliati: 

1. Se non ne è già stato creato uno, creare un account Lab. Per istruzioni, vedere [esercitazione: configurare un account Lab con Azure Lab Services](tutorial-setup-lab-account.md).
1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
2. Selezionare **Accedi** e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Selezionare **nuovo Lab**. 
    
    ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab. 
    2. Selezionare **grande (virtualizzazione annidata)** o **media (virtualizzazione annidata)** per le **dimensioni della macchina virtuale**.
    6. Selezionare un' **immagine** di Windows che si desidera utilizzare. La virtualizzazione nidificata è disponibile solo nei computer Windows. 
    4. Quindi selezionare **Avanti**. 

        ![Creare un lab per le classi](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. Nella pagina **credenziali macchina virtuale** specificare le credenziali predefinite per tutte le macchine virtuali nel Lab. Specificare il **nome** e la **password** per l'utente e quindi fare clic su **Avanti**.  

        ![Nuova finestra del Lab](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Nella pagina **criteri Lab** immettere il numero di ore assegnate per ogni utente (**quota per ogni utente**) al di fuori dell'ora pianificata per il Lab, quindi selezionare **fine**. 

        ![Quota per ogni utente](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Verrà visualizzata la schermata seguente che mostra lo stato della creazione della macchina virtuale del modello. La creazione del modello nel lab richiede fino a 20 minuti. 

    ![Stato della creazione della macchina virtuale modello](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. Nella pagina **modello** selezionare **Personalizza modello** sulla barra degli strumenti. 

    ![Pulsante Personalizza modello](../media/how-to-create-manage-template/customize-template-button.png)
2. Nella finestra di dialogo **Personalizza modello** selezionare **continua**. Una volta avviato il modello e apportato le modifiche, non avrà più la stessa configurazione delle macchine virtuali pubblicate per l'ultima volta agli utenti. Le modifiche ai modelli non verranno applicate alle macchine virtuali esistenti degli utenti finché non si esegue di nuovo la pubblicazione.

    ![Finestra di dialogo Personalizza](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Selezionare il pulsante **Connetti al modello** sulla barra degli strumenti per connettersi alla macchina virtuale modello per configurare la virtualizzazione annidata e seguire le istruzioni. Se si tratta di un computer Windows, verrà visualizzata un'opzione per scaricare il file RDP. 

    ![Connettersi alla macchina virtuale modello](../media/how-to-create-manage-template/connect-template-vm.png) 
9. All'interno della macchina virtuale modello, configurare la virtualizzazione annidata e configurare una rete virtuale con più macchine virtuali. Per istruzioni dettagliate, vedere [come abilitare la virtualizzazione annidata in una macchina virtuale di Azure](../../virtual-machines/windows/nested-virtualization.md). Ecco un breve riepilogo dei passaggi: 
    1. Abilitare la funzionalità Hyper-V nella macchina virtuale del modello.
    2. Configurare una rete virtuale interna con connettività Internet per le macchine virtuali nidificate
    3. Creazione di macchine virtuali tramite la console di gestione di Hyper-V
    4. Assegnare un indirizzo IP alle macchine virtuali
10. Nella pagina **modello** selezionare **pubblica** sulla barra degli strumenti. 

    ![Pulsante Pubblica modello](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > L'operazione di pubblicazione non può essere annullata. 
8. Nella pagina **Pubblica modello** immettere il numero di macchine virtuali che si desidera creare nel Lab, quindi selezionare **pubblica**. 

    ![Pubblicare un modello: numero di macchine virtuali](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Viene visualizzato lo **stato pubblicazione** del modello nella pagina. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>Passaggi successivi

A questo punto, ogni utente ottiene una singola macchina virtuale che include un ambiente con più VM al suo interno. Per informazioni su come aggiungere utenti al Lab e inviare loro il collegamento di registrazione, vedere l'articolo seguente: [aggiungere utenti al Lab](tutorial-setup-classroom-lab.md#add-users-to-the-lab).