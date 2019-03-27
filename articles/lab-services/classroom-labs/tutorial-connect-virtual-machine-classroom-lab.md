---
title: Accedere a un lab per le classi in Azure Lab Services | Microsoft Docs
description: Questa esercitazione descrive come accedere alle macchine virtuali in un lab per le classi impostato da un docente.
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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 3ec3abffc7962051f4cfc02d5369581ca193d70e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775578"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Esercitazione: Accedere a un lab per le classi in Azure Lab Services
Questa esercitazione descrive come uno studente può connettersi a una macchina virtuale (VM) in un lab per le classi. 

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Usare il collegamento di registrazione 
> * Connettersi alla macchina virtuale

## <a name="use-the-registration-link"></a>Usare il collegamento di registrazione

1. Passare all'**URL di registrazione** ricevuto dal docente. Non è necessario usare l'URL di registrazione dopo aver completato la registrazione. Usare invece l'URL [https://labs.azure.com](https://labs.azure.com). 
1. Accedere al servizio usando l'account dell'istituto di istruzione per completare la registrazione. 
2. Al termine della registrazione, verificare che le macchine virtuali per i lab a cui si ha accesso siano disponibili. 
3. Attendere che la macchina virtuale sia pronta e quindi **avviarla**. L'esecuzione di questo processo richiede tempo.  

    ![Avviare la VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale

1. Selezionare **Connetti** sul riquadro della macchina virtuale del lab a cui si vuole accedere. 

    ![Connettersi alla macchina virtuale](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Eseguire uno dei passaggi seguenti: 
    1. Per le macchine virtuali **Windows**, salvare il file **RDP** nel disco rigido. Aprire il file RDP per connettersi alla macchina virtuale. Usare il **nome utente** e la **password** forniti dal docente per accedere alla macchina virtuale. 
    3. Per le macchine virtuali **Linux**, copiare e salvare la stringa di connessione SSH nella finestra di dialogo **Connettersi alla macchina virtuale**. Usare questa stringa di connessione da un terminale SSH, ad esempio [Putty](https://www.putty.org/), per connettersi alla macchina virtuale. 

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come eseguire l'accesso a un lab per le classi usando il collegamento di registrazione ottenuto dal professore/docente.

In quanto proprietario del lab, si vuole vedere chi ha eseguito la registrazione per il lab e tenere traccia dell'utilizzo delle macchine virtuali. Passare all'esercitazione successiva per informazioni su come tenere traccia dell'uso del lab:

> [!div class="nextstepaction"]
> [Tenere traccia dell'utilizzo di un lab](tutorial-track-usage.md) 