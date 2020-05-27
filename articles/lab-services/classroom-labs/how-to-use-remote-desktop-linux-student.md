---
title: Usare Desktop remoto per Linux in Azure Lab Services | Microsoft Docs
description: Informazioni su come usare Desktop remoto per le macchine virtuali Linux in un lab Azure Lab Services.
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
ms.openlocfilehash: 8ccad0698ea6560dd183cacc71f5f3a644e8220c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588106"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Usare Desktop remoto per le macchine virtuali Linux in un lab per le classi di Azure Lab Services
Questo articolo spiega in che modo gli studenti possono connettersi a una macchina virtuale (VM) Linux in un lab usando RDP/SSH. 

Per consentire agli studenti di connettersi alla macchina virtuale del lab per le classi, un insegnante deve abilitare la funzionalità di connessione Desktop remoto. Per istruzioni su come un insegnante può abilitare la funzionalità di connessione Desktop remoto, vedere [Abilitare Desktop remoto per le macchine virtuali Linux](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Se si abilita la **connessione Desktop remoto**, nei computer Linux viene aperta solo la porta **RDP**. La prima volta, l'insegnante può connettersi al computer Linux usando SSH, quindi può installare RDP e i pacchetti GUI per consentire in seguito la connessione al computer Linux tramite RDP. 

## <a name="connect-to-the-student-vm"></a>Connettersi alla macchina virtuale degli studenti
Dopo che il proprietario del lab (educatore) ha **pubblicato** la macchina virtuale modello e ha installato RDP e i pacchetti GUI, gli studenti possono accedere con RDP alle proprie macchine virtuali Linux. I passaggi necessari sono i seguenti: 

1. Quando uno studente accede al portale Labs direttamente (`https://labs.azure.com`) o usando un collegamento di registrazione (`https://labs.azure.com/register/<registrationCode>`), viene visualizzato un riquadro per ogni lab a cui lo studente può accedere. 
2. Impostare il pulsante sul riquadro in modo da avviare la macchina virtuale quando si trova in stato di arresto. 
3. Selezionare **Connetti**. Sono disponibili due opzioni per connettersi alla macchina virtuale: **SSH** e **Desktop remoto**.

    ![Macchina virtuale dello studente: opzioni di connessione](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Connettersi tramite SSH o RDP
Se si seleziona l'opzione **SSH**, viene visualizzata la finestra di dialogo **Connetti alla macchina virtuale**:  

![Stringa di connessione SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selezionare il pulsante **Copia** accanto alla casella di testo per copiarlo negli Appunti. Salvare la stringa di connessione SSH. Usare questa stringa di connessione da un terminale SSH, ad esempio [Putty](https://www.putty.org/), per connettersi alla macchina virtuale.

Se si seleziona l'opzione **RDP**, viene scaricato un file RDP nel computer. Salvare il file e aprirlo per connettersi al computer. 

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni su come abilitare la funzionalità di connessione Desktop remoto in macchine virtuali Linux, vedere [Abilitare Desktop remoto per le macchine virtuali Linux](how-to-enable-remote-desktop-linux.md). 

