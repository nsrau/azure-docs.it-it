---
title: Abilitare Desktop remoto per Linux in Azure Lab Services | Microsoft Docs
description: Informazioni su come abilitare Desktop remoto per le macchine virtuali Linux in un lab Azure Lab Services.
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
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a40575340fc5b1c202be6b001807085954439f03
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588157"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Abilitare Desktop remoto per le macchine virtuali Linux in un lab Azure Lab Services
Questo articolo illustra come eseguire le attività seguenti:

- Abilitare Desktop remoto per macchine virtuali Linux
- Informazioni su come un docente può connettersi alla macchina virtuale modello tramite Connessione Desktop remoto (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Abilitare Desktop remoto per macchine virtuali Linux
Durante la creazione del lab, i docenti possono abilitare **Connessione Desktop remoto** per le immagini **Linux**. L'opzione **Abilita Connessione Desktop remoto** viene visualizzata quando si seleziona un'immagine Linux per il modello. Quando questa opzione è abilitata, i docenti possono connettersi alle macchine virtuali del modello e a quelle degli studenti tramite RDP (Desktop remoto). 

![Abilitare Connessione desktop remoto per un'immagine Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Nella finestra di messaggio **Abilitazione di Connessione Desktop remoto** selezionare **Continua con Desktop remoto**. 

![Abilitare Connessione desktop remoto per un'immagine Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Se si abilita la **connessione Desktop remoto**, nei computer Linux viene aperta solo la porta **RDP**. Se il protocollo RDP è già installato e configurato nell'immagine della macchina virtuale, gli studenti e il docente possono connettersi alle macchine virtuali tramite RDP senza seguire altri passaggi.
> 
> Se l'immagine della macchina virtuale non ha il protocollo RDP installato e configurato, è possibile connettersi al computer Linux la prima volta usando SSH, quindi installare RDP e i pacchetti GUI per consentire in seguito la connessione al computer Linux tramite RDP. Per altre informazioni, vedere [Installare e configurare Desktop remoto per connettersi a una VM Linux di Azure](../../virtual-machines/linux/use-remote-desktop.md). Dopodiché, pubblicare l'immagine in modo che gli studenti possano usare il protocollo RDP nelle loro macchine virtuali Linux. 

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Attualmente, la connessione Desktop remoto è supportata per i sistemi operativi seguenti:

- openSUSE Leap 42.3
- Basato su CentOS 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Connettersi alla macchina virtuale modello 
I docenti devono prima connettersi alla macchina virtuale modello usando SSH e installare i pacchetti RDP e GUI su di essa. Dopodiché, possono usare il protocollo RDP per connettersi alla macchina virtuale modello: 

1. Se si visualizza **Personalizza modello** sulla barra degli strumenti, selezionarlo. Selezionare quindi **Continua** nella finestra di dialogo **Personalizza modello**. Questa azione avvia la macchina virtuale modello.  

    ![Personalizzare il modello](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Dopo l'avvio della macchina virtuale modello, è possibile selezionare **Connetti modello** e poi **Connettiti tramite SSH** sulla barra degli strumenti. 

    ![Connettersi al modello tramite RDP dopo la creazione del lab](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Si visualizza la finestra di dialogo **Connetti alla tua macchina virtuale** seguente. Selezionare il pulsante **Copia** accanto alla casella di testo per copiarlo negli Appunti. Salvare la stringa di connessione SSH. Usare questa stringa di connessione da un terminale SSH, ad esempio [Putty](https://www.putty.org/), per connettersi alla macchina virtuale.
 
    ![Stringa di connessione SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Installare i pacchetti RDP e GUI in modo che gli studenti possano connettersi al computer Linux usando RDP in un secondo momento. Per altre informazioni, vedere [Installare e configurare Desktop remoto per connettersi a una VM Linux di Azure](../../virtual-machines/linux/use-remote-desktop.md). Dopodiché, pubblicare l'immagine in modo che gli studenti possano usare il protocollo RDP nelle loro macchine virtuali Linux.
5. Dopo l'installazione di questi pacchetti, è possibile usare l'opzione **Connettiti al modello** sulla barra degli strumenti e quindi selezionare **Connettiti tramite RDP** per connettersi alla macchina virtuale modello tramite RDP. Salvare il file RDP e usarlo per connettersi alla macchina virtuale modello tramite RDP. 

## <a name="next-steps"></a>Passaggi successivi
Dopo l'abilitazione della funzionalità di connessione Desktop remoto da parte di un docente, gli studenti possono connettersi alle loro macchine virtuali tramite RDP/SSH. Per altre informazioni, vedere [Usare Desktop remoto per macchine virtuali Linux in un lab per le classi](how-to-use-remote-desktop-linux-student.md). 