---
title: Aggiungere o modificare i ruoli di amministratore della sottoscrizione di Azure | Documentazione Microsoft
description: Descrive come aggiungere o modificare il co-amministratore di Azure, l’amministratore del servizio e l’amministratore dell’account
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: cwatson
ms.openlocfilehash: 94d574d16b1b9951ab91a09023f9193723f850a7
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583365"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure

Per gestire l'accesso alle risorse di Azure, è necessario il ruolo di amministratore appropriato. Questo articolo descrive come aggiungere o modificare il ruolo di amministratore per un utente a livello di sottoscrizione.

## <a name="what-administrator-role-do-i-use"></a>Quale ruolo di amministratore devo usare?

Azure offre più ruoli diversi. Per gestire l'accesso alle risorse, è possibile usare i ruoli di amministrazione della sottoscrizione classica, ad esempio Amministratore dei servizi e Coamministratore, oppure un sistema di autorizzazioni più recente denominato controllo degli accessi in base al ruolo. Per garantire un migliore controllo e per semplificare la gestione dell'accesso, è consigliabile usare Controllo degli accessi in base al ruolo per tutte le esigenze di gestione dell'accesso. Se possibile, è consigliabile riconfigurare i criteri di accesso esistenti con Controllo degli accessi in base al ruolo. Per altre informazioni, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../role-based-access-control/overview.md) e [Informazioni sui diversi ruoli](../role-based-access-control/rbac-and-directory-admin-roles.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-for-a-subscription-in-azure-portal"></a>Aggiungere un proprietario di Controllo degli accessi in base al ruolo per una sottoscrizione nel portale di Azure 

Per aggiungere un utente come amministratore per una sottoscrizione di Azure, assegnargli il ruolo [Proprietario](../role-based-access-control/built-in-roles.md#owner) (ruolo Controllo degli accessi in base al ruolo) nell'ambito della sottoscrizione. Il ruolo di proprietario può gestire le risorse nella sottoscrizione assegnata e non ha privilegi di accesso per altre sottoscrizioni.

1. Aprire [**Sottoscrizioni** nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selezionare la sottoscrizione a cui concedere l'accesso.
3. Selezionare **Controllo di accesso (IAM)** nell'elenco.
4. Selezionare **Aggiungi assegnazione di ruolo**.
   (Se manca il pulsante Aggiungi assegnazione di ruolo, non si è autorizzati ad aggiungere autorizzazioni.)
5. Nella casella **Ruolo** selezionare **Proprietario**. 
6. Nella casella **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**. 
7. Nella casella **Seleziona** digitare l'indirizzo di posta elettronica dell'utente che si vuole aggiungere come proprietario. Selezionare l'utente e quindi selezionare **Salva**.

    ![Schermata che mostra il pulsante Proprietario selezionato](./media/billing-add-change-azure-subscription-administrator/add-role.png)

Questo ruolo garantisce all'utente l'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Per concedere l'accesso per un ambito diverso, ad esempio un gruppo di risorse, vedere il pannello **Controllo di accesso (IAM)** per tale ambito.

## <a name="add-or-change-co-administrator"></a>Aggiungere o modificare un coamministratore

Solo un [Proprietario](../role-based-access-control/built-in-roles.md#owner) può essere aggiunto come coamministratore. Gli altri utenti con ruoli come [Collaboratore](../role-based-access-control/built-in-roles.md#contributor) e [Lettore](../role-based-access-control/built-in-roles.md#reader) non possono essere aggiunti come coamministratori.

> [!TIP]
> È solo necessario aggiungere il proprietario come coamministratore se l'utente deve gestire distribuzioni di Azure classiche. È consigliabile usare Controllo degli accessi in base al ruolo per tutti gli altri scopi.

1. Se non è già stato fatto, aggiungere un utente come proprietario seguendo le istruzioni illustrate in precedenza.
2. **Fare clic con il pulsante destro del mouse** sull'utente proprietario appena aggiunto e quindi scegliere **Aggiungi come coamministratore**. Se l'opzione **Aggiungi come coamministratore** non è visibile, aggiornare la pagina o provare a usare un altro browser Internet. 

    ![Schermata per l'aggiunta di un coamministratore](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Per rimuovere l'autorizzazione di coamministratore, **fare clic con il pulsante destro del mouse** sull'utente "Coamministratore" e quindi scegliere **Rimuovi coamministratore**.

    ![Schermata per la rimozione di un coamministratore](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Aggiunta di un utente guest come coamministratore

Gli utenti guest a cui è stato assegnato il ruolo di coamministratore potrebbero notare alcune differenze rispetto agli utenti membri con ruolo di coamministratore. Si consideri lo scenario seguente:

- L'utente A con un account aziendale o dell'istituto di istruzione di Azure AD è un amministratore del servizio per una sottoscrizione di Azure.
- L'utente B ha un account Microsoft.
- L'utente A assegna il ruolo di coamministratore all'utente B.
- L'utente B può eseguire quasi tutte le operazioni, ma non può registrare le applicazioni o cercare gli utenti nella directory di Azure AD.

Ci si aspetta che l'utente B possa gestire tutto. Il motivo di questa differenza è che l'account Microsoft viene aggiunto alla sottoscrizione come utente guest invece di utente membro. Gli utenti guest hanno diverse autorizzazioni predefinite in Azure AD rispetto agli utenti membri. Ad esempio, gli utenti membri possono leggere gli altri utenti di Azure AD mentre gli utenti guest non possono farlo. Gli utenti membri possono registrare nuove entità servizio in Azure AD mentre gli utenti guest non possono farlo. Se un utente guest deve essere in grado di eseguire queste attività, una possibile soluzione consiste nell'assegnare i ruoli di amministratore di Azure AD specifici necessari all'utente guest. Ad esempio, nello scenario precedente è possibile assegnare i [ruoli con autorizzazioni di lettura nella directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) per leggere altri utenti e assegnare il ruolo [sviluppatore di applicazioni](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) per poter creare le entità servizio. Per altre informazioni sugli utenti membri e guest e le relative autorizzazioni, vedere [Autorizzazioni utente predefinite in Azure Active Directory](../active-directory/fundamentals/users-default-permissions.md).

Si noti che i [ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md) sono diversi dai [ruoli di amministratore di Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). I ruoli predefiniti non concedono l'accesso ad Azure AD. Per altre informazioni, vedere [Informazioni sui diversi ruoli](../role-based-access-control/rbac-and-directory-admin-roles.md).

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Modificare l'amministratore del servizio per una sottoscrizione di Azure

Solo l'amministratore account può modificare l'amministratore del servizio per una sottoscrizione. Per impostazione predefinita, al momento dell'iscrizione, l'amministratore del servizio corrisponde all'amministratore account. Se un altro utente viene impostato come amministratore del servizio, l'amministratore account perde l'accesso al portale di Azure. L'amministratore account può tuttavia usare il Centro account per reimpostare se stesso come amministratore del servizio.

1. Verificare che lo scenario sia supportato esaminando le [limitazioni per la modifica degli amministratori del servizio](#limits).
1. Accedere al [Centro account](https://account.windowsazure.com/subscriptions) come amministratore account.
1. Selezionare una sottoscrizione.
1. Sul lato destro selezionare **Modifica i dettagli della sottoscrizione**.

    ![Screenshot che mostra il pulsante di modifica della sottoscrizione nel Centro account](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Nella casella **AMMINISTRATORE DEL SERVIZIO** immettere l'indirizzo di posta elettronica del nuovo amministratore del servizio.

    ![Screenshot che mostra la casella per modificare l'indirizzo e-mail dell'amministratore del servizio](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limitazioni per la modifica degli amministratori del servizio

* Ogni sottoscrizione è associata a una directory di Azure AD. Per trovare la directory a cui è associata la sottoscrizione, passare a [**Sottoscrizioni**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), quindi selezionare una sottoscrizione per visualizzare la directory.
* Se l'accesso è stato eseguito con un account aziendale o dell'istituto di istruzione, è possibile aggiungere altri account dell'organizzazione come amministratori del servizio. Ad esempio, abby@contoso.com può aggiungere bob@contoso.com come amministratore del servizio, ma non può aggiungere john@notcontoso.com a meno che john@notcontoso.com non si trovi nella directory contoso.com. Gli utenti che hanno eseguito l'accesso con un account aziendale o dell'istituto di istruzione possono continuare ad aggiungere utenti con account Microsoft come amministratori del servizio.

  | Metodo di accesso | Aggiungere l'utente con account Microsoft come amministratore del servizio? | Aggiungere un account aziendale o dell'istituto di istruzione nella stessa organizzazione come amministratore del servizio? | Aggiungere un account aziendale o dell'istituto di istruzione in un'organizzazione diversa come amministratore del servizio? |
  | --- | --- | --- | --- |
  |  Account Microsoft |Yes |No  |No  |
  |  Account aziendale o dell'istituto di istruzione |Yes |Sì |No  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Modificare l'amministratore account per una sottoscrizione di Azure

L'amministratore account è l'utente che inizialmente ha effettuato la sottoscrizione di Azure ed è il responsabile della fatturazione della sottoscrizione. Per modificare l'amministratore account di una sottoscrizione, vedere [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Non si è sicuri di chi sia l'amministratore account?** A tale scopo, seguire questa procedura:

1. Aprire [**Sottoscrizioni** nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.
1. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore dell'account**.  

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Altre informazioni sul controllo dell'accesso alle risorse e Active Directory

* Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../role-based-access-control/overview.md)
* Per altre informazioni su tutti i ruoli in Azure, vedere [Informazioni sui diversi ruoli](../role-based-access-control/rbac-and-directory-admin-roles.md).
* Per altre informazioni sul modo in cui Azure Active Directory, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) e [Assegnazione dei ruoli di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).