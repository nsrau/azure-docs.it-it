---
title: Abilitazione del desktop remoto per Linux in Azure Lab Services Documenti Microsoft
description: Informazioni su come abilitare il desktop remoto per le macchine virtuali Linux in un lab in Azure Lab Services.Learn how to enable remote desktop for Linux virtual machines in a lab in Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270862"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Abilitare il desktop remoto per le macchine virtuali Linux in un lab in Azure Lab ServicesEnable remote desktop for Linux virtual machines in a lab in Azure Lab Services
In questo articolo viene illustrato come eseguire le attività seguenti:This article shows you how to do the following tasks:

- Abilitare il desktop remoto per la macchina virtuale LinuxEnable remote desktop for Linux VM
- Come l'insegnante può connettersi alla macchina virtuale modello tramite Connessione desktop remoto (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Abilitare il desktop remoto per la macchina virtuale LinuxEnable remote desktop for Linux VM
Durante la creazione del laboratorio, gli insegnanti possono abilitare la connessione desktop remoto per le immagini Linux.During lab creation, teachers can enable **remote desktop connection** for **Linux** images. L'opzione **Abilita connessione Desktop remoto** viene visualizzata quando viene selezionata un'immagine Linux per il modello. Quando questa opzione è abilitata, gli insegnanti possono connettersi alle macchine virtuali del modello VM e studenti tramite RDP (Desktop remoto). 

![Abilitare la connessione desktop remoto per un'immagine LinuxEnable remote desktop connection for a Linux image](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Nella finestra di messaggio **Abilita connessione Desktop remoto** selezionare Continua con Desktop **remoto**. 

![Abilitare la connessione desktop remoto per un'immagine LinuxEnable remote desktop connection for a Linux image](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> L'abilitazione della **connessione desktop remoto** apre solo la porta **RDP** sui computer Linux. Se RDP è già installato e configurato nell'immagine della macchina virtuale (ad esempio: immagine della macchina virtuale Ubuntu Data Science), è possibile connettersi alle macchine virtuali tramite RDP senza seguire altri passaggi.
> 
> Se l'immagine della macchina virtuale non dispone di RDP installato e configurato, è necessario connettersi al computer Linux utilizzando SSH per la prima volta e installare i pacchetti RDP e GUI in modo che l'utente/studente possa connettersi al computer Linux utilizzando RDP in un secondo momento. Per altre informazioni, vedere [Installare e configurare Desktop remoto per la connessione a una macchina virtuale Linux in Azure.For](../../virtual-machines/linux/use-remote-desktop.md)more information, see Install and configure Remote Desktop to connect to a Linux VM in Azure. Quindi, si pubblica l'immagine in modo che gli studenti possono RDP per le macchine virtuali Linux studente. 

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Attualmente, la connessione desktop remoto è supportata per i seguenti sistemi operativi:

- openSUSE Leap 42.3
- Basato su CentOS 7.5
- Debian 9 "Allunga"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Connettersi alla macchina virtuale modello 
Gli insegnanti devono connettersi prima alla macchina virtuale del modello usando SSH e installare i pacchetti RDP e GUI su di essa. Quindi, gli insegnanti possono usare RDP per connettersi alla macchina virtuale del modello:Then, the teachers can use RDP to connect to the template VM: 

1. Se sulla barra degli strumenti è visualizzato **Personalizza modello,** selezionarlo. Quindi, selezionare **Continua** nella finestra di dialogo **Personalizza modello.** Questa azione avvia la macchina virtuale del modello.  

    ![Personalizzare il modello](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Dopo aver avviato la macchina virtuale del modello, è possibile selezionare **Connetti modello** e quindi **Connetti tramite SSH** sulla barra degli strumenti. 

    ![Connettersi al modello tramite RDP dopo la creazione del labConnect to template via RDP after the lab is created](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Viene visualizzato il seguente Connettersi alla finestra di dialogo **della macchina virtuale.** Selezionare il pulsante **Copia** accanto alla casella di testo per copiarla negli Appunti. Salvare la stringa di connessione SSH. Usare questa stringa di connessione da un terminale SSH, ad esempio [Putty](https://www.putty.org/), per connettersi alla macchina virtuale.
 
    ![Stringa di connessione SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Installare i pacchetti RDP e GUI in modo che l'utente/studente possa connettersi alla macchina Linux utilizzando RDP in un secondo momento. Per altre informazioni, vedere [Installare e configurare Desktop remoto per la connessione a una macchina virtuale Linux in Azure.For](../../virtual-machines/linux/use-remote-desktop.md)more information, see Install and configure Remote Desktop to connect to a Linux VM in Azure. Quindi, si pubblica l'immagine in modo che gli studenti possono RDP per le macchine virtuali Linux studente.
5. Dopo aver installato questi pacchetti, è possibile usare il **modello Connetti a** sulla barra degli strumenti e quindi selezionare Connetti **tramite RDP** per connettersi alla macchina virtuale modello tramite RDP. Salvare il file RDP e usarlo per connettersi alla macchina virtuale modello tramite RDP. 

## <a name="next-steps"></a>Passaggi successivi
Dopo che un istruttore ha abilitato la funzionalità di connessione desktop remoto, gli studenti possono connettersi alle proprie macchine virtuali tramite RDP/SSH. Per altre informazioni, vedere [Usare desktop remoto per macchine virtuali Linux in un laboratorio](how-to-use-remote-desktop-linux-student.md)di classe. 