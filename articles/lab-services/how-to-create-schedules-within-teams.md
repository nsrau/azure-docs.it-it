---
title: Creare pianificazioni Azure Lab Services all'interno di Team
description: Informazioni su come creare pianificazioni di servizi Lab in team.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 5578a2f63a4d6f58998a54560299a4356ed65997
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946751"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Creare e gestire pianificazioni di servizi Lab nei team

Le pianificazioni consentono di configurare un lab per le classi in modo che le macchine virtuali nel lab vengano automaticamente avviate e arrestate a un orario specificato. È possibile definire una pianificazione occasionale o una pianificazione ricorrente. Le procedure seguenti illustrano i passaggi per creare e gestire le pianificazioni di lab per le classi: 

Ecco in che modo le pianificazioni influiscono sulle macchine virtuali del Lab: 

- La macchina virtuale modello non è inclusa nelle pianificazioni. 
- Vengono avviate solo le macchine virtuali assegnate. Ciò significa che se un computer non è richiesto da un utente finale (studente), il computer non verrà avviato nelle ore pianificate. 
- Tutte le macchine virtuali (richieste da un utente o meno) vengono arrestate in base alla pianificazione del Lab. 

> [!IMPORTANT]
> Il tempo di esecuzione pianificato delle macchine virtuali non interferisce con la quota assegnata a un utente. La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali. 

## <a name="createeditdelete-a-schedule-for-the-lab"></a>Creazione/modifica/eliminazione di una pianificazione per il Lab

Creare un evento pianificato per il lab in modo che le VM al suo interno vengano automaticamente avviate/arrestate in specifici orari. La quota utente specificata in precedenza corrisponde al tempo aggiuntivo assegnato a ogni utente al di fuori del tempo pianificato. 

1. Passare alla pagina **pianificazioni** e selezionare **Aggiungi evento pianificato** sulla barra degli strumenti (in alto a sinistra della finestra). 
1. Impostare i parametri seguenti per la pianificazione:
    1. Verificare che per **Event type** (Tipo di evento) sia selezionata l'opzione **Standard**. Selezionare **Start only** (Solo avvio) per specificare solo l'ora di avvio per le VM. Selezionare **Stop only** (Solo arresto) per specificare solo l'ora di arresto per le VM. 
    1. Specificare la **data di inizio**.
    1. Specificare l'**ora di avvio** in cui si vuole che vengano avviate le VM.
    1. Specificare l'**ora di arresto** in cui devono essere arrestate le VM. 
    1. Specificare il **fuso orario** per le ore di avvio e di arresto specificate. 
    1. Nella sezione **Ripeti** selezionare **ogni settimana** o **mai**. 
    1. Per **Note (facoltativo)** immettere una descrizione o una nota per la pianificazione. 
1. Fare clic su **Save**. 

### <a name="view-schedules-in-calendar"></a>Visualizzare le pianificazioni nel calendario

È possibile visualizzare le date e le ore pianificate evidenziate nel calendario. Selezionare il pulsante **Oggi** nell'angolo in alto a destra per passare alla data corrente nel calendario. Selezionare la **freccia sinistra** per passare alla settimana precedente e la **freccia destra** per passare a quella successiva nel calendario. 

### <a name="edit-a-schedule"></a>Modificare pianificazione

Quando si seleziona una pianificazione evidenziata nel calendario, vengono visualizzati i pulsanti per **modificare** o **eliminare** la pianificazione. 

Nella pagina **modifica evento pianificato** è possibile aggiornare la pianificazione e selezionare **Salva**. 

### <a name="delete-a-schedule"></a>Eliminare una pianificazione

1. Per eliminare una pianificazione, selezionare una pianificazione evidenziata nel calendario e selezionare il pulsante icona del cestino (Elimina):
1. Nella finestra di dialogo **Elimina evento pianificato** selezionare **Sì** per confermare l'eliminazione. 

## <a name="automatic-shutdown-and-disconnect-settings"></a>Impostazioni di chiusura e disconnessione automatiche

Nella parte superiore della pagina si noterà un collegamento alle impostazioni di **arresto** automatico.

È possibile abilitare diverse funzionalità di controllo dei costi di AutoShutdown per prevenire in modo proattivo i costi aggiuntivi quando le macchine virtuali non vengono usate attivamente. La combinazione delle tre funzionalità di arresto e disconnessione automatiche rileva la maggior parte dei casi in cui gli utenti lasciano accidentalmente le macchine virtuali in esecuzione:
 
- Disconnettere automaticamente gli utenti dalle macchine virtuali ritenute inattive dal sistema operativo.
- Arresta automaticamente le macchine virtuali quando gli utenti si disconnettono.
- Arrestare automaticamente le macchine virtuali avviate, ma gli utenti non si connettono.

Per ulteriori informazioni, fare clic sull'icona *informazioni* accanto a opzioni nelle impostazioni.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Usare Azure Lab Services in teams Overview](lab-services-within-teams-overview.md)
- [Per iniziare, creare un Lab di Lab Services dai team](how-to-get-started-create-lab-within-teams.md)
- [Gestire gli elenchi utenti di Lab Services dai team](how-to-manage-user-lists-within-teams.md)
- [Gestire un pool di macchine virtuali in Lab Services dai team](how-to-manage-vm-pool-within-teams.md)
- [Accedere a una macchina virtuale (visualizzazione studente) in Lab Services dai team](how-to-access-vm-for-students-within-teams.md)