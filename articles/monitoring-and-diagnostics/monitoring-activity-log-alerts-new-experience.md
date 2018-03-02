---
title: "Creare gli avvisi del log attività e gestirli con la nuova esperienza di Avvisi (anteprima) in Monitoraggio di Azure | Microsoft Docs"
description: "Questo articolo fornisce informazioni su come creare gli avvisi del log attività dalla scheda Avvisi (anteprima) in Monitoraggio di Azure. Questo articolo illustra in dettaglio la nuova esperienza utente per questa funzionalità."
author: JYOTHIRMAISURI
manager: vvithal
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: 
ms.openlocfilehash: afdd82617c47f0dee22c229feba87bdf79b90a69
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-preview-experience"></a>Creare gli avvisi del log attività con la nuova esperienza Avvisi (anteprima)

Gli avvisi del log attività vengono attivati quando si verifica un nuovo evento del log attività che corrisponde alle condizioni specificate nell'avviso.

Si tratta di avvisi per le risorse di Azure e possono essere creati usando un modello di Azure Resource Manager. Possono essere create, aggiornate o eliminate anche nel portale di Azure. In questo articolo vengono presentati i concetti alla base degli avvisi del log attività. Questo articolo illustra come usare il portale di Azure per configurare un avviso sugli eventi del log attività tramite la nuova esperienza in [Avvisi di Azure (anteprima)](monitoring-overview-unified-alerts.md).

In genere si creano avvisi del log attività per ricevere una notifica quando si verificano modifiche specifiche nelle risorse presenti nella sottoscrizione di Azure. Questi avvisi sono spesso limitati a risorse o gruppi di risorse specifici. Ad esempio è possibile ricevere una notifica quando una macchina virtuale nel gruppo di risorse di esempio **myProductionResourceGroup** viene eliminata oppure quando nuovi ruoli vengono assegnati a un utente nella sottoscrizione.

È possibile configurare un avviso del log attività in base a qualsiasi proprietà di primo livello nell'oggetto JSON per un evento del log attività. Tuttavia, nel portale vengono visualizzate le opzioni più comuni, come descritto in dettaglio nelle sezioni seguenti:

>[!NOTE]

> Se la categoria è impostata su "Amministrativo", nell'avviso è necessario specificare almeno uno dei criteri precedenti. Non è possibile creare un avviso che viene attivato ogni volta che si crea un evento nei log attività.
>

Quando un avviso del log di attività viene attivato, usa un gruppo di azione per generare azioni o notifiche. Un gruppo di azione è un set riutilizzabile di ricevitori di notifica, ad esempio gli indirizzi di posta elettronica, gli URL webhook o i numeri di telefono di SMS. Più avvisi possono fare riferimento ai ricevitori per centralizzare e raggruppare i canali di notifica. Quando si definisce l'avviso del log di attività, sono disponibili due opzioni. È possibile:

* Usare un gruppo di azione esistente nell'avviso del log attività.
* Creare un nuovo gruppo di azione.

Per altre informazioni sui gruppi di azione, vedere [Creare e gestire gruppi di azione nel portale di Azure](monitoring-action-groups.md).

Per altre informazioni sulle notifiche sull'integrità del servizio, vedere [Ricevere gli avvisi del log attività per le notifiche sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).


## <a name="whats-new-in-alerts-preview-for-activity-logs"></a>Novità dell'anteprima di Avvisi per i log di attività.

[Avvisi di Azure (anteprima)](monitoring-overview-unified-alerts.md) offre ora una migliore esperienza utente per gli avvisi del log attività. Con l'[esperienza utente migliorata per gli avvisi](monitoring-overview-unified-alerts.md), è ora possibile:

- [Creare](#create-an-alert-rule-for-an-activity-log) e [gestire](#view-and-manage-activity-log-alert-rules) le regole degli avvisi del log attività, da **Monitoraggio** > **Avvisi (anteprima)**. Altre informazioni sui [log attività](monitoring-overview-activity-logs.md).

- **Nuove opzioni per la destinazione degli avvisi**: durante la creazione di una nuova regola di avviso del log attività, è ora possibile selezionare una risorsa, un gruppo di risorse o una sottoscrizione di destinazione.


## <a name="create-an-alert-rule-for-an-activity-log"></a>Creare una regola di avviso per un log attività

> [!NOTE]

>  Quando si creano le regole di avviso, verificare quanto segue:

> - La sottoscrizione nell'ambito non deve essere diversa dalla sottoscrizione in cui viene creato l'avviso.
- I criteri devono essere livello/stato/chiamante/gruppo di risorse/id risorsa/tipo di risorsa o categoria di eventi in cui è stato configurato l'avviso.
- Non sono presenti condizioni "anyOf" o condizioni nidificate nella configurazione degli avvisi JSON (in pratica, è consentito un solo allOf senza ulteriori allOf/anyOf).


Utilizzare la procedura seguente:

1. Nel portale di Azure selezionare **Monitoraggio** > **Avvisi (anteprima).**
2. Fare clic su **Nuova regola di avviso** in cima alla finestra **Avvisi (anteprima)**.

     ![nuova regola di avviso](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     Viene visualizzata la finestra **Crea regola**.

      ![nuove opzioni delle regole di avviso](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. Nella condizione **Definisci avviso** fornire le informazioni seguenti e fare clic su **Fatto**.

    - **Destinazione avviso**: per visualizzare e selezionare la destinazione per il nuovo avviso, usare **Filtra per sottoscrizione** / **Filtra per tipo di risorsa** e selezionare la risorsa o un gruppo di risorse nell'elenco visualizzato.

    > [!NOTE]

    > è possibile selezionare una risorsa, un gruppo di risorse o un'intera sottoscrizione.

    **Visualizzazione di esempio di destinazione di avviso**

     ![Selezionare la destinazione](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - In **Criteri di destinazione, fare clic su **Aggiungi criteri** selezionare il tipo di segnale come **Log attività**.

    - Selezionare il segnale nell'elenco visualizzato.

    È possibile selezionare la sequenza temporale della cronologia di log e la logica degli avvisi corrispondente per il segnale di destinazione:

    **Schermata Aggiungi criteri**

    ![aggiungi criteri](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Periodo cronologia**: ultime 12/6/24 ore, ultima settimana.

    **Logica avvisi**:

        - **Event Level**- The severity level of the event.**Verbose,Informational, Warning, Error**, or **Critical**.
        - **Status**: The status of the event.**Started, Failed**, or **Succeeded**.
        - **Event initiated by**: Also known as the caller; The email address or Azure Active Directory identifier of the user who performed the operation.

        **Sample signal graph with alert logic applied** :

        ![ criteria selected](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. Nella **definizione delle regole di avviso** fornire i dettagli seguenti:

    - **Nome regola di avviso**: nome della nuova regola di avviso
    - **Descrizione**: descrizione della nuova regola di avviso
    - **Salva avviso nel gruppo di risorse**: selezionare il gruppo di risorse in cui salvare la nuova regola.

5. In **Gruppo di azioni**, specificare il gruppo di azioni da assegnare a questa nuova regola di avviso dal menu a discesa. In alternativa, [creare un nuovo gruppo di azioni](monitoring-action-groups.md) e assegnarlo alla nuova regola. Per creare un nuovo gruppo, fare clic su **+ Nuovo gruppo**.

6. Per abilitare le regole dopo averle create, fare clic su **Sì** per **Abilita regola alla creazione**.
7. Fare clic su **Crea regola di avviso**.

    Viene creata la nuova regola di avviso per il log attività e viene visualizzato un messaggio di conferma nella parte superiore destra della finestra.

    ![ regola di avviso creata](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    È possibile abilitare, disabilitare, modificare o eliminare una regola. [Altre informazioni](#view-and-manage-activity-log-alert-rules) sulla gestione delle regole del log attività.

## <a name="view-and-manage-activity-log-alert-rules"></a>Visualizzare e gestire le regole di avviso del log attività

1. Nel portale di Azure fare clic su **Monitoraggio** > **Avvisi (anteprima)** e quindi su **Gestisci le regole** nella parte superiore sinistra della finestra.

    ![ gestione delle regole di avviso](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    Viene visualizzato l'elenco delle regole disponibili.

2. Cercare la regola del log attività da modificare.

    ![ cercare le regole di avviso del log attività](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    È possibile usare i filtri disponibili: **Sottoscrizione**, **Gruppo di risorse** o **Risorsa** per trovare la regola dell'attività che si vuole modificare.

    > [!NOTE]

    > È possibile modificare solo la **Descrizione**, i **Criteri di destinazione** e i **Gruppi di azioni**.

3.  Selezionare la regola e fare doppio clic per modificare le opzioni della regola. Apportare le modifiche necessarie e quindi fare clic su **Salva**.

    ![ gestione delle regole di avviso](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  È possibile disabilitare, abilitare o eliminare una regola. Selezionare l'opzione appropriata nella parte superiore della finestra, dopo aver selezionato la regola come descritto nel passaggio 2.


## <a name="next-steps"></a>Passaggi successivi

- [Archiviare gli avvisi del log attività](monitoring-archive-activity-log.md)
- [Trasmettere log di attività a Hub eventi](monitoring-stream-activity-logs-event-hubs.md)
- [Eseguire la migrazione nei log attività](monitoring-migrate-management-alerts.md)
