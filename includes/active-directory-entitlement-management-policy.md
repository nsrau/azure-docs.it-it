---
title: File di inclusione
description: File di inclusione
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 07/31/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 154d71c9cbc109834a5854b46c3e6584dcefa7eb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968888"
---
### <a name="policy-for-users-in-your-directory"></a>Criteri: Per gli utenti nella directory

Seguire questa procedura se si vuole che i criteri siano destinati agli utenti nella directory che possono richiedere questo pacchetto di accesso.  Gli **utenti nella directory** si riferiscono sia a utenti interni che a utenti esterni che sono stati precedentemente invitati alla directory, attraverso di essi la richiesta di una gestione dei diritti con un altro pacchetto di accesso o l'invito a Azure ad B2B. Quando si definiscono i criteri, è possibile specificare singoli utenti o più gruppi di utenti in genere. Ad esempio, è possibile che l'organizzazione disponga già di un gruppo come **tutti i dipendenti**.  Se tale gruppo viene aggiunto nei criteri per gli utenti che possono richiedere l'accesso, qualsiasi membro del gruppo potrà richiedere l'accesso.

1. Nella sezione **utenti che possono richiedere l'accesso** selezionare **per gli utenti nella directory**.

    Si noti che **per gli utenti nell'** impostazione della directory sono inclusi sia gli utenti membri che gli utenti Guest aggiunti alla directory. Se si desidera includere solo utenti membri e non utenti guest, selezionare **per gli utenti nella directory** e quindi selezionare un gruppo di utenti membri. Se necessario, è possibile creare un gruppo dinamico di utenti membro (User. userType-EQ "Member"). Per ulteriori informazioni, vedere [regole di appartenenza dinamiche per i gruppi in Azure Active Directory](../articles/active-directory/users-groups-roles/groups-dynamic-membership.md).

1. Nella sezione **Seleziona utenti e gruppi** fare clic su **Aggiungi utenti e gruppi**.

1. Nel riquadro Seleziona utenti e gruppi selezionare gli utenti e i gruppi che si desidera aggiungere.

    ![Accedere ai criteri dei pacchetti-selezionare utenti e gruppi](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Fare clic su **Seleziona** per aggiungere gli utenti e i gruppi.

1. Passare al [criterio: Sezione](#policy-request) della richiesta.

### <a name="policy-for-users-not-in-your-directory"></a>Criteri: Per gli utenti che non si trovino nella directory

Seguire questa procedura se si vuole che il criterio sia per gli utenti che non si trova nella directory che possono richiedere questo pacchetto di accesso. Gli **utenti non inclusi nella directory** fanno riferimento agli utenti che si trovano in un'altra directory Azure ad e potrebbero non essere ancora stati invitati alla directory. Attualmente, è possibile aggiungere utenti solo da organizzazioni con Azure AD. Le directory devono essere configurate per essere consentite nelle impostazioni di **restrizioni di collaborazione delle relazioni organizzative** .

> [!NOTE]
> Verrà creato un account utente esterno Guest per un utente non ancora presente nella directory la cui richiesta è approvata o approvata automaticamente. Il Guest verrà invitato ma non riceverà un messaggio di posta elettronica di invito. Riceveranno invece un messaggio di posta elettronica quando viene recapitata l'assegnazione del pacchetto di accesso. Per impostazione predefinita, in un secondo momento, quando tale utente Guest non dispone più di assegnazioni di pacchetti di accesso, perché l'ultima assegnazione è scaduta o annullata, l'account utente guest verrà bloccato dall'accesso e successivamente eliminato. Se si desidera che gli utenti Guest rimangano per la directory a tempo indeterminato, anche se non dispongono di assegnazioni di pacchetti di accesso, è possibile modificare le impostazioni per la configurazione di gestione dei diritti.

1. Nella sezione **utenti che possono richiedere l'accesso** selezionare **per gli utenti non presenti nella directory**.

1. Nella sezione **selezionare la directory Azure ad esterna** fare clic su **Aggiungi directory**.

1. Immettere un nome di dominio e cercare una directory Azure AD con tale nome di dominio.

1. Verificare che sia la directory corretta in base al nome della directory e al dominio iniziale specificati.

    > [!NOTE]
    > Tutti gli utenti della directory saranno in grado di richiedere questo pacchetto di accesso. Sono inclusi gli utenti di tutti i sottodomini associati alla directory, non solo il dominio utilizzato nella ricerca.

    ![Accedere al pacchetto-criteri-selezionare le directory](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Fare clic su **Aggiungi** per aggiungere la directory.

1. Ripetere questo passaggio per aggiungere altre directory.

1. Dopo aver aggiunto tutte le directory che si desidera includere nel criterio, fare clic su **Seleziona**.

1. Passare al [criterio: Sezione](#policy-request) della richiesta.

### <a name="policy-none-administrator-direct-assignments-only"></a>Criteri: Nessuno (solo assegnazioni dirette all'amministratore)

Seguire questa procedura se si vuole che i criteri ignorino le richieste di accesso e consentano agli amministratori di assegnare direttamente utenti specifici al pacchetto di accesso. Gli utenti non dovranno richiedere il pacchetto di accesso. È comunque possibile impostare le impostazioni di scadenza, ma non sono disponibili impostazioni della richiesta.

1. Nella sezione **utenti che possono richiedere l'accesso** selezionare **nessuno (solo assegnazioni dirette all'amministratore**).

    Dopo aver creato il pacchetto di accesso, è possibile assegnare direttamente utenti interni ed esterni specifici al pacchetto di accesso. Se si specifica un utente esterno, nella directory verrà creato un account utente Guest.

1. Passare al [criterio: Sezione](#policy-expiration) relativa alla scadenza.

### <a name="policy-request"></a>Criteri: Richiesta

Nella sezione richiesta specificare le impostazioni di approvazione quando gli utenti richiedono il pacchetto di accesso.

1. Per richiedere l'approvazione per le richieste degli utenti selezionati, impostare l'interruttore **Richiedi approvazione** su **Sì**. Per approvare automaticamente le richieste, impostare l'interruttore su **No**.

1. Se è richiesta l'approvazione, nella sezione **Seleziona responsabili approvazione** fare clic su **Aggiungi responsabili approvazione**.

1. Nel riquadro Seleziona responsabili approvazione selezionare uno o più utenti e/o gruppi da approvatori.

    Solo uno dei responsabili approvazione selezionati deve approvare una richiesta. L'approvazione da tutti i responsabili approvazione non è obbligatoria. La decisione di approvazione si basa su qualsiasi revisore che esamina prima la richiesta.

    ![Accedi ai criteri di pacchetto-seleziona responsabili approvazione](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Fare clic su **Seleziona** per aggiungere i responsabili approvazione.

1. Fare clic su **Mostra impostazioni richieste avanzate** per visualizzare le impostazioni aggiuntive.

    ![Accedere al pacchetto-criteri-selezionare le directory](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Per richiedere agli utenti di fornire una giustificazione per richiedere il pacchetto di accesso, impostare **Richiedi giustificazione** su **Sì**.

1. Per richiedere al responsabile approvazione di fornire una giustificazione per approvare una richiesta per il pacchetto di accesso, impostare **Richiedi giustificazione del responsabile approvazione** su **Sì**.

1. Nella casella **timeout richiesta di approvazione (giorni)** specificare la quantità di tempo per cui i responsabili approvazione devono esaminare una richiesta. Se nessuno dei responsabili approvazione li esamina in questo numero di giorni, la richiesta scade e l'utente dovrà inviare un'altra richiesta per il pacchetto di accesso.

### <a name="policy-expiration"></a>Criteri: Scadenza

Nella sezione relativa alla scadenza specificare la scadenza dell'assegnazione di un utente al pacchetto di accesso.

1. Nella sezione **scadenza** , impostare il **pacchetto di accesso scade** a **Data**, **numero di giorni**o **mai**.

    Per la **Data**di scadenza, selezionare una data di scadenza in futuro.

    Per **numero di giorni**, specificare un numero compreso tra 0 e 3660 giorni.

    In base alla selezione, l'assegnazione di un utente al pacchetto di accesso scade in una determinata data, un determinato numero di giorni dopo l'approvazione o mai.

1. Fare clic su **Mostra impostazioni di scadenza avanzate** per visualizzare le impostazioni aggiuntive.

1. Per consentire all'utente di estendere le assegnazioni, impostare **Consenti agli utenti di estendere l'accesso** a **Sì**.

    Se nel criterio sono consentite le estensioni, l'utente riceverà un messaggio di posta elettronica 14 giorni e anche 1 giorno prima che l'assegnazione del pacchetto di accesso sia impostata in modo da scadere per richiedere l'estensione dell'assegnazione.

    ![Accedere ai criteri di pacchetto-impostazioni di scadenza](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Criteri: Attiva criterio

1. Se si vuole che il pacchetto di accesso venga reso immediatamente disponibile per gli utenti nel criterio, fare clic su **Sì** per abilitare il criterio.

    È sempre possibile abilitarla in futuro dopo aver completato la creazione del pacchetto di accesso.

    ![Pacchetto di accesso-criteri-Abilita impostazione dei criteri](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Fare clic su **Avanti** o su **Crea**.
