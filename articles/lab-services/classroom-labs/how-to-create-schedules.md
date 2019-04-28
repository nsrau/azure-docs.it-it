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
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695995"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Creare e gestire le pianificazioni dei lab per le classi in Azure Lab Services 
Le pianificazioni consentono di configurare un lab per le classi in modo che le macchine virtuali nel lab vengano automaticamente avviate e arrestate a un orario specificato. È possibile definire una pianificazione occasionale o una pianificazione ricorrente. Le procedure seguenti illustrano i passaggi per creare e gestire le pianificazioni di lab per le classi: 

> [!IMPORTANT]
> Il tempo di esecuzione pianificato delle macchine virtuali non interferisce con la [quota assegnata a un utente](how-to-configure-student-usage.md#set-quotas-per-user). La quota è relativa al periodo di tempo non compreso nelle ore di pianificazione trascorso da uno studente sulle macchine virtuali. 

## <a name="add-a-schedule-once"></a>Aggiungere una pianificazione (occasionale)

1. Passare alla pagina **Pianificazioni** e selezionare **Aggiungi pianificazione** sulla barra degli strumenti. 

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](../media/how-to-create-schedules/add-schedule-button.png)
2. Nella pagina **Aggiungi pianificazione** verificare che sia selezionata l'opzione **Una sola volta** nella parte superiore. Se necessario, selezionare **Una sola volta**. 
3. In **Schedule date (required)** (Data pianificazione - obbligatoria) immettere la data o selezionare l'icona del calendario per selezionare una data. 
4. In **Ora di inizio** selezionare l'ora in cui le macchine virtuali devono essere avviate. L'ora di inizio è obbligatoria se non è impostata l'ora di arresto. Selezionare **Remove start event** (Rimuovi evento di avvio) per specificare solo l'ora di arresto. Se l'**ora di inizio** è disabilitata, selezionare **Add start event** (Aggiungi evento di avvio) accanto all'elenco a discesa per abilitarla. 
5. In **Ora di arresto** selezionare l'ora in cui le macchine virtuali devono arrestate. L'ora di arresto è obbligatoria se non è impostata l'ora di inizio. Selezionare **Remove stop event** (Rimuovi evento di arresto) per specificare solo l'ora di arresto. Se l'**ora di arresto** è disabilitata, selezionare **Add stop event** (Aggiungi evento di arresto) accanto all'elenco a discesa per abilitarla.
6. In **Time zone (required)** (Fuso orario - obbligatorio) selezionare il fuso orario per le ore di inizio e di arresto specificate. 
7. In **Note** immettere la descrizione o le note per la pianificazione. 
8. Selezionare **Salva**. 

    ![Pianificazione occasionale](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Aggiungere una pianificazione ricorrente (settimanale)

1. Passare alla pagina **Pianificazioni** e selezionare **Aggiungi pianificazione** sulla barra degli strumenti. 

    ![Pulsante Aggiungi pianificazione nella pagina Pianificazioni](../media/how-to-create-schedules/add-schedule-button.png)
2. Nella pagina **Aggiungi pianificazione** passare a **Settimanale** nella parte superiore. 
3. In **Schedule days (required)** (Giorni pianificazione - obbligatori) selezionare i giorni in cui la pianificazione deve essere effettiva. Nell'esempio seguente sono selezionati i giorni compresi tra lunedì e venerdì. 
4. Nel campo **Da** immettere la **data di inizio della pianificazione** o selezionare una data facendo clic sul pulsante del **calendario**. Questo campo è obbligatorio. 
5. In **Schedule end date** (Data fine pianificazione) immettere o selezionare una data di fine in cui arrestare le macchine virtuali. 
6. In **Ora di inizio** selezionare l'ora in cui le macchine virtuali devono essere avviate. L'ora di inizio è obbligatoria se non è impostata l'ora di arresto. Selezionare **Remove start event** (Rimuovi evento di avvio) per specificare solo l'ora di arresto. Se l'**ora di inizio** è disabilitata, selezionare **Add start event** (Aggiungi evento di avvio) accanto all'elenco a discesa per abilitarla. 
7. In **Ora di arresto** selezionare l'ora in cui le macchine virtuali devono arrestate. L'ora di arresto è obbligatoria se non è impostata l'ora di inizio. Selezionare **Remove stop event** (Rimuovi evento di arresto) per specificare solo l'ora di arresto. Se l'**ora di arresto** è disabilitata, selezionare **Add stop event** (Aggiungi evento di arresto) accanto all'elenco a discesa per abilitarla.
8. In **Time zone (required)** (Fuso orario - obbligatorio) selezionare il fuso orario per le ore di inizio e di arresto specificate.  
9. In **Note** immettere la descrizione o le note per la pianificazione. 
10. Selezionare **Salva**. 

    ![Pianificazione settimanale](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Visualizzare le pianificazioni nel calendario
È possibile vedere le date e gli orari pianificati evidenziati nella visualizzazione del calendario, come illustrato nell'immagine seguente:

![Pianificazioni nella visualizzazione del calendario](../media/how-to-create-schedules/schedules-in-calendar.png)

Selezionare il pulsante **Oggi** nell'angolo in alto a destra per passare alla data corrente nel calendario. Selezionare la **freccia sinistra** per passare alla settimana precedente e la **freccia destra** per passare a quella successiva nel calendario. 

## <a name="edit-a-schedule"></a>Modificare pianificazione
Quando si fa doppio clic su una pianificazione evidenziata nel calendario o si seleziona il pulsante con la **matita** sulla barra degli strumenti, viene visualizzata la pagina **Modifica pianificazione**. L'aggiornamento delle impostazioni in questa pagina corrisponde a quello delle impostazioni nella pagina **Aggiungi pianificazione** descritto nella sezione [Aggiungere una pianificazione ricorrente](#add-a-recurring-schedule-weekly). 

![Modificare la pagina della pianificazione](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Eliminare una pianificazione

1. Per eliminare una pianificazione, selezionare il pulsante con il cestino (Elimina) sulla barra degli strumenti, come illustrato nell'immagine seguente:

    ![Pulsante Elimina sulla barra degli strumenti](../media/how-to-create-schedules/delete-schedule-button.png)

    È possibile usare il pulsante **Elimina** per tutte le date e ore pianificate nel calendario. 
2. Nella pagina **Delete schedules** (Elimina pianificazioni) selezionare **Sì**.

    ![Conferma dell'eliminazione di una pianificazione](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire account lab come amministratore](how-to-manage-lab-accounts.md)
- [Creare e gestire lab come proprietario](how-to-manage-classroom-labs.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)
