---
title: Istruttore che accede alle macchine virtuali in Azure Lab Services
description: Questo articolo illustra in che modo gli insegnanti possono accedere alle VM degli studenti dalla visualizzazione Instructor. Un assistente didattico, ad esempio, può essere un insegnante per una classe, ma uno studente per altre classi.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641940"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Accedere alle macchine virtuali come studente dalla visualizzazione Instructor
Questo articolo illustra in che modo i docenti possono accedere alle proprie macchine virtuali per le classi che partecipano come studenti. 

Di seguito è riportato uno scenario in cui questa funzionalità sarà utile. Un assistente didattico è un insegnante per una classe, ma uno studente in altre classi. L'insegnante docente desidera visualizzare e accedere alle macchine virtuali degli studenti dalla visualizzazione Instructor che mostra i Lab di cui sono proprietari. 

## <a name="access-vms-from-instructor-view"></a>Accedere alle macchine virtuali dalla visualizzazione Instructor

1. Accedere al [sito web Azure Lab Services](https://labs.azure.com). Vengono visualizzati i Lab di cui si è proprietari. Questi Lab possono essere laboratori creati dall'utente o dai Lab assegnati come proprietario. Per ulteriori informazioni, vedere [come aggiungere altri proprietari a un lab esistente](how-to-add-user-lab-owner.md)
2. Per accedere alle macchine virtuali per le classi che vengono frequentate come studente, selezionare l'icona del computer nell'angolo superiore destro. Verificare che vengano visualizzate le VM a cui è possibile accedere come studente. Nell'esempio seguente l'utente è un assistente didattico per Python Lab, ma uno studente del Lab Java. Quindi, l'utente Visualizza la macchina virtuale dal Lab Java nell'elenco a discesa. L'utente può avviare la macchina virtuale e connettersi a essa. 
    
    ![Accedere alle VM degli studenti](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Connettersi a una macchina virtuale](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Connettersi a una macchina virtuale usando RDP in un Mac](connect-virtual-machine-mac-rdp.md)
- [Usare desktop remoto per le macchine virtuali Linux](how-to-use-remote-desktop-linux-student.md)
