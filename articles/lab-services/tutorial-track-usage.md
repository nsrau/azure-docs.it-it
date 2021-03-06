---
title: Tenere traccia dell'utilizzo di un lab in Azure Lab Services | Microsoft Docs
description: In questa esercitazione, in veste di creatore/proprietario del lab, si vuole traccia dell'utilizzo del lab.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: a9a9b49b568decc621be1969a8578d61ac7e4861
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445033"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Esercitazione: Tenere traccia dell'utilizzo di un lab in Azure Lab Services
Questa esercitazione spiega in che modo il creatore/proprietario di un lab può tenere traccia dell'utilizzo del lab.

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Visualizzare gli utenti che hanno eseguito la registrazione per il lab
> * Visualizzare l'utilizzo delle macchine virtuali nel lab
> * Gestire le macchine virtuali degli studenti 


## <a name="view-registered-users"></a>Visualizzare gli utenti registrati

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). 
2. Selezionare **Accedi** e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft.
3. Nella pagina **My labs** (I miei lab) selezionare il lab per il quale si vuole tenere traccia dell'utilizzo. 
4. Selezionare **Utenti** nel menu a sinistra oppure il riquadro **Utenti**. Verranno visualizzati gli studenti che hanno eseguito la registrazione per il lab.  

    ![Utenti registrati](./media/tutorial-track-usage/registered-users.png)

    Per altre informazioni su come aggiungere e gestire gli utenti per il lab, vedere [Aggiungere e gestire gli utenti del lab](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Visualizzare l'utilizzo di VM

1. Nel menu a sinistra scegliere **Macchine virtuali**. 
2. Verificare che vengano visualizzati lo stato e il numero di ore di esecuzione delle macchine virtuali. Il tempo usato dal proprietario del lab nella macchina virtuale di uno studente non viene conteggiato nel tempo di utilizzo visualizzato nell'ultima colonna. 

    ![Utilizzo delle VM](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gestire le macchine virtuali degli studenti 
In questa pagina è possibile avviare, arrestare o reimpostare le macchine virtuali degli studenti usando i controlli nella colonna **Stato** o sulla barra degli strumenti.

![Azioni delle macchine virtuali](./media/tutorial-track-usage/vm-controls.png)

Per altre informazioni su come gestire il pool di macchine virtuali per il lab, vedere [Configurare e gestire il pool di macchine virtuali](how-to-set-virtual-machine-passwords.md).

> [!NOTE]
> Quando un docente accende una macchina virtuale per studenti, la quota per lo studente rimane invariata. La quota per un utente specifica il numero di ore lab disponibili per l'utente al di fuori dell'orario delle lezioni pianificato. Per altre informazioni sulle quote, vedere [Impostare quote per gli utenti](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui lab per le classi, vedere gli articoli nella sezione [Guide alle procedure](how-to-manage-lab-accounts.md).
