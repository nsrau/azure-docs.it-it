---
title: Modificare le impostazioni della richiesta per un pacchetto di accesso in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come modificare le impostazioni della richiesta per un pacchetto di accesso in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7b70e8a408c22be0331bfd0dcbe01830b072ab8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449982"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Modificare le impostazioni della richiesta per un pacchetto di accesso in Azure AD gestione dei diritti

Per accedere a gestione pacchetti, è possibile modificare gli utenti che possono richiedere un pacchetto di accesso in qualsiasi momento modificando il criterio o aggiungendo un nuovo criterio. Questo articolo descrive come modificare le impostazioni della richiesta per un pacchetto di accesso esistente.

## <a name="choose-between-one-or-multiple-policies"></a>Scegliere uno o più criteri

Il modo in cui si specificano gli utenti che possono richiedere un pacchetto di accesso è con un criterio. Prima di creare un nuovo criterio o modificare un criterio esistente in un pacchetto di accesso, è necessario determinare il numero di criteri necessari per il pacchetto di accesso. 

Quando si crea un pacchetto di accesso, si specifica l'impostazione della richiesta che crea un criterio. La maggior parte dei pacchetti di accesso avrà un solo criterio, ma un singolo pacchetto di accesso può avere più criteri. È possibile creare più criteri per un pacchetto di accesso se si desidera consentire a diversi set di utenti di concedere le assegnazioni con diverse impostazioni di richiesta e approvazione. 

Ad esempio, non è possibile usare un singolo criterio per assegnare utenti interni ed esterni allo stesso pacchetto di accesso. Tuttavia, è possibile creare due criteri nello stesso pacchetto di accesso, uno per gli utenti interni e uno per gli utenti esterni. Se sono presenti più criteri che si applicano a un utente, verrà richiesto al momento della richiesta di selezionare i criteri a cui si desidera assegnare. Il diagramma seguente illustra un pacchetto di accesso con due criteri.

![Più criteri in un pacchetto di accesso](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>Quanti criteri sono necessari?

| Scenario | Numero di criteri |
| --- | --- |
| Si desidera che tutti gli utenti della directory dispongano delle stesse impostazioni di richiesta e approvazione per un pacchetto di accesso | Uno |
| Desidero che tutti gli utenti di determinate organizzazioni connesse siano in grado di richiedere un pacchetto di accesso | Uno |
| Desidero consentire agli utenti della mia directory e anche a utenti esterni alla directory di richiedere un pacchetto di accesso | Più elementi |
| Si desidera specificare impostazioni di approvazione diverse per alcuni utenti | Più elementi |
| Si desidera che alcuni utenti accedano alle assegnazioni dei pacchetti per scadere mentre altri utenti possono estenderne l'accesso | Più elementi |

Per informazioni sulla logica di priorità usata quando si applicano più criteri, vedere [criteri multipli](entitlement-management-troubleshoot.md#multiple-policies
).

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Aprire un pacchetto di accesso esistente e aggiungere un nuovo criterio per le impostazioni della richiesta

Se si dispone di un set di utenti che devono disporre di impostazioni di richiesta e approvazione diverse, è probabile che sia necessario creare un nuovo criterio. Per iniziare ad aggiungere un nuovo criterio a un pacchetto di accesso esistente, attenersi alla procedura seguente:

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **criteri** , quindi su **Aggiungi criterio**.

1. Si inizierà dalla scheda **nozioni di base** . Digitare un nome e una descrizione per il criterio.

    ![Crea criterio con nome e descrizione](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Fare clic su **Avanti** per aprire la scheda **Richieste**.

1. Modificare gli **utenti che possono richiedere** l'impostazione di accesso. Usare la procedura descritta nelle sezioni seguenti per modificare l'impostazione in una delle opzioni seguenti: 
    - [Per gli utenti nella directory](#for-users-in-your-directory) 
    - [Per gli utenti che non si trovino nella directory](#for-users-not-in-your-directory)
    - [Nessuno (solo assegnazioni dirette all'amministratore)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>Per gli utenti nella directory

Seguire questa procedura se si desidera consentire agli utenti della directory di richiedere questo pacchetto di accesso. Quando si definiscono i criteri di richiesta, è possibile specificare singoli utenti o più gruppi di utenti in genere. Ad esempio, è possibile che l'organizzazione disponga già di un gruppo come **tutti i dipendenti**.  Se tale gruppo viene aggiunto nei criteri per gli utenti che possono richiedere l'accesso, qualsiasi membro del gruppo potrà richiedere l'accesso.

1. Nella sezione **Utenti che possono richiedere l'accesso** fare clic su **Per gli utenti nella directory**.

    Quando si seleziona questa opzione, vengono visualizzate le nuove opzioni per definire ulteriormente chi nella directory può richiedere questo pacchetto di accesso.

    ![Accedere ai pacchetti-richieste-per gli utenti nella directory](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Selezionare una delle opzioni seguenti:

    |  |  |
    | --- | --- |
    | **Utenti e gruppi specifici** | Scegliere questa opzione se si desidera che solo gli utenti e i gruppi nella directory specificata siano in grado di richiedere questo pacchetto di accesso. |
    | **Tutti i membri (esclusi i guest)** | Scegliere questa opzione se si desidera che tutti gli utenti membro della directory siano in grado di richiedere questo pacchetto di accesso. Questa opzione non include gli utenti guest che potrebbero essere stati invitati nella directory. |
    | **Tutti gli utenti (inclusi i guest)** | Scegliere questa opzione se si desidera che tutti gli utenti membro e gli utenti guest nella directory siano in grado di richiedere questo pacchetto di accesso. |

    Gli utenti Guest fanno riferimento a utenti esterni che sono stati invitati alla directory con [Azure ad B2B](../external-identities/what-is-b2b.md). Per ulteriori informazioni sulle differenze tra utenti membro e utenti guest, vedere [quali sono le autorizzazioni utente predefinite in Azure Active Directory?](../fundamentals/users-default-permissions.md).

1. Se sono stati selezionati **utenti e gruppi specifici**, fare clic su **Aggiungi utenti e gruppi**.

1. Nel riquadro Seleziona utenti e gruppi selezionare gli utenti e i gruppi che si desidera aggiungere.

    ![Accedere ai pacchetti-richieste-selezionare utenti e gruppi](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. Fare clic su **Seleziona** per aggiungere gli utenti e i gruppi.

1. Se si desidera richiedere l'approvazione, attenersi alla procedura descritta in [modificare le impostazioni di approvazione per un pacchetto di accesso in Azure ad gestione dei diritti](entitlement-management-access-package-approval-policy.md) per configurare le impostazioni di approvazione.

1. Passare alla sezione [Enable requests](#enable-requests) .
 
## <a name="for-users-not-in-your-directory"></a>Per gli utenti che non si trovino nella directory

 **Gli utenti non inclusi nella directory** fanno riferimento agli utenti che si trovano in un altro Azure ad directory o dominio. Questi utenti potrebbero non essere ancora stati invitati alla directory. Le directory di Azure AD devono essere configurate in modo da consentire inviti in **restrizioni di collaborazione**. Per altre informazioni, vedere [abilitare la collaborazione esterna B2B e gestire gli utenti che possono invitare i guest](../external-identities/delegate-invitations.md).

> [!NOTE]
> Verrà creato un account utente Guest per un utente non ancora presente nella directory la cui richiesta è approvata o approvata automaticamente. Il Guest verrà invitato ma non riceverà un messaggio di posta elettronica di invito. Riceveranno invece un messaggio di posta elettronica quando viene recapitata l'assegnazione del pacchetto di accesso. Per impostazione predefinita, in un secondo momento, quando tale utente Guest non dispone più di assegnazioni di pacchetti di accesso, perché l'ultima assegnazione è scaduta o annullata, l'account utente guest verrà bloccato dall'accesso e successivamente eliminato. Se si desidera che gli utenti Guest rimangano per la directory a tempo indeterminato, anche se non dispongono di assegnazioni di pacchetti di accesso, è possibile modificare le impostazioni per la configurazione di gestione dei diritti. Per ulteriori informazioni sull'oggetto utente Guest, vedere [proprietà di un utente di collaborazione B2B di Azure Active Directory](../external-identities/user-properties.md).

Se si desidera consentire agli utenti non inclusi nella directory di richiedere il pacchetto di accesso, attenersi alla procedura seguente:

1. Nella sezione **utenti che possono richiedere l'accesso** fare clic su **per gli utenti non presenti nella directory**.

    Quando si seleziona questa opzione, vengono visualizzate le nuove opzioni.

    ![Accedere ai pacchetti-richieste: per gli utenti che non si trovino nella directory](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Selezionare una delle opzioni seguenti:

    |  |  |
    | --- | --- |
    | **Organizzazioni connesse specifiche** | Scegliere questa opzione se si desidera effettuare una selezione da un elenco di organizzazioni che l'amministratore ha aggiunto in precedenza. Tutti gli utenti delle organizzazioni selezionate possono richiedere questo pacchetto di accesso. |
    | **Tutte le organizzazioni connesse configurate** | Scegliere questa opzione se tutti gli utenti di tutte le organizzazioni connesse configurate possono richiedere questo pacchetto di accesso. Solo gli utenti di organizzazioni connesse configurate possono richiedere pacchetti di accesso visualizzati agli utenti di tutte le organizzazioni configurate. |
    | **Tutti gli utenti (tutte le organizzazioni connesse e tutti i nuovi utenti esterni)** | Scegliere questa opzione se un utente su Internet deve essere in grado di richiedere questo pacchetto di accesso.  Se non appartengono a un'organizzazione connessa nella directory, un'organizzazione connessa verrà creata automaticamente quando richiede il pacchetto. Lo stato dell'organizzazione connessa creata automaticamente sarà **proposto** . Per ulteriori informazioni sullo stato proposto, vedere [proprietà di stato delle organizzazioni connesse](entitlement-management-organization.md#state-properties-of-connected-organizations). |

    Un'organizzazione connessa è una directory o un dominio di Azure AD esterno a cui è associata una relazione.

1. Se sono state selezionate **organizzazioni connesse specifiche**, fare clic su **Aggiungi directory** per selezionare da un elenco di organizzazioni connesse che l'amministratore ha aggiunto in precedenza.

1. Digitare il nome o il nome di dominio in cui eseguire la ricerca di un'organizzazione precedentemente connessa.

    ![Accedere ai pacchetti-richieste-selezionare le directory](./media/entitlement-management-access-package-request-policy/select-directories.png)

    Se l'organizzazione con cui si vuole collaborare non è presente nell'elenco, è possibile richiedere all'amministratore di aggiungerla come organizzazione connessa. Per altre informazioni, vedere [aggiungere un'organizzazione connessa](entitlement-management-organization.md).

1. Dopo aver selezionato tutte le organizzazioni connesse, fare clic su **Seleziona**.

    > [!NOTE]
    > Tutti gli utenti delle organizzazioni connesse selezionate saranno in grado di richiedere questo pacchetto di accesso. Sono inclusi gli utenti in Azure AD da tutti i sottodomini associati all'organizzazione, a meno che tali domini non siano bloccati dall'elenco Consenti o nega di Azure B2B. Per altre informazioni, consultare [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](../external-identities/allow-deny-list.md).

1. Se si desidera richiedere l'approvazione, attenersi alla procedura descritta in [modificare le impostazioni di approvazione per un pacchetto di accesso in Azure ad gestione dei diritti](entitlement-management-access-package-approval-policy.md) per configurare le impostazioni di approvazione.
 
1. Passare alla sezione [Enable requests](#enable-requests) .

## <a name="none-administrator-direct-assignments-only"></a>Nessuno (solo assegnazioni dirette all'amministratore)

Seguire questa procedura se si vuole ignorare le richieste di accesso e consentire agli amministratori di assegnare direttamente utenti specifici a questo pacchetto di accesso. Gli utenti non dovranno richiedere il pacchetto di accesso. È comunque possibile impostare le impostazioni del ciclo di vita, ma non sono disponibili impostazioni della richiesta.

1. Nella sezione **utenti che possono richiedere l'accesso** fare clic su **None (solo assegnazioni dirette all'amministratore**).

    ![Access Package-requests-None solo assegnazioni dirette all'amministratore](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    Dopo aver creato il pacchetto di accesso, è possibile assegnare direttamente utenti interni ed esterni specifici al pacchetto di accesso. Se si specifica un utente esterno, nella directory verrà creato un account utente Guest. Per informazioni sull'assegnazione diretta di un utente, vedere [visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso](entitlement-management-access-package-assignments.md).

1. Passare alla sezione [Enable requests](#enable-requests) .


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>Aprire e modificare un criterio esistente per le impostazioni della richiesta

Per modificare le impostazioni di richiesta e approvazione per un pacchetto di accesso, è necessario aprire il criterio corrispondente. Per aprire e modificare le impostazioni della richiesta per un pacchetto di Access, attenersi alla procedura seguente:

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Fare clic su **criteri** , quindi selezionare il criterio che si desidera modificare.

    Verrà visualizzato il riquadro dettagli criteri nella parte inferiore della pagina.

    ![Pacchetto di accesso-riquadro dei dettagli dei criteri](./media/entitlement-management-shared/policy-details.png)

1. Fare clic su **modifica** per modificare il criterio.

    ![Accedi ai criteri di modifica del pacchetto](./media/entitlement-management-shared/policy-edit.png)

1. Fare clic sulla scheda **richieste** per aprire le impostazioni della richiesta.

1. Usare la procedura descritta nelle sezioni precedenti per modificare le impostazioni della richiesta in base alle esigenze.

1. Passare alla sezione [Enable requests](#enable-requests) .

## <a name="enable-requests"></a>Abilita richieste

1. Se si vuole che il pacchetto di accesso venga reso immediatamente disponibile per gli utenti del criterio di richiesta da richiedere, spostare l'interruttore Abilita su **Sì**.

    È sempre possibile abilitarla in futuro dopo aver completato la creazione del pacchetto di accesso.

    Se è stata selezionata l'opzione **Nessuna (solo assegnazione diretta amministratore)** e si imposta Abilita su **No**, gli amministratori non potranno assegnare direttamente questo pacchetto di accesso.

    ![Pacchetto di accesso-criteri-Abilita impostazione dei criteri](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Fare clic su **Avanti**.

1. Se si desidera che i richiedenti forniscano informazioni aggiuntive durante la richiesta di accesso a un pacchetto di accesso, attenersi alla procedura descritta in []() per configurare le informazioni sul richiedente (anteprima).

1. Configurare le impostazioni del ciclo di vita.

1. Se si sta modificando un criterio, fare clic su **Aggiorna**. Se si aggiunge un nuovo criterio, fare clic su **Crea**.

## <a name="next-steps"></a>Passaggi successivi

- [Modificare le impostazioni di approvazione per un pacchetto di accesso](entitlement-management-access-package-approval-policy.md)
- [Modificare le impostazioni del ciclo di vita per un pacchetto di accesso](entitlement-management-access-package-lifecycle-policy.md)
- [Visualizzare le richieste per un pacchetto di accesso](entitlement-management-access-package-requests.md)
