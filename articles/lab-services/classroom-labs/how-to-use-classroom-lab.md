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
ms.date: 11/15/2018
ms.author: spelluru
ms.openlocfilehash: b5a55a35dc4baacf8248adad3133220214050eeb
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706688"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Come accedere a un lab per le classi in Azure Lab Services
Questo articolo descrive come accedere a un lab per le classi, connettersi alla macchina virtuale nel lab e arrestare la macchina virtuale. 

## <a name="register-to-a-lab"></a>Registrarsi a un lab
1. Passare all'**URL di registrazione** ricevuto dal docente. 
2. Accedere al servizio usando l'account dell'istituto di istruzione per completare la registrazione. 
3. Al termine della registrazione, verificare che le macchine virtuali per i lab a cui si ha accesso siano disponibili. 
2. Attendere che la macchina virtuale sia pronta e quindi **avviarla**. L'esecuzione di questo processo richiede tempo.  

    ![Avviare la VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Visualizzare tutti i lab della classe
Dopo la registrazione ai lab, è possibile visualizzare tutti i lab per le classi attenendosi alla procedura seguente: 

1. Passare a [https://labs.azure.com](https://labs.azure.com). 
2. Accedere al servizio con l'account utente usato per la registrazione al lab. 
3. Verificare che siano visualizzati tutti i lab a cui si ha accesso. 

    ![Visualizzare tutti i lab](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Connettersi alla macchina virtuale in un lab della classe

1. Avviare la macchina virtuale, se non è già stata avviata, selezionando **Avvia**.
2. Selezionare **Connetti** sul riquadro che rappresenta la macchina virtuale del lab a cui si vuole accedere. 

    ![Visualizzare tutti i lab](../media/how-to-use-classroom-lab/connect-button.png)
3. Salvare il file RDP (per la macchina virtuale Windows) nel disco rigido e aprirlo. 
4. Usare **nome utente** e **password** ricevuti dal docente per accedere alla macchina virtuale. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Arrestare la macchina virtuale in un lab della classe

Per arrestare la macchina virtuale, selezionare **Interrompi** nel riquadro. Quando la macchina virtuale viene arrestata, il pulsante **Avvia** sul riquadro diventa attivo. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
 