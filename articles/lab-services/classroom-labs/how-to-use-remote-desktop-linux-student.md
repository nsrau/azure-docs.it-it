---
title: Usare il desktop remoto per Linux in Azure Lab Services . Documenti Microsoft
description: Informazioni su come usare il desktop remoto per le macchine virtuali Linux in un lab in Azure Lab Services.Learn how to use remote desktop for Linux virtual machines in a lab in Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585087"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Usare il desktop remoto per le macchine virtuali Linux in un laboratorio di classe di Azure Lab ServicesUse remote desktop for Linux virtual machines in a classroom lab of Azure Lab Services
Questo articolo illustra come gli studenti possono connettersi a una macchina virtuale (VM) Linux in un lab usando RDP/SSH. 

Un istruttore deve abilitare la funzionalità di connessione desktop remoto prima che gli studenti possano connettersi alla macchina virtuale del laboratorio di classe. Per istruzioni su come un istruttore può abilitare la funzionalità di connessione desktop remoto, vedere [Abilitare il desktop remoto per le macchine virtuali Linux.](how-to-enable-remote-desktop-linux.md)

> [!IMPORTANT] 
> L'abilitazione della **connessione desktop remoto** apre solo la porta **RDP** sui computer Linux. Un istruttore può connettersi alla macchina Linux utilizzando SSH per la prima volta e installare i pacchetti RDP e GUI in modo che sia possibile connettersi alla macchina Linux utilizzando RDP in un secondo momento. 

## <a name="connect-to-the-student-vm"></a>Connettersi alla macchina virtuale studenteConnect to the student VM
Gli studenti possono RDP nelle loro macchine virtuali Linux dopo che il proprietario del lab (insegnante/professore) **pubblica** la macchina virtuale del modello con i pacchetti RDP e GUI installati nel computer. I passaggi necessari sono i seguenti: 

1. Quando uno studente accede direttamente al`https://labs.azure.com`portale Labs ( )`https://labs.azure.com/register/<registrationCode>`o utilizzando un collegamento di registrazione ( ), viene visualizzato un riquadro per ogni lab a cui lo studente ha accesso. 
2. Nel riquadro attivare o disattivare il pulsante per avviare la macchina virtuale se è in stato di arresto. 
3. Selezionare **Connetti**. Vengono visualizzate due opzioni per connettersi alla macchina virtuale: **SSH** e **Desktop remoto**.

    ![VM studente - opzioni di connessione](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Connettersi utilizzando SSH o RDP
Se si seleziona l'opzione **SSH,** viene visualizzata la finestra di dialogo Connetti alla macchina virtuale seguente:If you select the SSH option, you see the following **Connect to your virtual machine** dialog box:  

![Stringa di connessione SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selezionare il pulsante **Copia** accanto alla casella di testo per copiarla negli Appunti. Salvare la stringa di connessione SSH. Usare questa stringa di connessione da un terminale SSH, ad esempio [Putty](https://www.putty.org/), per connettersi alla macchina virtuale.

Se si seleziona l'opzione **RDP,** viene scaricato un file RDP nel computer. Salvarlo e aprirlo per connettersi alla macchina. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come abilitare la funzionalità di connessione desktop remoto per le macchine virtuali Linux in un lab di classe, vedere [Abilitare il desktop remoto per le macchine virtuali Linux.](how-to-enable-remote-desktop-linux.md) 

