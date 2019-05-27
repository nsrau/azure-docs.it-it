---
title: File di inclusione
description: File di inclusione
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 05/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 6711506c1e489dcbd50aedd36241affc3bbed80b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113397"
---
### <a name="policy-for-users-in-your-directory"></a>Criteri: Per gli utenti della directory

Seguire questa procedura se si desidera che i criteri per gli utenti e gruppi nella directory che può richiedere questo pacchetto di accesso.

1. Nel **gli utenti che possono richiedere l'accesso** sezione, selezionare **per gli utenti della directory**.

1. Nel **Seleziona utenti e gruppi** fare clic su **aggiungere gli utenti e gruppi**.

1. In Seleziona utenti e il riquadro gruppi, selezionare gli utenti e gruppi da aggiungere.

    ![Pacchetto di accesso - criteri di selezione utenti e gruppi](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Fare clic su **seleziona** per aggiungere utenti e gruppi.

1. Passare al [criteri: Richiedere](#policy-request) sezione.

### <a name="policy-for-users-not-in-your-directory"></a>Criteri: Per gli utenti non della directory

Seguire questa procedura se si desidera che i criteri per gli utenti non della directory che può richiedere questo pacchetto di accesso. Le directory devono essere configurate per essere consentito nel **restrizioni di collaborazione aziendale relazioni** impostazioni.

> [!NOTE]
> Per un utente verrà creato un account utente guest non ancora nella directory di cui richiesta è approvata o approvata automaticamente. Il guest sarà invitato, ma non riceverà un messaggio di posta elettronica di invito. Al contrario, riceveranno un messaggio di posta elettronica quando viene fornito l'assegnazione di pacchetti di accesso. Per impostazione predefinita, in seguito quando tale utente guest non duri più ha qualsiasi pacchetto assegnazioni di accesso, perché è scaduta o è stata annullata, l'ultima assegnazione che l'account utente verrà impedito l'accesso ed eliminato. Se si desidera disporre gli utenti guest rimangono nella directory a tempo indeterminato, anche se non dispongono di alcun le assegnazioni di accesso pacchetto, è possibile modificare le impostazioni per la configurazione di gestione dei diritti.

1. Nel **gli utenti che possono richiedere l'accesso** sezione, selezionare **per gli utenti non nella directory**.

1. Nel **directory selezionare Azure AD esterno** fare clic su **aggiungere directory**.

1. Immettere un nome di dominio e cercare un riferimento esterno directory di Azure AD.

1. Verificare che è la directory corretta per il nome della directory specificata e il dominio iniziale.

    > [!NOTE]
    > Tutti gli utenti dalla directory sarà in grado di richiedere questo pacchetto di accesso. Sono inclusi gli utenti da tutti i sottodomini associati alla directory, non solo il dominio usato nella ricerca.

    ![Pacchetto di accesso - criteri di selezione directory](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Fare clic su **Add** per aggiungere la directory.

1. Ripetere questo passaggio per aggiungere tutte le altre directory.

1. Dopo aver aggiunto tutte le directory si desidera includere nei criteri, fare clic su **seleziona**.

1. Passare al [criteri: Richiedere](#policy-request) sezione.

### <a name="policy-none-administrator-direct-assignments-only"></a>Criteri: Nessuno (amministratore assegnazioni dirette solo)

Seguire questa procedura se si desidera che i criteri per ignorare le richieste di accesso e consentire agli amministratori di assegnare direttamente a utenti specifici per il pacchetto di accesso. Non è necessario richiedere il pacchetto di accesso. È comunque possibile impostare le impostazioni di scadenza, ma nessuna impostazione richiesta.

1. Nel **gli utenti che possono richiedere l'accesso** sezione, selezionare **None (assegnazioni dirette di amministratore solo**.

    Dopo aver creato il pacchetto di accesso, è possibile assegnare direttamente utenti interni ed esterni specifici per il pacchetto di accesso. Se si specifica un utente esterno, un account utente guest verrà creato nella directory.

1. Passare al [criteri: Scadenza](#policy-expiration) sezione.

### <a name="policy-request"></a>Criteri: Richiesta

Nella sezione Request, specificare le impostazioni di approvazione quando gli utenti richiedono il pacchetto di accesso.

1. Per richiedere l'approvazione per le richieste da utenti selezionati, impostare il **richiedono l'approvazione** passare alla **Yes**. Per le richieste approvate automaticamente, impostare l'interruttore su **No**.

1. Se si richiedono l'approvazione, nelle **seleziona responsabili approvazione** fare clic su **aggiungere responsabili approvazione**.

1. Nel riquadro selezionare responsabili approvazione, selezionare uno o più utenti e/o gruppi come responsabili approvazione.

    Solo uno dei responsabili approvazione selezionati deve approvare una richiesta. Non è necessaria l'approvazione da tutti i responsabili. La decisione di approvazione si basa su qualsiasi responsabile approvazione esamina innanzitutto la richiesta.

    ![Pacchetto di accesso - criteri - selezionare responsabili approvazione](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Fare clic su **seleziona** aggiungere responsabili approvazione.

1. Fare clic su **Mostra impostazioni richiesta avanzate** per visualizzare le impostazioni aggiuntive.

    ![Pacchetto di accesso - criteri di selezione directory](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Per richiedere agli utenti di fornire una giustificazione per richiedere il pacchetto di accesso, impostare **Richiedi la giustificazione** al **Yes**.

1. Per richiedere al revisore di fornire una giustificazione per approvare una richiesta per il pacchetto di accesso, impostare **Richiedi la giustificazione responsabile approvazione** al **Yes**.

1. Nel **timeout della richiesta di approvazione (giorni)** , specificare la quantità di tempo sono responsabili dell'approvazione per una richiesta di revisione. Se nessun responsabili approvazione esaminarla in questo numero di giorni, la richiesta scade e l'utente dovrà inviare un'altra richiesta per il pacchetto di accesso.

### <a name="policy-expiration"></a>Criteri: Scadenza

Nella sezione scadenza, si specifica in cui scade l'assegnazione dell'utente per il pacchetto di accesso.

1. Nel **scadenza** sezione, impostare **pacchetto di accesso scade** al **data**, **numero di giorni**, o **mai**.

    Per la **data**, selezionare una data di scadenza nel futuro.

    Per la **numero di giorni**, specificare un numero compreso tra 0 e 3660 giorni.

    In base alla selezione, assegnazione di un utente per il pacchetto di accesso scade in una determinata data, un determinato numero di giorni dopo essere stati approvati, o mai.

1. Fare clic su **Mostra impostazioni di scadenza avanzate** per visualizzare le impostazioni aggiuntive.

1. Per consentire di estendere le assegnazioni utente, impostare **consentire agli utenti di estendere l'accesso** al **Yes**.

    Se le estensioni sono consentite nei criteri, l'utente riceverà un messaggio di posta elettronica 14 giorni e 1 giorno prima che l'assegnazione di pacchetti di accesso viene impostato su una scadenza chiederle anche estendere l'assegnazione.

    ![Pacchetto di accesso - impostazioni di scadenza dei criteri:](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Criteri: Attiva criterio

1. Se si desidera che il pacchetto di accesso da rendere immediatamente disponibili per gli utenti nei criteri, fare clic su **Sì** per abilitare i criteri.

    È sempre possibile abilitarla in futuro dopo aver creato il pacchetto di accesso.

    ![Pacchetto di accesso - impostazione dei criteri di abilitazione criteri](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Fare clic su **successivo** oppure **creare**.
