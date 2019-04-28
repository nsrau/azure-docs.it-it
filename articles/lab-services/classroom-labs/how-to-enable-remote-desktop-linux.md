---
title: Abilitare desktop remoto per Linux in Azure Lab Services | Microsoft Docs
description: Informazioni su come abilitare desktop remoto per macchine virtuali Linux in un lab in Azure Lab Services.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 6985bd0bbae858ad258e723ef4d6d6d687b2c86e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695467"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Abilitare e usare desktop remoto per macchine virtuali Linux in un lab in Azure Lab Services
Questo articolo illustra come eseguire le attività seguenti:

- Abilitare desktop remoto per VM Linux
- Modalità di connessione per docenti con il modello di macchina virtuale tramite connessione Desktop remoto (RDP).
- Modalità di connessione studenti per il macchina virtuale tramite RDP per studenti

## <a name="enable-remote-desktop-for-linux-vm"></a>Abilitare desktop remoto per VM Linux
Durante la creazione del lab, è possono abilitare gli insegnanti **connessione desktop remoto** per **Linux** immagini. Il **abilitare una connessione Desktop remoto** opzione viene visualizzata quando è selezionata un'immagine Linux per il modello. Quando questa opzione è abilitata, gli insegnanti possono connettersi al modello di macchina virtuale e studente VM tramite RDP (Remote Desktop). 

![Abilitare connessione desktop remoto per un'immagine Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

> [!IMPORTANT] 
> Abilitazione **connessione desktop remoto** consente di aprire solo le **RDP** porta nei computer Linux. È, i docenti, connettersi al computer Linux tramite SSH per la prima volta e installare i pacchetti RDP e interfaccia utente grafica in modo che sia possibile connettersi al computer Linux usando il protocollo RDP in un secondo momento. Quindi, si **pubblicare** l'immagine in modo che gli studenti possano RDP in per gli studenti le macchine virtuali Linux. 

## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Attualmente, la connessione desktop remoto è supportata per i sistemi operativi seguenti:

- openSUSE Leap 42.3
- 7.5 basata su centOS
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Gli insegnanti la connessione al modello di macchina virtuale tramite RDP
Gli insegnanti necessario connettersi al modello di macchina virtuale tramite SSH prima di tutto e installare i pacchetti RDP e interfaccia utente grafica su di esso. Quindi, i docenti possono usare la procedura seguente per connettersi alle macchine virtuali Linux tramite RDP: 

Viene visualizzato il **Desktop remoto** opzione per connettersi al modello di macchina virtuale al momento della creazione del lab. 

![Connettersi al modello tramite RDP al momento della creazione](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Viene visualizzato il **Desktop remoto** opzione sulla home page del lab dopo aver creato il lab e il modello di macchina virtuale viene avviata. Avviare il modello di macchina virtuale se non è già avviato. 

![Connettersi al modello tramite RDP dopo aver creato il lab](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Quando si seleziona il **RDP** opzione, scarica un file RDP. Si apre la connessione al computer Linux. 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Docenti la connessione a uno studente VM tramite RDP
Un proprietario del lab (docente) può connettersi a un macchina virtuale per studenti passando al **macchine virtuali** consente di visualizzare e selezionare il **connettersi** icona. In precedenza, è necessario insegnanti **pubblicare** l'immagine modello con RDP e interfaccia utente grafica di pacchetti installati su di esso. 

![Insegnanti ci si connette per il macchina virtuale per studenti](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

## <a name="students-connecting-to-the-student-vm"></a>Studenti ci si connette per il macchina virtuale per studenti
Studente può RDP in per le proprie macchine virtuali di Linux dopo il proprietario del lab (docente) **pubblica** il modello di macchina virtuale con RDP e interfaccia utente grafica pacchetti installato nel computer. Di seguito sono riportati i passaggi necessari: 

1. Quando uno studente esegue l'accesso al portale Labs direttamente (`http://labs.azure.com`) o tramite un collegamento di iscrizione (`http://labs.azure.com/register/<registrationCode>`), viene visualizzato un riquadro per ogni lab lo studente ha accesso a. 
2. Nel riquadro, selezionare **avviare** se la VM viene arrestata. 
3. Selezionare **Connessione**. Questa azione Scarica il file RDP al computer. Salvare il file e aprire per connettersi al computer Linux tramite RDP. 

    ![Scaricare RDP studente una macchina virtuale:](../media/how-to-enable-remote-desktop-linux/student-rdp-download.png)

    È comunque possibile connettersi alla VM Linux tramite SSH. Selezionare **... (puntini di sospensione)**  per visualizzare l'opzione di SSH. 
    
    ![Studente una macchina virtuale: SSH](../media/how-to-enable-remote-desktop-linux/student-ssh.png)

    Copiare e salvare la stringa di connessione SSH sul **Connetti a macchina virtuale** nella finestra di dialogo. Usare questa stringa di connessione da un terminale SSH, ad esempio [Putty](https://www.putty.org/), per connettersi alla macchina virtuale. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)

