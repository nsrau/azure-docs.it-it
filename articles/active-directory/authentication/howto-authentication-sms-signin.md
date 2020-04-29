---
title: Accesso utente basato su SMS per Azure Active Directory
description: Informazioni su come configurare e consentire agli utenti di accedere a Azure Active Directory usando SMS (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770559"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Configurare e abilitare gli utenti per l'autenticazione basata su SMS usando Azure Active Directory (anteprima)

Per ridurre i rischi di complessità e sicurezza per gli utenti per l'accesso ad applicazioni e servizi, Azure Active Directory (Azure AD) offre più opzioni di autenticazione. L'autenticazione basata su SMS, attualmente in fase di anteprima, consente agli utenti di eseguire l'accesso senza dover fornire o addirittura conoscerne il nome utente e la password. Dopo che l'account è stato creato da un amministratore di identità, può immettere il numero di telefono al prompt di accesso e fornire un codice di autenticazione che viene inviato tramite SMS. Questo metodo di autenticazione semplifica l'accesso ad applicazioni e servizi, in particolare per i ruoli di lavoro in linea.

Questo articolo illustra come abilitare l'autenticazione basata su SMS per utenti o gruppi selezionati in Azure AD.

|     |
| --- |
| L'autenticazione basata su SMS per gli utenti è una funzionalità di anteprima pubblica di Azure Active Directory. Per ulteriori informazioni sulle anteprime, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant Azure Active Directory associato alla sottoscrizione.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Per abilitare l'autenticazione basata su SMS, è necessario disporre dei privilegi di *amministratore globale* nel tenant di Azure ad.
* Ogni utente abilitato nei criteri del metodo di autenticazione tramite SMS deve essere concesso in licenza, anche se non lo usa. Ogni utente abilitato deve avere una delle seguenti Azure AD o licenze Microsoft 365:
    * [Azure AD Premium P1 o P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 o F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 o E5][ems-licensing] o [Microsoft 365 (M365) E3 o E5][m365-licensing]

## <a name="limitations"></a>Limitazioni

Durante l'anteprima pubblica dell'autenticazione basata su SMS, si applicano le limitazioni seguenti:

* L'autenticazione basata su SMS non è attualmente compatibile con Multi-Factor Authentication di Azure.
* Fatta eccezione per i team, l'autenticazione basata su SMS non è attualmente compatibile con le applicazioni di Office native.
* L'autenticazione basata su SMS non è consigliata per gli account B2B.
* Gli utenti federati non eseguono l'autenticazione nel tenant Home. Eseguono solo l'autenticazione nel cloud.

## <a name="enable-the-sms-based-authentication-method"></a>Abilitare il metodo di autenticazione basato su SMS

Per abilitare e utilizzare l'autenticazione basata su SMS nell'organizzazione, è necessario eseguire tre passaggi principali:

* Abilitare i criteri del metodo di autenticazione.
* Selezionare gli utenti o i gruppi che possono usare il metodo di autenticazione basato su SMS.
* Assegnare un numero di telefono per ogni account utente.
    * Questo numero di telefono può essere assegnato nella portale di Azure (illustrata in questo articolo) e nel personale o nel *profilo* *personale* .

Per prima cosa, è necessario abilitare l'autenticazione basata su SMS per il tenant di Azure AD.

1. Accedere al [portale di Azure][azure-portal] come *amministratore globale*.
1. Cercare e selezionare **Azure Active Directory**.
1. Dal menu di spostamento sul lato sinistro della finestra di Azure Active Directory selezionare **sicurezza > metodi di autenticazione > criteri metodo di autenticazione (anteprima)**.

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Dall'elenco dei metodi di autenticazione **disponibili selezionare SMS**.
1. Impostare **Abilita** su *Sì*.

    ![Abilitare l'autenticazione del testo nella finestra criteri metodo di autenticazione](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    È possibile scegliere di abilitare l'autenticazione basata su SMS per *tutti gli utenti* o *selezionare utenti* e gruppi. Nella sezione successiva viene abilitata l'autenticazione basata su SMS per un utente test.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Assegnare il metodo di autenticazione a utenti e gruppi

Con l'autenticazione basata su SMS abilitata nel tenant di Azure AD, ora selezionare alcuni utenti o gruppi a cui è consentito l'uso di questo metodo di autenticazione.

1. Nella finestra dei criteri di autenticazione degli SMS impostare **destinazione** su *Seleziona utenti*.
1. Scegliere di **aggiungere utenti o gruppi**, quindi selezionare un utente o un gruppo di test, ad esempio *Contoso User* o *Contoso SMS*Users.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Dopo aver selezionato gli utenti o i gruppi, scegliere **Seleziona**, quindi **salvare** i criteri del metodo di autenticazione aggiornati.

Ogni utente abilitato nei criteri del metodo di autenticazione tramite SMS deve essere concesso in licenza, anche se non lo usa. Assicurarsi di disporre delle licenze appropriate per gli utenti abilitati nei criteri del metodo di autenticazione, soprattutto quando si Abilita la funzionalità per gruppi di utenti di grandi dimensioni.

## <a name="set-a-phone-number-for-user-accounts"></a>Impostare un numero di telefono per gli account utente

Gli utenti sono ora abilitati per l'autenticazione basata su SMS, ma il numero di telefono deve essere associato al profilo utente in Azure AD prima di poter eseguire l'accesso. L'utente può [impostare questo numero di telefono](../user-help/sms-sign-in-explainer.md) nel *profilo personale*oppure è possibile assegnare il numero di telefono usando il portale di Azure. I numeri di telefono possono essere impostati da *amministratori globali*, *amministratori dell'autenticazione*o *amministratori di autenticazione con privilegi*.

Quando un numero di telefono è impostato per SMS-Sign, è disponibile anche per l'uso con [Azure multi-factor authentication][tutorial-azure-mfa] e la [reimpostazione della password self-service][tutorial-sspr].

1. Cercare e selezionare **Azure Active Directory**.
1. Dal menu di spostamento sul lato sinistro della finestra di Azure Active Directory selezionare **utenti**.
1. Selezionare l'utente abilitato per l'autenticazione basata su SMS nella sezione precedente, ad esempio *utente contoso*, quindi selezionare **metodi di autenticazione**.
1. Immettere il numero di telefono dell'utente, incluso il codice paese, ad esempio *+ 1 xxxxxxxxx*. Il portale di Azure convalida che il numero di telefono è nel formato corretto.

    ![Impostare un numero di telefono per un utente nel portale di Azure da usare con l'autenticazione basata su SMS](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    Il numero di telefono deve essere univoco nel tenant. Se si tenta di utilizzare lo stesso numero di telefono per più utenti, viene visualizzato un messaggio di errore.

1. Per applicare il numero di telefono a un account utente, selezionare **Salva**.

Quando è stato eseguito il provisioning, viene visualizzato un segno di spunta per l' *accesso SMS abilitato*.

## <a name="test-sms-based-sign-in"></a>Testare l'accesso basato su SMS

Per testare l'account utente che ora è abilitato per l'accesso basato su SMS, attenersi alla procedura seguente:

1. Aprire una nuova finestra del Web browser InPrivate o in incognito per[https://www.office.com][office]
1. Nell'angolo superiore destro selezionare **Sign in (accedi**).
1. Al prompt di accesso immettere il numero di telefono associato all'utente nella sezione precedente, quindi fare clic su **Avanti**.

    ![Immettere un numero di telefono alla richiesta di accesso per l'utente test](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Viene inviato un SMS al numero di telefono fornito. Per completare il processo di accesso, immettere il codice a 6 cifre fornito nell'SMS al prompt di accesso.

    ![Immettere il codice di conferma inviato tramite SMS al numero di telefono dell'utente](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. L'utente è ora connesso senza la necessità di specificare un nome utente o una password.

## <a name="troubleshoot-sms-based-sign-in"></a>Risolvere i problemi di accesso basato su SMS

Gli scenari e le procedure di risoluzione dei problemi seguenti possono essere usati in caso di problemi con l'abilitazione e l'uso dell'accesso basato su SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Il numero di telefono è già impostato per un account utente

Se un utente ha già effettuato la registrazione per Azure Multi-Factor Authentication e/o la reimpostazione della password self-service (SSPR), dispone già di un numero di telefono associato al proprio account. Questo numero di telefono non è automaticamente disponibile per l'uso con l'accesso basato su SMS.

Un utente che dispone di un numero di telefono già impostato per il proprio account viene visualizzato con un pulsante per *abilitare l'accesso a SMS* nella pagina **profilo personale** . Selezionare questo pulsante e l'account è abilitato per l'uso con l'accesso basato su SMS e la registrazione Multi-Factor Authentication o SSPR di Azure precedente.

Per altre informazioni sull'esperienza dell'utente finale, vedere [esperienza utente di accesso SMS per numero di telefono (anteprima)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Errore durante il tentativo di impostare un numero di telefono per l'account di un utente

Se viene visualizzato un errore quando si tenta di impostare un numero di telefono per un account utente nella portale di Azure, rivedere i passaggi di risoluzione dei problemi seguenti:

1. Assicurarsi di essere abilitati per l'anteprima di accesso basato su SMS.
1. Verificare che l'account utente sia abilitato nei criteri del metodo *di autenticazione tramite SMS.*
1. Assicurarsi di impostare il numero di telefono con la formattazione corretta, come convalidato nell'portale di Azure (ad esempio *+ 1 4251234567*).
1. Verificare che il numero di telefono non venga usato altrove nel tenant.
1. Verificare che non vi sia un numero di voce impostato per l'account. Se viene impostato un numero di voce, eliminare e provare nuovamente a usare il numero di telefono.

## <a name="next-steps"></a>Passaggi successivi

Per altri modi per accedere a Azure AD senza una password, ad esempio l'app Microsoft Authenticator o le chiavi di sicurezza FIDO2, vedere [Opzioni di autenticazione senza password per Azure ad][concepts-passwordless].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
