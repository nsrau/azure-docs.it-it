---
title: Come accedere a un lab per le classi in Azure Lab Services | Microsoft Docs
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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 3a24c826ffdd3e368b6b16f6e78fbafc50c54b1e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Come accedere a un lab per le classi in Azure Lab Services
Questo articolo descrive come accedere a un lab per le classi, connettersi alla macchina virtuale nel lab e arrestare la macchina virtuale. 

## <a name="view-all-the-classroom-labs"></a>Visualizzare tutti i lab della classe

1. Passare all'**URL di registrazione** ricevuto dal docente. 
2. Accedere al servizio usando l'account dell'istituto di istruzione per completare la registrazione. 
3. Al termine della registrazione, verificare che le macchine virtuali per i lab a cui si ha accesso siano disponibili. 

    ![Visualizzare tutti i lab](./media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Connettersi alla macchina virtuale in un lab della classe

1. Selezionare **Connetti** sul riquadro che rappresenta la macchina virtuale del lab a cui si vuole accedere.

    ![Visualizzare tutti i lab](./media/how-to-use-classroom-lab/connect-button.png)
2. Sono necessari alcuni minuti prima che la macchina virtuale sia pronta.

    ![Preparazione della macchina virtuale](./media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. Salvare il file RDP nel disco rigido e aprirlo. 
    
    ![Salvare il file RDP](./media/how-to-use-classroom-lab/save-rdp-file.png)
4. Usare **nome utente** e **password** ricevuti dal docente per accedere alla macchina virtuale. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Arrestare la macchina virtuale in un lab della classe

Selezionare **Arresta** sul riquadro che rappresenta il lab della classe. Quando la macchina virtuale viene arrestata, il pulsante **Avvia** sul riquadro diventa attivo. 

## <a name="next-steps"></a>Passaggi successivi
Introduzione alla configurazione di un lab usando Azure Lab Services:

- [Configurare un lab per le classi](how-to-manage-classroom-labs.md)
- [Configurare un lab personalizzato](tutorial-create-custom-lab.md)

 