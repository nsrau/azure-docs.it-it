---
title: file di inclusione
description: file di inclusione
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: aba3dd34383edbfb555adcc2063e4a2b76af1959
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389384"
---
## <a name="for-users-in-your-directory"></a>Per gli utenti nella directory

Seguire questa procedura se si desidera consentire agli utenti della directory di richiedere questo pacchetto di accesso. Quando si definiscono i criteri di richiesta, è possibile specificare singoli utenti o più gruppi di utenti in genere. Ad esempio, è possibile che l'organizzazione disponga già di un gruppo come **tutti i dipendenti**.  Se tale gruppo viene aggiunto nei criteri per gli utenti che possono richiedere l'accesso, qualsiasi membro del gruppo potrà richiedere l'accesso.

1. Negli **utenti che possono richiedere l'** elenco di accesso selezionare **per gli utenti nella directory**.

    Quando si seleziona questa opzione, vengono visualizzate le nuove opzioni per definire ulteriormente chi nella directory può richiedere questo pacchetto di accesso.

    ![Accedere ai pacchetti-richieste-per gli utenti nella directory](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selezionare una delle opzioni seguenti:

    |  |  |
    | --- | --- |
    | **Utenti e gruppi specifici** | Scegliere questa opzione se si desidera che solo gli utenti e i gruppi nella directory specificata siano in grado di richiedere questo pacchetto di accesso. |
    | **Tutti i membri (esclusi i guest)** | Scegliere questa opzione se si desidera che tutti gli utenti membro della directory siano in grado di richiedere questo pacchetto di accesso. Questa opzione non include gli utenti guest che potrebbero essere stati invitati nella directory. |
    | **Tutti gli utenti (inclusi i guest)** | Scegliere questa opzione se si desidera che tutti gli utenti membro e gli utenti guest nella directory siano in grado di richiedere questo pacchetto di accesso. |

    Gli utenti Guest fanno riferimento a utenti esterni che sono stati invitati alla directory con [Azure ad B2B](../articles/active-directory/b2b/what-is-b2b.md). Per informazioni sulle differenze tra utenti membro e utenti guest, vedere [quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Se sono stati selezionati **utenti e gruppi specifici**, fare clic su **Aggiungi utenti e gruppi**.

1. Nel riquadro Seleziona utenti e gruppi selezionare gli utenti e i gruppi che si desidera aggiungere.

    ![Accedere ai pacchetti-richieste-selezionare utenti e gruppi](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Fare clic su **Seleziona** per aggiungere gli utenti e i gruppi.

1. Passare alla sezione [approvazione](#approval) .

## <a name="for-users-not-in-your-directory"></a>Per gli utenti che non si trovino nella directory

Seguire questa procedura se si desidera consentire agli utenti non inclusi nella directory di richiedere questo pacchetto di accesso. Gli **utenti non inclusi nella directory** fanno riferimento agli utenti che si trovano in un altro Azure ad directory o dominio e potrebbero non essere ancora stati invitati nella propria directory. Le directory di Azure AD devono essere configurate in modo da consentire inviti in **restrizioni di collaborazione**. Per altre informazioni, vedere [abilitare la collaborazione esterna B2B e gestire gli utenti che possono invitare i guest](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Verrà creato un account utente Guest per un utente non ancora presente nella directory la cui richiesta è approvata o approvata automaticamente. Il Guest verrà invitato ma non riceverà un messaggio di posta elettronica di invito. Riceveranno invece un messaggio di posta elettronica quando viene recapitata l'assegnazione del pacchetto di accesso. Per impostazione predefinita, in un secondo momento, quando tale utente Guest non dispone più di assegnazioni di pacchetti di accesso, perché l'ultima assegnazione è scaduta o annullata, l'account utente guest verrà bloccato dall'accesso e successivamente eliminato. Se si desidera che gli utenti Guest rimangano per la directory a tempo indeterminato, anche se non dispongono di assegnazioni di pacchetti di accesso, è possibile modificare le impostazioni per la configurazione di gestione dei diritti. Per ulteriori informazioni sull'oggetto utente Guest, vedere [proprietà di un utente di collaborazione B2B di Azure Active Directory](../articles/active-directory/b2b/user-properties.md).

1. Negli **utenti che possono richiedere l'** elenco di accesso selezionare **per gli utenti non presenti nella directory**.

    Quando si seleziona questa opzione, vengono visualizzate le nuove opzioni.

    ![Accedere ai pacchetti-richieste: per gli utenti che non si trovino nella directory](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selezionare una delle opzioni seguenti:

    |  |  |
    | --- | --- |
    | **Organizzazioni connesse specifiche** | Scegliere questa opzione se si desidera effettuare una selezione da un elenco di organizzazioni che l'amministratore ha aggiunto in precedenza. Tutti gli utenti delle organizzazioni selezionate saranno in grado di richiedere questo pacchetto di accesso. |
    | **Tutte le organizzazioni connesse** | Scegliere questa opzione se si desidera che tutti gli utenti di tutte le organizzazioni connesse siano in grado di richiedere questo pacchetto di accesso. |

    Un'organizzazione connessa è una directory o un dominio di Azure AD esterno con cui si collabora di frequente.

1. Se sono state selezionate **organizzazioni connesse specifiche**, fare clic su **Aggiungi directory** per selezionare da un elenco di organizzazioni connesse che l'amministratore ha aggiunto in precedenza.

1. Immettere un nome di dominio per cercare un'organizzazione connessa con tale nome di dominio.

    ![Accedere ai pacchetti-richieste-selezionare le directory](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Se l'organizzazione con cui si vuole collaborare non è presente nell'elenco, è possibile richiedere all'amministratore di aggiungerla come organizzazione connessa. 

1. Dopo aver selezionato tutte le organizzazioni connesse, fare clic su **Seleziona**.

    > [!NOTE]
    > Tutti gli utenti delle organizzazioni connesse selezionate saranno in grado di richiedere questo pacchetto di accesso. Sono inclusi gli utenti di tutti i sottodomini associati alle organizzazioni connesse.

1. Passare alla sezione [approvazione](#approval) .

## <a name="none-administrator-direct-assignments-only"></a>Nessuno (solo assegnazioni dirette all'amministratore)

Seguire questa procedura se si vuole ignorare le richieste di accesso e consentire agli amministratori di assegnare direttamente utenti specifici a questo pacchetto di accesso. Gli utenti non dovranno richiedere il pacchetto di accesso. È comunque possibile impostare le impostazioni del ciclo di vita, ma non sono disponibili impostazioni della richiesta.

1. Negli **utenti che possono richiedere l'** elenco di accesso selezionare **nessuno (solo assegnazioni dirette all'amministratore**.

    ![Access Package-requests-None solo assegnazioni dirette all'amministratore](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Dopo aver creato il pacchetto di accesso, è possibile assegnare direttamente utenti interni ed esterni specifici al pacchetto di accesso. Se si specifica un utente esterno, nella directory verrà creato un account utente Guest. Per informazioni sull'assegnazione diretta di un utente, vedere [visualizzare e modificare le assegnazioni per un pacchetto di accesso](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Passare alla sezione [Enable requests](#enable-requests) .

## <a name="approval"></a>Approvazione

Nella sezione approvazione specificare se è necessaria un'approvazione quando gli utenti richiedono questo pacchetto di accesso. Le impostazioni di approvazione funzionano nel modo seguente:

- Solo uno dei responsabili approvazione o i responsabili approvazione del fallback selezionati deve approvare una richiesta. L'approvazione da tutti i responsabili approvazione non è obbligatoria.
- La decisione di approvazione si basa su qualsiasi revisore che esamina prima la richiesta.

1. Per richiedere l'approvazione per le richieste degli utenti selezionati, impostare l'interruttore **Richiedi approvazione** su **Sì**. Per approvare automaticamente le richieste, impostare l'interruttore su **No**.

    ![Access Package-requests-impostazioni di approvazione](./media/active-directory-entitlement-management-request-policy/approval.png)

1. Per richiedere agli utenti di fornire una giustificazione per richiedere il pacchetto di accesso, impostare l'opzione **Richiedi giustificazione** del richiedente su **Sì**.

1. Determinare se la richiesta richiede un'approvazione a una o più fasi. Impostare il **numero di fasi** su 1 per **una** sola fase.

1. Per responsabili approvazione, selezionare **responsabile come responsabile approvazione** o **scegliere responsabili approvazione specifici**.

    Il Manager è determinato dall'attributo **Manager** nel profilo dell'utente di Azure ad. Per altre informazioni, vedere [aggiungere o aggiornare le informazioni sul profilo di un utente usando Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

    ![Attributo Azure Active Directory User Profile-Manager](./media/active-directory-entitlement-management-request-policy/profile-manager.png)

1. Se è stato selezionato responsabile come responsabile approvazione, fare clic su **Aggiungi fallback** per selezionare uno o più utenti o gruppi nella directory come responsabile approvazione del fallback nel caso in cui la gestione dei diritti non sia in grado di trovare il responsabile.

1. Se è stata selezionata l'opzione Scegli responsabili approvazione specifici, fare clic su **Aggiungi responsabili approvazione** per selezionare uno o più utenti o gruppi nella directory da approvatori.

1. È **necessario decidere in quanti giorni?** specificare il numero di giorni per cui un responsabile approvazione deve esaminare una richiesta di questo pacchetto di accesso.

    Se una richiesta non viene approvata entro questo periodo di tempo, verrà negata automaticamente. L'utente dovrà inviare un'altra richiesta per il pacchetto di accesso.

1. Per richiedere agli utenti di fornire una giustificazione per richiedere il pacchetto di accesso, impostare **Richiedi giustificazione** su **Sì**.

    Una giustificazione è visibile ad altri responsabili approvazione e al richiedente.

## <a name="enable-requests"></a>Abilita richieste

1. Se si vuole che il pacchetto di accesso venga reso immediatamente disponibile per gli utenti nel criterio di richiesta da richiedere, fare clic su **Sì** per abilitare.

    È sempre possibile abilitarla in futuro dopo aver completato la creazione del pacchetto di accesso.

    Se è stata selezionata l'opzione **Nessuna (solo assegnazione diretta amministratore)** e si imposta Abilita su **No**, gli amministratori non saranno in grado di assegnare direttamente questo pacchetto di accesso.

    ![Pacchetto di accesso-criteri-Abilita impostazione dei criteri](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Fare clic su **Next** (Avanti).
