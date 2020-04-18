---
title: Accesso con istruttore in Azure Lab Services
description: Questo articolo illustra come gli istruttori possono accedere alle macchine virtuali degli studenti dalla visualizzazione istruttore. Ad esempio, un assistente didattico può essere un istruttore per una classe, ma uno studente per altre classi.
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641940"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Accedere alle macchine virtuali come studente dalla visualizzazione istruttore
Questo articolo illustra come gli istruttori possono accedere alle macchine virtuali per i corsi frequentati da studenti. 

Di seguito è riportato uno scenario in cui questa funzionalità sarà utile. Un assistente docente è un istruttore per una classe, ma uno studente in altre classi. Inoltre, l'istruttore di insegnamento desidera visualizzare e accedere alle macchine virtuali degli studenti dalla visualizzazione dell'istruttore che mostra i lab di cui è proprietario. 

## <a name="access-vms-from-instructor-view"></a>Accedere alle macchine virtuali dalla visualizzazione istruttore

1. Accedere al sito Web di [Servizi di laboratorio di Azure](https://labs.azure.com). Vedi i laboratori che possiedi. Questi lab possono essere laboratori creati dall'utente o dai lab assegnati dall'amministratore all'utente come proprietario. Per altre informazioni, vedere [Come aggiungere altri proprietari a un lab esistenteFor more information,](how-to-add-user-lab-owner.md) see How to add additional owners to an existing lab
2. Per accedere alle macchine virtuali per i corsi frequentati come studente, selezionare l'icona del computer nell'angolo in alto a destra. Verificare che vengano visualizzate le macchine virtuali a cui è possibile accedere come studente. Nell'esempio seguente, l'utente è un assistente didattico per il laboratorio Python, ma uno studente del laboratorio Java. Pertanto, l'utente vede la macchina virtuale dal lab Java nell'elenco a discesa. L'utente può avviare la macchina virtuale e connettersi a essa. 
    
    ![Accedere alle macchine virtuali degli studenti](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Connettersi a una macchina virtuale](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Connettersi a una macchina virtuale usando RDP in un Mac](connect-virtual-machine-mac-rdp.md)
- [Usare il desktop remoto per le macchine virtuali LinuxUse remote desktop for Linux virtual machines](how-to-use-remote-desktop-linux-student.md)
