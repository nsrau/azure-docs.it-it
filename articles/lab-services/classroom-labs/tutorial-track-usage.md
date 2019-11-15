---
title: Tenere traccia dell'utilizzo di un lab in Azure Lab Services | Microsoft Docs
description: In questa esercitazione, in veste di creatore/proprietario del lab, si vuole traccia dell'utilizzo del lab.
services: lab-services
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 315ebfa1460f9d9bc041925cec2451f63ac5be16
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580266"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Esercitazione: Tenere traccia dell'utilizzo di un lab in Azure Lab Services
Questa esercitazione spiega in che modo il creatore/proprietario di un lab può tenere traccia dell'utilizzo del lab.

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Visualizzare gli utenti che hanno eseguito la registrazione per il lab
> * Visualizzare l'utilizzo delle macchine virtuali nel lab
> * Gestire le macchine virtuali degli studenti 


## <a name="view-users-registered-with-the-lab"></a>Visualizzare gli utenti che hanno eseguito la registrazione per il lab

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). 
2. Selezionare **Accedi** e immettere le credenziali. Azure Lab Services supporta gli account aziendali e gli account Microsoft.
3. Nella pagina **My labs** (I miei lab) selezionare il lab per il quale si vuole tenere traccia dell'utilizzo. 
4. Selezionare **Utenti** nel menu a sinistra oppure il riquadro **Utenti**. Verranno visualizzati gli studenti che hanno eseguito la registrazione per il lab. Selezionare **Registration link** (Collegamento di registrazione), copiare il collegamento e inviarlo a qualsiasi nuovo studente che non ha ancora eseguito la registrazione per il lab. 

    ![Utenti registrati](../media/tutorial-track-usage/registered-users.png)

    Per informazioni dettagliate sull'aggiunta e la gestione degli utenti per il lab, vedere [Aggiungere e gestire gli utenti del lab](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms-in-the-lab"></a>Visualizzare l'utilizzo delle macchine virtuali nel lab 

1. Nel menu a sinistra scegliere **Macchine virtuali**. 
2. Verificare che vengano visualizzati lo stato e il numero di ore di esecuzione delle macchine virtuali. Il tempo usato dal proprietario del lab nella macchina virtuale di uno studente non viene conteggiato nel tempo di utilizzo visualizzato nell'ultima colonna. 

    ![Utilizzo delle VM](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gestire le macchine virtuali degli studenti 
In questa pagina è possibile avviare, arrestare o reimpostare le macchine virtuali degli studenti usando i controlli nella colonna **Stato** o sulla barra degli strumenti.

![Controlli della macchina virtuale](../media/tutorial-track-usage/vm-controls.png)

Per informazioni dettagliate sulla gestione del pool di macchine virtuali per il lab, vedere [Configurare e gestire il pool di macchine virtuali](how-to-set-virtual-machine-passwords.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui lab per le classi, vedere gli articoli nella sezione [Guide alle procedure](how-to-manage-lab-accounts.md).
