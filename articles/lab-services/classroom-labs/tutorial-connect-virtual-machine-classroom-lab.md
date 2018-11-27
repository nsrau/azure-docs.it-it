---
title: Accedere a un lab per le classi in Azure Lab Services | Microsoft Docs
description: Questa esercitazione descrive come accedere alle macchine virtuali in un lab per le classi impostato da un professore.
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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: b9d140eedf69ed730985412ba1dcdca2d626b191
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706705"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Esercitazione: Accedere a un lab per le classi in Azure Lab Services
Questa esercitazione descrive come uno studente può connettersi a una macchina virtuale (VM) in un lab per le classi. 

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Usare il collegamento di registrazione 
> * Connettersi alla macchina virtuale

## <a name="use-the-registration-link"></a>Usare il collegamento di registrazione

1. Passare all'**URL di registrazione** ricevuto dal docente. 
2. Accedere al servizio usando l'account dell'istituto di istruzione per completare la registrazione. 
3. Al termine della registrazione, verificare che le macchine virtuali per i lab a cui si ha accesso siano disponibili. 
2. Attendere che la macchina virtuale sia pronta e quindi **avviarla**. L'esecuzione di questo processo richiede tempo.  

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