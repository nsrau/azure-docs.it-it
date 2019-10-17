---
title: Abilitare Desktop remoto per Linux in Azure Lab Services | Microsoft Docs
description: Informazioni su come abilitare Desktop remoto per le macchine virtuali Linux in un Lab in Azure Lab Services.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 65a77b8243e7afc8d858360d3d3be86f44e6b67e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332189"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Abilitare Desktop remoto per le macchine virtuali Linux in un Lab in Azure Lab Services
Questo articolo illustra come eseguire le attività seguenti:

- Abilitare Desktop remoto per la macchina virtuale Linux
- Il modo in cui l'insegnante può connettersi alla macchina virtuale modello tramite Connessione Desktop remoto (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Abilitare Desktop remoto per la macchina virtuale Linux
Durante la creazione del Lab, gli insegnanti possono abilitare la **Connessione desktop remoto** per le immagini **Linux** . L'opzione **abilita connessione Desktop remoto** viene visualizzata quando si seleziona un'immagine Linux per il modello. Quando questa opzione è abilitata, gli insegnanti possono connettersi alle VM del modello e alle VM degli studenti tramite RDP (Desktop remoto). 

![Abilitare la connessione Desktop remoto per un'immagine Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Nella finestra di messaggio **Abilitazione di connessione Desktop remoto** selezionare **continua con desktop remoto**. 

![Abilitare la connessione Desktop remoto per un'immagine Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> L'abilitazione di **Connessione desktop remoto** apre solo la porta **RDP** nei computer Linux. Se RDP è già installato e configurato nell'immagine della macchina virtuale (ad esempio, Ubuntu Data Science Virtual Machine Image), gli studenti possono connettersi alle macchine virtuali tramite RDP senza seguire ulteriori passaggi.
> 
> Se l'immagine di macchina virtuale non dispone di RDP installato e configurato, è necessario connettersi al computer Linux usando SSH per la prima volta e installare i pacchetti RDP e GUI in modo che gli studenti possano connettersi al computer Linux usando RDP in un secondo momento. Per altre informazioni, vedere [installare e configurare Desktop remoto per connettersi a una macchina virtuale Linux in Azure](../../virtual-machines/linux/use-remote-desktop.md). Quindi, si pubblica l'immagine in modo che gli studenti possano usare il protocollo RDP per le macchine virtuali Linux per studenti. 

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Attualmente, la connessione Desktop remoto è supportata per i sistemi operativi seguenti:

- openSUSE Leap 42.3
- Basato su CentOS 7,5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Connettersi alla macchina virtuale modello 
Gli insegnanti devono connettersi prima alla macchina virtuale del modello usando SSH e installare i pacchetti RDP e GUI. Quindi, gli insegnanti possono usare RDP per connettersi alla macchina virtuale modello: 

1. Se viene visualizzato **Personalizza modello** sulla barra degli strumenti, selezionarlo. Selezionare quindi **continua** nella finestra di dialogo **Personalizza modello** . Questa azione avvia la macchina virtuale del modello.  

    ![Personalizzare il modello](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Dopo l'avvio della macchina virtuale modello, è possibile selezionare **Connetti modello** e quindi **connettersi tramite SSH** sulla barra degli strumenti. 

    ![Connettersi al modello tramite RDP dopo la creazione del Lab](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Viene visualizzata la finestra **di dialogo Connetti alla macchina virtuale** . Selezionare il pulsante **copia** accanto alla casella di testo per copiarlo negli Appunti. Salvare la stringa di connessione SSH. Usare questa stringa di connessione da un terminale SSH, ad esempio [Putty](https://www.putty.org/), per connettersi alla macchina virtuale.
 
    ![Stringa di connessione SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Installare i pacchetti RDP e GUI in modo che gli studenti possano connettersi al computer Linux usando RDP in un secondo momento. Per altre informazioni, vedere [installare e configurare Desktop remoto per connettersi a una macchina virtuale Linux in Azure](../../virtual-machines/linux/use-remote-desktop.md). Quindi, si pubblica l'immagine in modo che gli studenti possano usare il protocollo RDP per le macchine virtuali Linux per studenti.
5. Dopo l'installazione di questi pacchetti, è possibile usare il **modello Connetti a** sulla barra degli strumenti e quindi selezionare **Connetti tramite RDP** per connettersi alla macchina virtuale modello tramite RDP. Salvare il file RDP e usarlo per connettersi alla macchina virtuale modello tramite RDP. 

## <a name="next-steps"></a>Passaggi successivi
Dopo che un insegnante ha abilitato la funzionalità di connessione Desktop remoto, gli studenti possono connettersi alle macchine virtuali tramite RDP/SSH. Per altre informazioni, vedere [usare desktop remoto per macchine virtuali Linux in un Lab della classe](how-to-use-remote-desktop-linux-student.md). 