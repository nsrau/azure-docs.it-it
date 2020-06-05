---
title: Docente che accede alle macchine virtuali in Azure Lab Services
description: Questo articolo illustra in che modo i docenti possono accedere alle macchine virtuali degli studenti dalla visualizzazione docente. Un assistente docente può, ad esempio, essere un docente di un corso e uno studente di altri corsi.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 4b4e6bf7b69f1b4c7664a104a0af70d9e486f08a
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699951"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>Accedere alle macchine virtuali come studente dalla visualizzazione docente
Questo articolo illustra in che modo i docenti possono accedere alle proprie macchine virtuali per i corsi a cui partecipano come studenti. 

Ecco uno scenario in cui questa funzionalità risulta utile. Un assistente docente è un docente di un corso e uno studente di altri corsi e vuole visualizzare e accedere alle macchine virtuali degli studenti dalla visualizzazione docente che mostra i lab di cui è proprietario. 

## <a name="access-vms-from-educator-view"></a>Accedere alle macchine virtuali dalla visualizzazione docente

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). Vengono visualizzati i lab di cui si è proprietari. Questi lab possono essere lab creati personalmente o lab assegnati dall'amministratore come proprietario. Per altre informazioni, vedere [Come aggiungere altri proprietari a un lab esistente](how-to-add-user-lab-owner.md).
2. Per accedere alle macchine virtuali per i corsi a cui si partecipa come studente, selezionare l'icona del computer nell'angolo in alto a destra. Verificare che vengano visualizzate le macchine virtuali a cui è possibile accedere come studente. Nell'esempio seguente l'utente è un assistente docente per il lab Python e uno studente del lab Java, di conseguenza visualizza la macchina virtuale dal lab Java nell'elenco a discesa e può avviarla e connettersi a essa. 
    
    ![Accedere alle macchine virtuali degli studenti](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Connettersi a una macchina virtuale](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Connettersi a una macchina virtuale usando RDP in un Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Connettersi a una macchina virtuale usando RDP in un Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)
- [Usare Desktop remoto per le macchine virtuali Linux](how-to-use-remote-desktop-linux-student.md)
