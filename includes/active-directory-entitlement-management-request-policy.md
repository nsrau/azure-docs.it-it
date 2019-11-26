---
title: File di inclusione
description: File di inclusione
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 11/11/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: fb72ed337219f58481c094d68342dbf6f26493c7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260766"
---
## <a name="for-users-in-your-directory"></a>Per gli utenti nella directory

Seguire questa procedura se si desidera consentire agli utenti della directory di richiedere questo pacchetto di accesso. Quando si definiscono i criteri di richiesta, è possibile specificare singoli utenti o più gruppi di utenti in genere. Ad esempio, è possibile che l'organizzazione disponga già di un gruppo come **tutti i dipendenti**.  Se tale gruppo viene aggiunto nei criteri per gli utenti che possono richiedere l'accesso, qualsiasi membro del gruppo potrà richiedere l'accesso.

1. Nella sezione **Utenti che possono richiedere l'accesso** fare clic su **Per gli utenti nella directory**.

    Quando si seleziona questa opzione, vengono visualizzate le nuove opzioni per definire ulteriormente chi nella directory può richiedere questo pacchetto di accesso.

    ![Accedere ai pacchetti-richieste-per gli utenti nella directory](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selezionare una delle opzioni seguenti:

    |  |  |
    | --- | --- |
    | **Utenti e gruppi specifici** | Scegliere questa opzione se si desidera che solo gli utenti e i gruppi nella directory specificata siano in grado di richiedere questo pacchetto di accesso. |
    | **Tutti i membri (esclusi i guest)** | Scegliere questa opzione se si desidera che tutti gli utenti membro della directory siano in grado di richiedere questo pacchetto di accesso. Questa opzione non include gli utenti guest che potrebbero essere stati invitati nella directory. |
    | **Tutti gli utenti (inclusi i guest)** | Scegliere questa opzione se si desidera che tutti gli utenti membro e gli utenti guest nella directory siano in grado di richiedere questo pacchetto di accesso. |

    Gli utenti Guest fanno riferimento a utenti esterni che sono stati invitati alla directory con [Azure ad B2B](../articles/active-directory/b2b/what-is-b2b.md). Per ulteriori informazioni sulle differenze tra utenti membro e utenti guest, vedere [quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Se sono stati selezionati **utenti e gruppi specifici**, fare clic su **Aggiungi utenti e gruppi**.

1. Nel riquadro Seleziona utenti e gruppi selezionare gli utenti e i gruppi che si desidera aggiungere.

    ![Accedere ai pacchetti-richieste-selezionare utenti e gruppi](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Fare clic su **Seleziona** per aggiungere gli utenti e i gruppi.

1. Passare alla sezione [approvazione](#approval) .

## <a name="for-users-not-in-your-directory"></a>Per gli utenti che non si trovino nella directory

 **Gli utenti non inclusi nella directory** fanno riferimento agli utenti che si trovano in un altro Azure ad directory o dominio. Questi utenti potrebbero non essere ancora stati invitati alla directory. Le directory di Azure AD devono essere configurate in modo da consentire inviti in **restrizioni di collaborazione**. Per altre informazioni, vedere [abilitare la collaborazione esterna B2B e gestire gli utenti che possono invitare i guest](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Verrà creato un account utente Guest per un utente non ancora presente nella directory la cui richiesta è approvata o approvata automaticamente. Il Guest verrà invitato ma non riceverà un messaggio di posta elettronica di invito. Riceveranno invece un messaggio di posta elettronica quando viene recapitata l'assegnazione del pacchetto di accesso. Per impostazione predefinita, in un secondo momento, quando tale utente Guest non dispone più di assegnazioni di pacchetti di accesso, perché l'ultima assegnazione è scaduta o annullata, l'account utente guest verrà bloccato dall'accesso e successivamente eliminato. Se si desidera che gli utenti Guest rimangano per la directory a tempo indeterminato, anche se non dispongono di assegnazioni di pacchetti di accesso, è possibile modificare le impostazioni per la configurazione di gestione dei diritti. Per ulteriori informazioni sull'oggetto utente Guest, vedere [proprietà di un utente di collaborazione B2B di Azure Active Directory](../articles/active-directory/b2b/user-properties.md).

Se si desidera consentire agli utenti non inclusi nella directory di richiedere il pacchetto di accesso, attenersi alla procedura seguente:

1. Nella sezione **utenti che possono richiedere l'accesso** fare clic su **per gli utenti non presenti nella directory**.

    Quando si seleziona questa opzione, vengono visualizzate le nuove opzioni.

    ![Accedere ai pacchetti-richieste: per gli utenti che non si trovino nella directory](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selezionare una delle opzioni seguenti:

    |  |  |
    | --- | --- |
    | **Organizzazioni connesse specifiche** | Scegliere questa opzione se si desidera effettuare una selezione da un elenco di organizzazioni che l'amministratore ha aggiunto in precedenza. Tutti gli utenti delle organizzazioni selezionate possono richiedere questo pacchetto di accesso. |
    | **Tutte le organizzazioni connesse** | Scegliere questa opzione se tutti gli utenti di tutte le organizzazioni connesse possono richiedere questo pacchetto di accesso. |

    Un'organizzazione connessa è una directory o un dominio di Azure AD esterno a cui è associata una relazione.

1. Se sono state selezionate **organizzazioni connesse specifiche**, fare clic su **Aggiungi directory** per selezionare da un elenco di organizzazioni connesse che l'amministratore ha aggiunto in precedenza.

1. Digitare il nome o il nome di dominio in cui eseguire la ricerca di un'organizzazione precedentemente connessa.

    ![Accedere ai pacchetti-richieste-selezionare le directory](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Se l'organizzazione con cui si vuole collaborare non è presente nell'elenco, è possibile richiedere all'amministratore di aggiungerla come organizzazione connessa. Per altre informazioni, vedere [aggiungere un'organizzazione connessa](../articles/active-directory/governance/entitlement-management-organization.md).

1. Dopo aver selezionato tutte le organizzazioni connesse, fare clic su **Seleziona**.

    > [!NOTE]
    > Tutti gli utenti delle organizzazioni connesse selezionate saranno in grado di richiedere questo pacchetto di accesso. Sono inclusi gli utenti in Azure AD da tutti i sottodomini associati all'organizzazione, a meno che tali domini non siano bloccati dall'elenco Consenti o nega di Azure B2B. Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](../articles/active-directory/b2b/allow-deny-list.md).

1. Passare alla sezione [approvazione](#approval) .

## <a name="none-administrator-direct-assignments-only"></a>Nessuno (solo assegnazioni dirette all'amministratore)

Seguire questa procedura se si vuole ignorare le richieste di accesso e consentire agli amministratori di assegnare direttamente utenti specifici a questo pacchetto di accesso. Gli utenti non dovranno richiedere il pacchetto di accesso. È comunque possibile impostare le impostazioni del ciclo di vita, ma non sono disponibili impostazioni della richiesta.

1. Nella sezione **utenti che possono richiedere l'accesso** fare clic su **None (solo assegnazioni dirette all'amministratore**).

    ![Access Package-requests-None solo assegnazioni dirette all'amministratore](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Dopo aver creato il pacchetto di accesso, è possibile assegnare direttamente utenti interni ed esterni specifici al pacchetto di accesso. Se si specifica un utente esterno, nella directory verrà creato un account utente Guest. Per informazioni sull'assegnazione diretta di un utente, vedere [visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Passare alla sezione [Enable requests](#enable-requests) .

## <a name="approval"></a>Approvazione

Nella sezione approvazione specificare se è necessaria un'approvazione quando gli utenti richiedono questo pacchetto di accesso. Le impostazioni di approvazione funzionano nel modo seguente:

- Solo uno dei responsabili approvazione o i responsabili approvazione del fallback selezionati deve approvare una richiesta per l'approvazione a fase singola. 
- Solo uno dei responsabili approvazione selezionati da ogni fase deve approvare una richiesta per l'approvazione a 2 fasi.
- Il responsabile approvazione può essere un responsabile, uno sponsor interno o un sponsor esterno, a seconda dell'utente che governa l'accesso.
- L'approvazione di ogni responsabile approvazione selezionato non è necessaria per l'approvazione a una o due fasi.
- La decisione di approvazione si basa su qualsiasi revisore che esamina prima la richiesta.

Per specificare le impostazioni di approvazione per le richieste per il pacchetto di accesso, attenersi alla procedura seguente:

1. Per richiedere l'approvazione per le richieste degli utenti selezionati, impostare l'interruttore **Richiedi approvazione** su **Sì**. In alternativa, per fare in modo che le richieste vengano approvate automaticamente, impostare l'interruttore su **No**.

1. Per richiedere agli utenti di fornire una giustificazione per richiedere il pacchetto di accesso, impostare l'opzione **Richiedi giustificazione** del richiedente su **Sì**.
    
1. Determinare ora se le richieste richiedono l'approvazione a una o due fasi. Impostare il **numero di fasi** su **1** per l'approvazione della fase singola o impostare l'interruttore su **2** per l'approvazione in due fasi.

    ![Access Package-requests-impostazioni di approvazione](./media/active-directory-entitlement-management-request-policy/approval.png)


Usare la procedura seguente per aggiungere responsabili approvazione dopo aver selezionato il numero di fasi necessarie: 

### <a name="single-stage-approval"></a>Approvazione a fase singola

1. Aggiungere il **primo responsabile approvazione**:
    
    Se il criterio è impostato per gestire l'accesso per gli utenti nella directory, è possibile selezionare **Manager come responsabile approvazione**. In alternativa, aggiungere un utente specifico facendo clic su **Aggiungi responsabili approvazione** dopo aver selezionato Scegli responsabili approvazione specifici dal menu a discesa.
    
    ![Accedere ai pacchetti-richieste-per gli utenti nel responsabile dell'approvazione della directory](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Se questo criterio è impostato per gestire l'accesso per gli utenti che non si trova nella directory, è possibile selezionare **sponsor esterno** o **sponsor interno**. In alternativa, aggiungere un utente specifico facendo clic su **Aggiungi responsabili approvazione** o gruppi in Scegli responsabili approvazione specifici.
    
    ![Accedere al pacchetto-richieste-per gli utenti fuori dalla directory-primo responsabile approvazione](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Se è stata selezionata l'opzione **Manager** come primo responsabile approvazione, fare clic su **Aggiungi fallback** per selezionare uno o più utenti o gruppi nella directory come responsabile approvazione del fallback. I responsabili approvazione fallback ricevono la richiesta se la gestione dei diritti non riesce a trovare il responsabile per l'utente che richiede l'accesso.

    Il Manager è stato rilevato dalla gestione dei diritti utilizzando l'attributo **Manager** . L'attributo si trova nel profilo dell'utente in Azure AD. Per altre informazioni, vedere [aggiungere o aggiornare le informazioni sul profilo di un utente usando Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Se è stata selezionata l'opzione **Scegli responsabili approvazione specifici**, fare clic su **Aggiungi responsabili approvazione** per selezionare uno o più utenti o gruppi nella directory da approvatori.

1. Nella casella in base alle **decisioni è necessario**specificare il numero di giorni per cui un responsabile approvazione deve esaminare una richiesta di questo pacchetto di accesso.

    Se una richiesta non viene approvata entro questo periodo di tempo, verrà negata automaticamente. L'utente dovrà inviare un'altra richiesta per il pacchetto di accesso.

1. Per richiedere agli approvatori di fornire una giustificazione per la loro decisione, impostare Richiedi giustificazione del responsabile approvazione su **Sì**.

    La giustificazione è visibile ad altri responsabili approvazione e al richiedente.

### <a name="2-stage-approval-preview"></a>approvazione a 2 fasi (anteprima)

Se è stata selezionata un'approvazione a 2 fasi, sarà necessario aggiungere un secondo responsabile approvazione.

1. Aggiungere il **secondo responsabile approvazione**: 
    
    Se gli utenti si trovano nella directory, aggiungere un utente specifico come secondo responsabile approvazione facendo clic su **Aggiungi responsabili approvazione** in Scegli responsabili approvazione specifici.

    ![Accedere ai pacchetti-richieste-per gli utenti nel responsabile approvazione directory-secondo](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Se gli utenti non sono presenti nella directory, selezionare **sponsor interno** o **sponsor esterno** come secondo responsabile approvazione. Dopo aver selezionato il responsabile approvazione, aggiungere i responsabili approvazione fallback.

    ![Accedere al pacchetto-richieste-per gli utenti fuori dalla directory-secondo responsabile approvazione](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Specificare il numero di giorni per cui il secondo responsabile approvazione deve approvare la richiesta nella casella in base alla **decisione deve essere effettuata**per il numero di giorni. 

1. Impostare l'opzione Richiedi giustificazione del responsabile approvazione su **Sì** o **No**.

### <a name="alternate-approvers"></a>Responsabili approvazione alternativi

È possibile specificare responsabili approvazione alternativi, in modo analogo alla specifica del primo e del secondo approvatori che possono approvare le richieste. Con responsabili approvazione alternativi, è possibile garantire che le richieste vengano approvate o negate prima della scadenza (timeout). È possibile elencare gli approvatori alternativi il primo responsabile approvazione e il secondo responsabile dell'approvazione per 2 fasi. 

Specificando responsabili approvazione alternativi, nel caso in cui il primo o il secondo approvatori non fosse in grado di approvare o rifiutare la richiesta, la richiesta in sospeso viene trasmessa ai responsabili approvazione alternativi, in base alla pianificazione di invio specificata durante l'installazione dei criteri. Ricevono un messaggio di posta elettronica per approvare o negare la richiesta in sospeso.

Dopo l'invio della richiesta ai responsabili approvazione alternativi, il primo o il secondo approvatori può comunque approvare o rifiutare la richiesta. Gli approvatori alternativi utilizzano lo stesso sito di accesso personale per approvare o negare la richiesta in sospeso.

È possibile elencare persone o gruppi di persone come responsabili approvazione e responsabili approvazione alternativi. Assicurarsi di elencare set diversi di persone come primo, secondo e responsabili approvazione alternativi.
Se ad esempio sono stati elencati Alice e Bob come primo responsabile approvazione, elencare Carol e Dave come approvatori alternativi. Usare la procedura seguente per aggiungere responsabili approvazione alternativi a un pacchetto di accesso:

1. Sotto il primo responsabile approvazione, secondo responsabile approvazione o entrambi, fare clic su **Mostra impostazioni richieste avanzate**.

    ![Accedi ai criteri di pacchetto-Mostra impostazioni avanzate delle richieste](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Impostare **se non viene eseguita alcuna azione, procedere con l'invio a responsabili approvazione alternativi?** impostare su **Sì**.

1. Fare clic su **Aggiungi responsabili approvazione alternativi** e selezionare gli approvatori alternativi dall'elenco.

    ![Accedere ai criteri dei pacchetti-aggiungere responsabili approvazione alternativi](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. In **futuro per i responsabili approvazione alternativi dopo** il numero di giorni, inserire il numero di giorni per cui i responsabili approvazione devono approvare o rifiutare una richiesta. Se nessun responsabile approvazione ha approvato o negato la richiesta prima della durata della richiesta, la richiesta scade (timeout) e l'utente dovrà inviare un'altra richiesta per il pacchetto di accesso. 

    Le richieste possono essere inviate solo a responsabili approvazione alternativi al giorno dopo la durata della richiesta. In questo esempio, la durata della richiesta è di 14 giorni. Quindi, la durata della richiesta raggiunge la metà della vita del giorno 7. Quindi, la richiesta non può essere trasmessa prima del giorno 8. Non è inoltre possibile inviare le richieste l'ultimo giorno della durata della richiesta. Nell'esempio, l'ultima richiesta che è possibile trasmettere è il giorno 13.

## <a name="enable-requests"></a>Abilita richieste

1. Se si vuole che il pacchetto di accesso venga reso immediatamente disponibile per gli utenti nel criterio di richiesta da richiedere, fare clic su **Sì** per abilitare.

    È sempre possibile abilitarla in futuro dopo aver completato la creazione del pacchetto di accesso.

    Se è stata selezionata l'opzione **Nessuna (solo assegnazione diretta amministratore)** e si imposta Abilita su **No**, gli amministratori non potranno assegnare direttamente questo pacchetto di accesso.

    ![Pacchetto di accesso-criteri-Abilita impostazione dei criteri](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Fare clic su **Next** (Avanti).
