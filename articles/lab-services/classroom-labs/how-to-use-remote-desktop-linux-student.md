---
title: Usare desktop remoto per Linux in Azure Lab Services | Microsoft Docs
description: Informazioni su come usare desktop remoto per le macchine virtuali Linux in un Lab in Azure Lab Services.
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
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585087"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Usare desktop remoto per le macchine virtuali Linux in un Lab della classe Azure Lab Services
Questo articolo illustra in che modo gli studenti possono connettersi a una macchina virtuale (VM) Linux in un Lab usando RDP/SSH. 

Un insegnante deve abilitare la funzionalità di connessione Desktop remoto prima che gli studenti possano connettersi alla macchina virtuale del Lab della classe. Per istruzioni su come un insegnante può abilitare la funzionalità di connessione Desktop remoto, vedere [Enable Remote Desktop for Linux Virtual Machines](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> L'abilitazione di **Connessione desktop remoto** apre solo la porta **RDP** nei computer Linux. Un insegnante può connettersi al computer Linux usando SSH per la prima volta e installare i pacchetti RDP e GUI per potersi connettere al computer Linux usando RDP in un secondo momento. 

## <a name="connect-to-the-student-vm"></a>Connettersi alla macchina virtuale per studenti
Gli studenti possono usare il protocollo RDP nelle VM Linux dopo che il proprietario del Lab (docente/Docente) **pubblica** la VM modello con i pacchetti RDP e GUI installati nel computer. Di seguito sono riportati i passaggi necessari: 

1. Quando uno studente accede direttamente al portale Labs (`https://labs.azure.com`) o usando un collegamento di registrazione (`https://labs.azure.com/register/<registrationCode>`), viene visualizzato un riquadro per ogni Lab a cui lo studente può accedere. 
2. Sul riquadro, impostare il pulsante per avviare la VM se è in stato interrotto. 
3. Selezionare **Connessione**. Vengono visualizzate due opzioni per connettersi alla macchina virtuale: **SSH** e **Desktop remoto**.

    ![VM studente-opzioni di connessione](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Connettersi tramite SSH o RDP
Se si seleziona l'opzione **SSH** , viene visualizzata la finestra **di dialogo Connetti alla macchina virtuale** seguente:  

![Stringa di connessione SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selezionare il pulsante **copia** accanto alla casella di testo per copiarlo negli Appunti. Salvare la stringa di connessione SSH. Usare questa stringa di connessione da un terminale SSH, ad esempio [Putty](https://www.putty.org/), per connettersi alla macchina virtuale.

Se si seleziona l'opzione **RDP** , nel computer viene scaricato un file RDP. Salvarlo e aprirlo per connettersi al computer. 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come abilitare la funzionalità di connessione Desktop remoto per le VM Linux in un Lab della classe, vedere [abilitare Desktop remoto per le macchine virtuali Linux](how-to-enable-remote-desktop-linux.md). 

