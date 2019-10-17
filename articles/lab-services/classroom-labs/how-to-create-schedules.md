---
title: Creare la pianificazione dei lab per le classi in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare le pianificazioni dei lab per le classi in Azure Lab Services in modo che le macchine virtuali nei lab vengano avviate e arrestate a un orario specificato.
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
ms.openlocfilehash: 4e3cf302437c3e4954ac977ac3f4ff6b2021a760
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330621"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Creare e gestire le pianificazioni dei lab per le classi in Azure Lab Services 
Le pianificazioni consentono di configurare un lab per le classi in modo che le macchine virtuali nel lab vengano automaticamente avviate e arrestate a un orario specificato. È possibile definire una pianificazione occasionale o una pianificazione ricorrente. Le procedure seguenti illustrano i passaggi per creare e gestire le pianificazioni di lab per le classi: 

> [!IMPORTANT]
> Il tempo di esecuzione pianificato delle macchine virtuali non interferisce con la [quota assegnata a un utente](how-to-configure-student-usage.md#set-quotas-for-users). La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali. 

## <a name="set-a-schedule-for-the-lab"></a>Impostare una pianificazione per il lab
Creare un evento pianificato per il Lab in modo che le macchine virtuali nel Lab vengano avviate/interrotte automaticamente in momenti specifici. La quota utente specificata in precedenza corrisponde al tempo aggiuntivo assegnato a ogni utente al di fuori di questa ora pianificata. 

1. Passare alla pagina **pianificazioni** e selezionare **Aggiungi evento pianificato** sulla barra degli strumenti. 

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](../media/how-to-create-schedules/add-schedule-button.png)
2. Verificare che sia selezionato **standard** come **tipo di evento**. Selezionare **Avvia solo** per specificare solo l'ora di inizio per le macchine virtuali. Selezionare **Interrompi solo** per specificare solo l'ora di arresto per le macchine virtuali. 
7. Nella sezione **Ripeti** selezionare la pianificazione corrente. 

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](../media/how-to-create-schedules/select-current-schedule.png)
5. Nella finestra di dialogo **Ripeti** eseguire le operazioni seguenti:
    1. Verificare che **ogni settimana** sia impostata per il campo di **ripetizione** . 
    3. Specificare la **Data di inizio**.
    4. Specificare l' **ora di inizio** in cui si desidera che le macchine virtuali vengano avviate.
    5. Specificare l' **ora di arresto** in cui devono essere arrestate le macchine virtuali. 
    6. Specificare il **fuso orario** per le ore di inizio e di fine specificate. 
    2. Consente di selezionare i giorni in cui si desidera rendere effettiva la pianificazione. Nell'esempio seguente è selezionato Monday-Thursday. 
    8. Selezionare **Salva**. 

        ![Imposta pianificazione ripetizione](../media/how-to-create-schedules/set-repeat-schedule.png)

3. A questo punto, nella pagina **Aggiungi evento pianificato** , per **Note (facoltativo)** , immettere eventuali descrizioni o note per la pianificazione. 
4. Nella pagina **Aggiungi evento pianificato** selezionare **Salva**. 

    ![Pianificazione settimanale](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Visualizzare le pianificazioni nel calendario
È possibile vedere le date e gli orari pianificati evidenziati nella visualizzazione del calendario, come illustrato nell'immagine seguente:

![Pianificazioni nella visualizzazione del calendario](../media/how-to-create-schedules/schedules-calendar.png)

Selezionare il pulsante **Oggi** nell'angolo in alto a destra per passare alla data corrente nel calendario. Selezionare la **freccia sinistra** per passare alla settimana precedente e la **freccia destra** per passare a quella successiva nel calendario. 

## <a name="edit-a-schedule"></a>Modificare pianificazione
Quando si seleziona una pianificazione evidenziata nel calendario, vengono visualizzati i pulsanti per **modificare** o **eliminare** la pianificazione. 

![Modificare la pagina della pianificazione](../media/how-to-create-schedules/schedule-edit-button.png)

Nella pagina **modifica evento pianificato** è possibile aggiornare la pianificazione e selezionare **Salva**. 

## <a name="delete-a-schedule"></a>Eliminare una pianificazione

1. Per eliminare una pianificazione, selezionare una pianificazione evidenziata nel calendario e selezionare il pulsante icona del cestino (Elimina):

    ![Pulsante Elimina sulla barra degli strumenti](../media/how-to-create-schedules/schedule-delete-button.png)
2. Nella finestra di dialogo **Elimina evento pianificato** selezionare **Sì** per confermare l'eliminazione. 



## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
