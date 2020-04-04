---
title: File di inclusione
description: File di inclusione
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 7fd716be397d9ef6b9d6132cd4470f653f3cea0f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655960"
---
## <a name="for-users-in-your-directory"></a>Per gli utenti nella directory

Seguire questi passaggi se si desidera consentire agli utenti nella directory di poter richiedere questo pacchetto di accesso. Quando si definiscono i criteri di richiesta, è possibile specificare singoli utenti o più comunemente gruppi di utenti. Ad esempio, l'organizzazione potrebbe già disporre di un gruppo, ad esempio **Tutti i dipendenti**.  Se tale gruppo viene aggiunto nei criteri per gli utenti che possono richiedere l'accesso, qualsiasi membro di tale gruppo può richiedere l'accesso.

1. Nella sezione **Utenti che possono richiedere l'accesso** fare clic su **Per gli utenti nella directory**.

    Quando si seleziona questa opzione, vengono visualizzate nuove opzioni per perfezionare ulteriormente chi nella directory può richiedere questo pacchetto di accesso.

    ![Pacchetto di accesso - Richieste - Per gli utenti nella directoryAccess package - Requests - For users in your directory](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selezionare una delle opzioni seguenti:

    |  |  |
    | --- | --- |
    | **Utenti e gruppi specifici** | Scegliere questa opzione se si desidera che solo gli utenti e i gruppi della directory specificata siano in grado di richiedere questo pacchetto di accesso. |
    | **Tutti i membri (esclusi gli ospiti)** | Scegliere questa opzione se si desidera che tutti gli utenti membri nella directory siano in grado di richiedere questo pacchetto di accesso. Questa opzione non include gli utenti guest che potresti aver invitato nella tua directory. |
    | **Tutti gli utenti (inclusi gli ospiti)** | Scegliere questa opzione se si desidera che tutti gli utenti membri e gli utenti guest nella directory siano in grado di richiedere questo pacchetto di accesso. |

    Gli utenti guest fanno riferimento a utenti esterni che sono stati invitati nella directory con [Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Per altre informazioni sulle differenze tra gli utenti membri e gli utenti guest, vedere [Quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Se è stato selezionato **Utenti e gruppi specifici**, fare clic su Aggiungi utenti e **gruppi**.

1. Nel riquadro Seleziona utenti e gruppi selezionare gli utenti e i gruppi che si desidera aggiungere.

    ![Pacchetto di accesso - Richieste - Selezionare utenti e gruppi](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Fare clic su **Seleziona** per aggiungere utenti e gruppi.

1. Passare alla sezione [Approvazione.](#approval)

## <a name="for-users-not-in-your-directory"></a>Per gli utenti non presente nella directory

 **Gli utenti non presenti nella directory** si riferiscono agli utenti che si trovano in un'altra directory o dominio di Azure AD. Questi utenti potrebbero non essere ancora stati invitati nella directory. Le directory di Azure AD devono essere configurate per consentire gli inviti nelle restrizioni di **collaborazione.** Per ulteriori informazioni, consultate [Abilitare la collaborazione esterna B2B e gestire chi può invitare ospiti.](../articles/active-directory/b2b/delegate-invitations.md)

> [!NOTE]
> Verrà creato un account utente guest per un utente non ancora presente nella directory la cui richiesta è stata approvata o approvata automaticamente. L'ospite verrà invitato, ma non riceverà un'e-mail di invito. Al contrario, riceveranno un messaggio di posta elettronica quando viene recapitata l'assegnazione del pacchetto di accesso. Per impostazione predefinita, in un secondo momento, quando l'utente guest non ha più alcuna assegnazione del pacchetto di accesso, poiché l'ultima assegnazione è scaduta o è stata annullata, tale account utente guest verrà bloccato dall'accesso e successivamente eliminato. Se si desidera che gli utenti guest rimangano nella directory a tempo indeterminato, anche se non dispongono di assegnazioni di pacchetti di accesso, è possibile modificare le impostazioni per la configurazione di gestione dei diritti. Per altre informazioni sull'oggetto utente guest, vedere Proprietà di un utente di collaborazione B2B di Azure Active Directory.For more information about the guest user object, see [Properties of an Azure Active Directory B2B collaboration user](../articles/active-directory/b2b/user-properties.md).

Se si desidera consentire agli utenti non presenti nella directory di richiedere questo pacchetto di accesso, attenersi alla seguente procedura:

1. Nella sezione **Utenti che possono richiedere l'accesso** fare clic su Per utenti non nella **directory**.

    Quando si seleziona questa opzione, vengono visualizzate nuove opzioni.

    ![Pacchetto di accesso - Richieste - Per gli utenti non inclusi nella directoryAccess package - Requests - For users not in your directory](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selezionare una delle opzioni seguenti:

    |  |  |
    | --- | --- |
    | **Organizzazioni connesse specifiche** | Scegliere questa opzione se si desidera selezionare da un elenco di organizzazioni aggiunte in precedenza dall'amministratore. Tutti gli utenti delle organizzazioni selezionate possono richiedere questo pacchetto di accesso. |
    | **Tutte le organizzazioni connesse** | Scegliere questa opzione se tutti gli utenti di tutte le organizzazioni connesse possono richiedere questo pacchetto di accesso. |
    | **Tutti gli utenti (Tutte le organizzazioni connesse - tutti i nuovi utenti esterni)** | Scegliere questa opzione se tutti gli utenti di tutte le organizzazioni connesse possono richiedere questo pacchetto di accesso e che le impostazioni dell'elenco B2B devono avere la precedenza per qualsiasi nuovo utente esterno. |

    Un'organizzazione connessa è una directory o un dominio di Azure AD esterno con cui si ha una relazione.

1. Se è stata selezionata l'opzione **Organizzazioni connesse specifiche**, fare clic su **Aggiungi directory** per selezionare da un elenco di organizzazioni connesse aggiunte in precedenza dall'amministratore.

1. Digitare il nome o il nome di dominio per cercare un'organizzazione connessa in precedenza.

    ![Pacchetto di accesso - Richieste - Seleziona directory](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Se l'organizzazione con cui si desidera collaborare non è presente nell'elenco, è possibile chiedere all'amministratore di aggiungerla come organizzazione connessa. Per ulteriori informazioni, vedere [Aggiungere un'organizzazione connessa](../articles/active-directory/governance/entitlement-management-organization.md).

1. Dopo aver selezionato tutte le organizzazioni connesse, fare clic su **Seleziona**.

    > [!NOTE]
    > Tutti gli utenti delle organizzazioni connesse selezionate saranno in grado di richiedere questo pacchetto di accesso. Sono inclusi gli utenti in Azure AD di tutti i sottodomini associati all'organizzazione, a meno che tali domini non siano bloccati dall'elenco Consenti o Nega di Azure B2B.This includes users in Azure AD from all subdomains associated with the organization, unless those domains are blocked by the Azure B2B allow or deny list. Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](../articles/active-directory/b2b/allow-deny-list.md).

1. Passare alla sezione [Approvazione.](#approval)

## <a name="none-administrator-direct-assignments-only"></a>Nessuno (solo assegnazioni dirette dall'amministratore)

Seguire questi passaggi se si desidera ignorare le richieste di accesso e consentire agli amministratori di assegnare direttamente utenti specifici a questo pacchetto di accesso. Gli utenti non dovranno richiedere il pacchetto di accesso. È comunque possibile impostare le impostazioni del ciclo di vita, ma non sono presenti impostazioni di richiesta.

1. Nella sezione **Utenti che possono richiedere l'accesso** fare clic su Nessuno **(solo assegnazioni dirette dall'amministratore.**

    ![Pacchetto di accesso - Richieste - Solo assegnazioni dirette dell'amministratore](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Dopo aver creato il pacchetto di accesso, è possibile assegnare direttamente utenti interni ed esterni specifici al pacchetto di accesso. Se si specifica un utente esterno, verrà creato un account utente guest nella directory. Per informazioni sull'assegnazione diretta di un utente, vedere [Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)di accesso.

1. Passare alla sezione [Abilita richieste.](#enable-requests)

## <a name="approval"></a>Approvazione

Nella sezione Approvazione è possibile specificare se è necessaria un'approvazione quando gli utenti richiedono questo pacchetto di accesso. Le impostazioni di approvazione funzionano nel modo seguente:

- Solo uno degli approvatori o degli approvatori di fallback selezionati deve approvare una richiesta di approvazione a fase singola. 
- Solo uno degli approvatori selezionati da ogni fase deve approvare una richiesta di approvazione in due fasi.
- L'approvatore può essere un Manager, uno sponsor interno o uno sponsor esterno a seconda di chi il criterio disciplina l'accesso.
- L'approvazione da parte di ogni approvatore selezionato non è necessaria per l'approvazione singola o a due fasi.
- La decisione di approvazione si basa sul responsabile approvazione che esamina prima la richiesta.

Per una dimostrazione di come aggiungere approvatori a un criterio di richiesta, guardare il video seguente:For a demonstration of how to add approvers to a request policy, watch the following video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Per una dimostrazione di come aggiungere un'approvazione in più fasi a un criterio di richiesta, guardare il video seguente:For a demonstration of how to add a multi-stage approval to a request policy, watch the following video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Seguire questi passaggi per specificare le impostazioni di approvazione per le richieste per il pacchetto di accesso:Follow these steps to specify the approval settings for requests for the access package:

1. Per richiedere l'approvazione per le richieste degli utenti selezionati, impostare l'interruttore **Richiedi approvazione** su **Sì**. In alternativa, per approvare automaticamente le richieste, impostare l'interruttore su **No**.

1. Per richiedere agli utenti di fornire una giustificazione per richiedere il pacchetto di accesso, impostare l'opzione **Richiedi giustificazione richiedente** su **Sì**.
    
1. A questo punto determinare se le richieste richiedono l'approvazione a una o a due fasi. Impostare l'interruttore **Numero di fasi** su **1** per l'approvazione a fase singola o impostare l'interruttore su **2** per l'approvazione a 2 fasi.

    ![Pacchetto di accesso - Richieste - Impostazioni di approvazione](./media/active-directory-entitlement-management-request-policy/approval.png)

Attenersi alla seguente procedura per aggiungere approvatori dopo aver selezionato il numero di fasi necessarie: 

### <a name="single-stage-approval"></a>Approvazione monofase

1. Aggiungere il **primo responsabile approvazione**:
    
    Se il criterio è impostato per gestire l'accesso per gli utenti nella directory, è possibile selezionare **Manager come approvatore**. In alternativa, aggiungere un utente specifico facendo clic su **Aggiungi responsabili approvazione** dopo aver selezionato Scegli responsabili approvazione specifici dal menu a discesa.
    
    ![Pacchetto di accesso - Richieste - Per gli utenti nella directory - Primo responsabile approvazioneAccess package - Requests - For users in directory - First Approver](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Se questo criterio è impostato per gestire l'accesso per gli utenti non nella directory, è possibile selezionare **Sponsor esterno** o **Sponsor interno**. In alternativa, aggiungere un utente specifico facendo clic su **Aggiungi responsabili di approvazione** o gruppi in Scegliere responsabili approvazione specifici.
    
    ![Pacchetto di accesso - Richieste - Per gli utenti fuori dalla directory - Primo responsabile approvazione](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Se hai selezionato **Manager** come primo approvatore, fai clic su **Aggiungi fallback** per selezionare uno o più utenti o gruppi nella directory come approvatore di fallback. Gli approvatori di fallback ricevono la richiesta se la gestione dei diritti non riesce a trovare il responsabile per l'utente che richiede l'accesso.

    Il manager viene trovato dalla gestione dei diritti utilizzando l'attributo **Manager.** L'attributo è nel profilo dell'utente in Azure AD. Per altre informazioni, vedere Aggiungere o aggiornare le informazioni sul profilo di un utente usando Azure Active Directory.For more information, see Add or [update a user's profile information using Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Se è stata selezionata l'opzione **Scegli approvatori specifici**, fare clic su **Aggiungi responsabili approvazione** per selezionare uno o più utenti o gruppi nella directory da approvare.

1. Nella casella **In Decisione deve essere presa tra quanti giorni?**, specificare il numero di giorni in cui un approvatore deve esaminare una richiesta per questo pacchetto di accesso.

    Se una richiesta non viene approvata entro questo periodo di tempo, verrà automaticamente negata. L'utente dovrà inviare un'altra richiesta per il pacchetto di accesso.

1. Per richiedere agli approvatori di fornire una giustificazione per la decisione, impostare Richiedi giustificazione approvatore su **Sì**.

    La giustificazione è visibile agli altri approvatori e al richiedente.

### <a name="2-stage-approval"></a>Approvazione in 2 fasi

Se è stata selezionata un'approvazione in due fasi, sarà necessario aggiungere un secondo responsabile approvazione.

1. Aggiungere il **secondo responsabile approvazione**: 
    
    Se gli utenti si trovano nella directory, aggiungere un utente specifico come secondo approvatore facendo clic su **Aggiungi responsabili approvazione** in Scegli responsabili approvazione specifici.

    ![Pacchetto di accesso - Richieste - Per gli utenti nella directory - Secondo responsabile approvazioneAccess package - Requests - For users in directory - Second Approver](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Se gli utenti non sono nella directory, selezionare **Sponsor interno** o **Sponsor esterno** come secondo approvatore. Dopo aver selezionato l'approvatore, aggiungere gli approvatori di fallback.

    ![Pacchetto di accesso - Richieste - Per gli utenti dalla directory - Secondo responsabile approvazione](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Specificare il numero di giorni in cui il secondo approvatore deve approvare la richiesta nella casella **Decisione in quanti giorni?**. 

1. Impostare l'interruttore Richiedi giustificazione approvatore su **Sì** o **No**.

### <a name="alternate-approvers"></a>Approvatori alternativi

È possibile specificare responsabili approvazione alternativi, in modo analogo a specificare il primo e il secondo approvatore che possono approvare le richieste. La presenza di responsabili approvazione alternativi contribuirà a garantire che le richieste vengano approvate o rifiutate prima della scadenza (timeout). È possibile elencare gli approvatori alternativi il primo approvatore e il secondo approvatore per l'approvazione in due fasi. 

Specificando responsabili approvazione alternativi, nel caso in cui il primo o il secondo approvatore non sia stato in grado di approvare o rifiutare la richiesta, la richiesta in sospeso viene inoltrata ai responsabili approvazione alternativi, in base alla pianificazione di inoltro specificata durante l'impostazione dei criteri. Ricevono un'e-mail per approvare o rifiutare la richiesta in sospeso.

Dopo che la richiesta viene inoltrata agli approvatori alternativi, il primo o il secondo approvatore può comunque approvare o rifiutare la richiesta. Gli approvatori alternativi utilizzano lo stesso sito Accesso personale per approvare o rifiutare la richiesta in sospeso.

Possiamo elencare persone o gruppi di persone come approvatori e approvatori alternativi. Assicurati di elencare diversi set di persone come responsabili approvazione primo, secondo e alternativo.
Ad esempio, se Alice e Bob sono stati elencati come Primo approvatore, si elencacaro e Dave come approvatori alternativi. Utilizzare la procedura seguente per aggiungere responsabili approvazione alternativi a un pacchetto di accesso:Use the following steps to add alternate approvers to an access package:

1. In Primo responsabile approvazione, Secondo responsabile approvazione o in entrambi fare clic su **Mostra impostazioni richiesta avanzate**.

    ![Pacchetto di accesso - Criterio - Mostra impostazioni avanzate di richiesta](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Impostare **Se non viene eseguita alcuna azione, inoltrare a alternare gli approvatori?** passare a **Sì**.

1. Fare clic su **Aggiungi approvatori alternativi** e selezionare gli approvatori alternativi dall'elenco.

    ![Pacchetto di accesso - Criterio - Aggiunta di responsabili approvazione alternativiAccess package - Policy - Add Alternate Approvers](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. Nella casella **Inoltra a approvatori alternativi dopo** il numero di giorni, inserirlo nel numero di giorni in cui gli approvatori devono approvare o rifiutare una richiesta. Se nessun approvatore ha approvato o negato la richiesta prima della durata della richiesta, la richiesta scade (timeout) e l'utente dovrà inviare un'altra richiesta per il pacchetto di accesso. 

    Le richieste possono essere inoltrate solo agli approvatori alternativi un giorno dopo che la durata della richiesta raggiunge l'emivita. In questo esempio, la durata della richiesta è 14 giorni. Quindi, la durata della richiesta raggiunge l'emivita al giorno 7. Quindi la richiesta non può essere inoltrata prima del giorno 8. Inoltre, le richieste non possono essere inoltrate l'ultimo giorno della durata della richiesta. Quindi, nell'esempio, l'ultima richiesta può essere inoltrata è il giorno 13.

## <a name="enable-requests"></a>Abilitare le richieste

1. Se si desidera che il pacchetto di accesso sia immediatamente disponibile per gli utenti nel criterio di richiesta da richiedere, spostare l'interruttore Abilita su **Sì**.

    È sempre possibile abilitarlo in futuro dopo aver completato la creazione del pacchetto di accesso.

    Se è stata selezionata l'opzione **Nessuno (solo assegnazioni dirette dall'amministratore)** e si imposta enable su **No**, gli amministratori non possono assegnare direttamente questo pacchetto di accesso.

    ![Pacchetto di accesso - Criterio- Abilita impostazione dei criteri](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Fare clic su **Avanti**.
