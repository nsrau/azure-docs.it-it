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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: bc5c12d4bb92edaafcc9808da8c48106a6e0cbd5
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548036"
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

1. Passare a [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
2. Accedere al servizio con l'account utente usato per la registrazione al lab. 
3. Verificare che siano visualizzati tutti i lab a cui si ha accesso. 

    ![Visualizzare tutti i lab](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Connettersi alla macchina virtuale in un lab della classe

1. Avviare la macchina virtuale, se non è già stata avviata, selezionando **Avvia** nel riquadro. 
2. Selezionare **Connetti** sul riquadro che rappresenta la macchina virtuale del lab a cui si vuole accedere. 
3. Procedere in uno dei seguenti modi: 
   1. Per le macchine virtuali **Windows**, salvare il file **RDP** nel disco rigido. Aprire il file RDP per connettersi alla macchina virtuale. Usare il **nome utente** e la **password** forniti dal docente per accedere alla macchina virtuale. 
   3. Per le macchine virtuali **Linux**, copiare e salvare la stringa di connessione SSH nella finestra di dialogo **Connettersi alla macchina virtuale**. Usare questa stringa di connessione da un terminale SSH, ad esempio [Putty](https://www.putty.org/), per connettersi alla macchina virtuale.

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Arrestare la macchina virtuale in un lab della classe

Per arrestare la macchina virtuale, selezionare **Interrompi** nel riquadro. Quando la macchina virtuale viene arrestata, il pulsante **Avvia** sul riquadro diventa attivo. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
 