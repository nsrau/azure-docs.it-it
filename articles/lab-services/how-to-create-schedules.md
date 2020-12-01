---
title: Creare una pianificazione per i Lab in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare pianificazioni per i Lab in Azure Lab Services in modo che le macchine virtuali nei Lab vengano avviate e arrestate a un orario specificato.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2a827c3d9f3022cb7d27ee43c9c95227c44f97e7
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434026"
---
# <a name="create-and-manage-schedules-for-labs-in-azure-lab-services"></a>Creare e gestire le pianificazioni per i Lab in Azure Lab Services 
Le pianificazioni consentono di configurare un lab per le classi in modo che le macchine virtuali nel lab vengano automaticamente avviate e arrestate a un orario specificato. È possibile definire una pianificazione occasionale o una pianificazione ricorrente. Le procedure seguenti illustrano i passaggi per creare e gestire le pianificazioni di lab per le classi: 

> [!IMPORTANT]
> Il tempo di esecuzione pianificato delle macchine virtuali non interferisce con la [quota assegnata a un utente](how-to-configure-student-usage.md#set-quotas-for-users). La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali. 

## <a name="set-a-schedule-for-the-lab"></a>Impostare una pianificazione per il lab
Creare un evento pianificato per il lab in modo che le VM al suo interno vengano automaticamente avviate/arrestate in specifici orari. La quota utente specificata in precedenza corrisponde al tempo aggiuntivo assegnato a ogni utente al di fuori del tempo pianificato. 

> [!NOTE]
> Prima di iniziare, ecco in che modo le pianificazioni influiscono sulle macchine virtuali del Lab: 
>- La macchina virtuale modello non è inclusa nelle pianificazioni. 
>- Vengono avviate solo le macchine virtuali assegnate. Ciò significa che se un computer non è richiesto da un utente finale (studente), il computer non verrà avviato nelle ore pianificate. 
>- Tutte le macchine virtuali (richieste da un utente o meno) vengono arrestate in base alla pianificazione del Lab. 

1. Passare alla pagina **Schedules** (Pianificazione) e selezionare **Add scheduled event** (Aggiungi evento pianificato) sulla barra degli strumenti. 

    ![Screenshot che mostra il Azure Lab Services pagina "Schedule" con il pulsante Aggiungi pianificazione selezionato.](./media/how-to-create-schedules/add-schedule-button.png)
2. Verificare che per **Event type** (Tipo di evento) sia selezionata l'opzione **Standard**. Selezionare **Start only** (Solo avvio) per specificare solo l'ora di avvio per le VM. Selezionare **Stop only** (Solo arresto) per specificare solo l'ora di arresto per le VM. 
7. Nella sezione **Repeat** (Ripeti) selezionare la pianificazione corrente. 

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](./media/how-to-create-schedules/select-current-schedule.png)
5. Nella finestra di dialogo **Repeat** (Ripeti) procedere come segue:
    1. Verificare che il campo **Repeat** (Ripeti) sia impostato su **every week** (ogni settimana). 
    3. Specificare la **data di inizio**.
    4. Specificare l'**ora di avvio** in cui si vuole che vengano avviate le VM.
    5. Specificare l'**ora di arresto** in cui devono essere arrestate le VM. 
    6. Specificare il **fuso orario** per le ore di avvio e di arresto specificate. 
    2. Selezionare i giorni in cui la pianificazione deve essere effettiva. Nell'esempio seguente sono selezionati i giorni compresi tra lunedì e giovedì. 
    8. Selezionare **Salva**. 

        ![Impostare la pianificazione ripetuta](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Ora nella pagina **Add scheduled event** (Aggiungi evento pianificato) immettere una descrizione per la pianificazione in **Notes (optional)** (Note - facoltativo). 
4. Nella pagina **Add scheduled event** (Aggiungi evento pianificato) selezionare **Save** (Salva). 

    ![Pianificazione settimanale](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Visualizzare le pianificazioni nel calendario
È possibile vedere le date e gli orari pianificati evidenziati nella visualizzazione del calendario, come illustrato nell'immagine seguente:

![Pianificazioni nella visualizzazione del calendario](./media/how-to-create-schedules/schedules-calendar.png)

Selezionare il pulsante **Oggi** nell'angolo in alto a destra per passare alla data corrente nel calendario. Selezionare la **freccia sinistra** per passare alla settimana precedente e la **freccia destra** per passare a quella successiva nel calendario. 

## <a name="edit-a-schedule"></a>Modificare pianificazione
Quando si seleziona una pianificazione evidenziata nel calendario, vengono visualizzati i pulsanti per **modificare** o **eliminare** la pianificazione. 

![Modificare la pagina della pianificazione](./media/how-to-create-schedules/schedule-edit-button.png)

Nella pagina **modifica evento pianificato** è possibile aggiornare la pianificazione e selezionare **Salva**. 

## <a name="delete-a-schedule"></a>Eliminare una pianificazione

1. Per eliminare una pianificazione, selezionare una pianificazione evidenziata nel calendario e selezionare il pulsante icona del cestino (Elimina):

    ![Pulsante Elimina sulla barra degli strumenti](./media/how-to-create-schedules/schedule-delete-button.png)
2. Nella finestra di dialogo **Elimina evento pianificato** selezionare **Sì** per confermare l'eliminazione. 



## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente del Lab, Access Labs](how-to-use-classroom-lab.md)
