---
title: Creare pianificazioni Azure Lab Services all'interno di Team
description: Informazioni su come creare pianificazioni di servizi Lab in team.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042338"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Creare e gestire pianificazioni di servizi Lab nei team

Le pianificazioni consentono di configurare un lab per le classi in modo che le macchine virtuali nel lab vengano automaticamente avviate e arrestate a un orario specificato. È possibile definire una pianificazione occasionale o una pianificazione ricorrente. Le procedure seguenti illustrano i passaggi per creare e gestire le pianificazioni di lab per le classi: 

Ecco in che modo le pianificazioni influiscono sulle macchine virtuali del Lab: 

- La macchina virtuale modello non è inclusa nelle pianificazioni. 
- Vengono avviate solo le macchine virtuali assegnate. Ciò significa che se un computer non è richiesto da un utente finale (studente), il computer non verrà avviato nelle ore pianificate. 
- Tutte le macchine virtuali (richieste da un utente o meno) vengono arrestate in base alla pianificazione del Lab. 

> [!IMPORTANT]
> Il tempo di esecuzione pianificato delle macchine virtuali non interferisce con la quota assegnata a un utente. La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali. 

Gli utenti possono creare, modificare ed eliminare le pianificazioni dei Lab nei team come nel [sito Web Labs](https://labs.azure.com). Vedere l'articolo sulla [creazione e la gestione delle pianificazioni](how-to-create-schedules-within-teams.md).

## <a name="automatic-shutdown-and-disconnect-settings"></a>Impostazioni di chiusura e disconnessione automatiche

È possibile abilitare diverse funzionalità di controllo dei costi di AutoShutdown per prevenire in modo proattivo i costi aggiuntivi quando le macchine virtuali non vengono usate attivamente. La combinazione delle tre funzionalità di arresto e disconnessione automatiche rileva la maggior parte dei casi in cui gli utenti lasciano accidentalmente le macchine virtuali in esecuzione:
 
- Disconnettere automaticamente gli utenti dalle macchine virtuali ritenute inattive dal sistema operativo.
- Arresta automaticamente le macchine virtuali quando gli utenti si disconnettono.
- Arrestare automaticamente le macchine virtuali avviate, ma gli utenti non si connettono.

Per altri dettagli, vedere l'articolo sulla [configurazione delle impostazioni di arresto automatico per un Lab](how-to-enable-shutdown-disconnect.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Usare Azure Lab Services in teams Overview](lab-services-within-teams-overview.md)
- [Introduzione e creazione di un Lab nei team](how-to-get-started-create-lab-within-teams.md)
- [Gestire gli elenchi degli utenti del Lab nei team](how-to-manage-user-lists-within-teams.md)
- [Gestisci il pool di macchine virtuali del Lab in team](how-to-manage-vm-pool-within-teams.md)
- [Accedere a una macchina virtuale all'interno di teams-visualizzazione studente](how-to-access-vm-for-students-within-teams.md)
