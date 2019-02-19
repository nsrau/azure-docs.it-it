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
ms.openlocfilehash: 2e34aef9a6f184c60fbb23023f798941a1fc11da
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894429"
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
2. Salvare il file RDP sul disco rigido e aprirlo (supponendo che sia una VM Windows)
3. Usare **nome utente** e **password** ricevuti dal docente per accedere alla macchina virtuale. 

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione ha illustrato come eseguire l'accesso a un lab per le classi usando il collegamento di registrazione ottenuto dal professore/docente.

In quanto proprietario del lab, si vuole vedere chi ha eseguito la registrazione per il lab e tenere traccia dell'utilizzo delle macchine virtuali. Passare all'esercitazione successiva per informazioni su come tenere traccia dell'uso del lab:

> [!div class="nextstepaction"]
> [Tenere traccia dell'utilizzo di un lab](tutorial-track-usage.md) 