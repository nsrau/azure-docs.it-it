---
title: Accesso utente basato su SMS per Azure Active Directory
description: Informazioni su come configurare e abilitare gli utenti ad accedere ad Azure Active Directory tramite SMS (anteprima)Learn how to configure and enable users to sign in to Azure Active Directory using SMS (preview)
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
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770559"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Configurare e abilitare gli utenti per l'autenticazione basata su SMS usando Azure Active Directory (anteprima)Configure and enable users for SMS-based authentication using Azure Active Directory (preview)

Per ridurre la complessità e i rischi di sicurezza per gli utenti di accedere ad applicazioni e servizi, Azure Active Directory (Azure AD) offre più opzioni di autenticazione. L'autenticazione basata su SMS, attualmente in anteprima, consente agli utenti di accedere senza dover fornire, o addirittura conoscere, il proprio nome utente e password. Dopo che il loro account è stato creato da un amministratore di identità, possono immettere il proprio numero di telefono al prompt di accesso e fornire un codice di autenticazione che viene inviato tramite SMS. Questo metodo di autenticazione semplifica l'accesso ad applicazioni e servizi, in particolare per i lavoratori in prima linea.

Questo articolo illustra come abilitare l'autenticazione basata su SMS per utenti o gruppi selezionati in Azure AD.

|     |
| --- |
| L'autenticazione basata su SMS per gli utenti è una funzionalità di anteprima pubblica di Azure Active Directory.SMS-based authentication for users is a public preview feature of Azure Active Directory. Per altre informazioni sulle anteprime, vedere Condizioni per [l'utilizzo supplementari per le anteprime di Microsoft AzureFor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) more information about previews, see Supplemental Terms of Use for Microsoft Azure Previews|
|     |

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Per abilitare l'autenticazione basata su SMS sono necessari privilegi di *amministratore globale* nel tenant di Azure AD.
* Ogni utente abilitato nei criteri del metodo di autenticazione tramite SMS deve essere concesso in licenza, anche se non lo usa. Ogni utente abilitato deve disporre di una delle seguenti licenze di Azure AD o Microsoft 365:
    * [Azure AD Premium P1 o P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 o F3][m365-firstline-workers-licensing]
    * [Mobilità aziendale - Sicurezza (EMS) E3 o E5][ems-licensing] o [Microsoft 365 (M365) E3 o E5][m365-licensing]

## <a name="limitations"></a>Limitazioni

Durante l'anteprima pubblica dell'autenticazione basata su SMS, si applicano le seguenti limitazioni:

* L'autenticazione basata su SMS non è attualmente compatibile con Azure Multi-Factor Authentication.
* Ad eccezione di Teams, l'autenticazione basata su SMS non è attualmente compatibile con le applicazioni native di Office.
* L'autenticazione basata su SMS non è consigliata per gli account B2B.
* Gli utenti federati non verranno autenticati nel tenant principale. Si autenticano solo nel cloud.

## <a name="enable-the-sms-based-authentication-method"></a>Abilitare il metodo di autenticazione basato su SMS

Esistono tre passaggi principali per abilitare e utilizzare l'autenticazione basata su SMS nell'organizzazione:

* Abilitare i criteri del metodo di autenticazione.
* Selezionare gli utenti o i gruppi che possono utilizzare il metodo di autenticazione basato su SMS.
* Assegnare un numero di telefono per ogni account utente.
    * Questo numero di telefono può essere assegnato nel portale di Azure (illustrato in questo articolo) e in *Personale* personale o *Profilo personale*.

In primo luogo, è possibile abilitare l'autenticazione basata su SMS per il tenant di Azure AD.

1. Accedere al [portale][azure-portal] di Azure come *amministratore globale.*
1. Cercare e selezionare **Azure Active Directory**.
1. Dal menu di navigazione sul lato sinistro della finestra di Azure Active Directory selezionare Metodi di protezione > autenticazione > criterio del metodo di autenticazione **(anteprima)**.

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Dall'elenco dei metodi di autenticazione disponibili, selezionare **Messaggio di testo**.
1. Impostare **Abilita su** *Sì*.

    ![Abilitare l'autenticazione testo nella finestra dei criteri del metodo di autenticazioneEnable text authentication in the authentication method policy window](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    È possibile scegliere di abilitare l'autenticazione basata su SMS per *Tutti gli utenti* o Seleziona *utenti* e gruppi. Nella sezione successiva si abilita l'autenticazione basata su SMS per un utente di test.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Assegnare il metodo di autenticazione a utenti e gruppi

Con l'autenticazione basata su SMS abilitata nel tenant di Azure AD, selezionare ora alcuni utenti o gruppi a cui consentire l'utilizzo di questo metodo di autenticazione.

1. Nella finestra dei criteri di autenticazione tramite SMS impostare **Target** su *Seleziona utenti*.
1. Scegliere **Aggiungi utenti o gruppi**, quindi selezionare un utente o un gruppo di prova, ad esempio *Utente Contoso* o Utenti *SMS Contoso*.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Dopo aver selezionato gli utenti o i gruppi, scegliere **Seleziona**, quindi **Salva** il criterio del metodo di autenticazione aggiornato.

Ogni utente abilitato nei criteri del metodo di autenticazione tramite SMS deve essere concesso in licenza, anche se non lo usa. Assicurarsi di disporre delle licenze appropriate per gli utenti abilitati nei criteri del metodo di autenticazione, in particolare quando si abilita la funzionalità per grandi gruppi di utenti.

## <a name="set-a-phone-number-for-user-accounts"></a>Impostare un numero di telefono per gli account utente

Gli utenti sono ora abilitati per l'autenticazione basata su SMS, ma il loro numero di telefono deve essere associato al profilo utente in Azure AD prima di poter accedere. L'utente può impostare questo numero di [telefono autonomamente](../user-help/sms-sign-in-explainer.md) in Profilo personale oppure è possibile assegnare il numero di telefono tramite il portale di Azure.The user can set this phone number themselves in *My Profile,* or you can assign the phone number using the Azure portal. I numeri di telefono possono essere impostati dagli *amministratori globali,* dagli amministratori di *autenticazione*o dagli *amministratori dell'autenticazione con privilegi.*

Quando un numero di telefono è impostato per la firma SMS, è anche quindi disponibile per l'uso con [Azure Multi-Factor Authentication][tutorial-azure-mfa] e [la reimpostazione della password self-service][tutorial-sspr].

1. Cercare e selezionare **Azure Active Directory**.
1. Nel menu di navigazione sul lato sinistro della finestra di Azure Active Directory selezionare **Utenti**.
1. Selezionare l'utente abilitato per l'autenticazione basata su SMS nella sezione precedente, ad esempio *Utente Contoso*, quindi selezionare Metodi di **autenticazione**.
1. Immettere il numero di telefono dell'utente, incluso il codice del paese, ad esempio *1 xxxxxxxxx*. Il portale di Azure convalida che il numero di telefono sia nel formato corretto.

    ![Impostare un numero di telefono per un utente nel portale di Azure da usare con l'autenticazione basata su SMSSet a phone number for a user in the Azure portal to use with SMS-based authentication](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    Il numero di telefono deve essere univoco nel tenant. Se si tenta di utilizzare lo stesso numero di telefono per più utenti, viene visualizzato un messaggio di errore.

1. Per applicare il numero di telefono all'account di un utente, selezionare **Salva**.

Quando il provisioning è stato eseguito correttamente, viene visualizzato un segno di spunta per *l'accesso a SMS abilitato*.

## <a name="test-sms-based-sign-in"></a>Testare l'accesso basato su SMS

La procedura seguente illustra come testare l'account utente abilitato per l'accesso basato su SMS:

1. Aprire una nuova finestra del browser Web InPrivate o Incognito per[https://www.office.com][office]
1. Nell'angolo in alto a destra, seleziona **Accedi.**
1. Al prompt di accesso, immettere il numero di telefono associato all'utente nella sezione precedente, quindi selezionare **Avanti**.

    ![Immettere un numero di telefono al prompt di accesso per l'utente di prova](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Un messaggio di testo viene inviato al numero di telefono fornito. Per completare il processo di accesso, immettere il codice a 6 cifre fornito nel messaggio di testo al prompt di accesso.

    ![Inserisci il codice di conferma inviato tramite SMS al numero di telefono dell'utente](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. L'utente ha eseguito l'accesso senza la necessità di fornire un nome utente o una password.

## <a name="troubleshoot-sms-based-sign-in"></a>Risolvere i problemi relativi all'accesso basato su SMSTroubleshoot SMS-based sign-in

I seguenti scenari e procedure per la risoluzione dei problemi possono essere utilizzati in caso di problemi con l'abilitazione e l'utilizzo dell'accesso basato su SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Numero di telefono già impostato per un account utente

Se un utente è già registrato per Azure Multi-Factor Authentication e/o la reimpostazione della password self-service (SSPR), dispone già di un numero di telefono associato al proprio account. Questo numero di telefono non è automaticamente disponibile per l'utilizzo con l'accesso basato su SMS.

Un utente che ha già impostato un numero di telefono per il proprio account viene visualizzato un pulsante *di abilitalazione per l'accesso SMS* nella pagina **Profilo personale.** Selezionare questo pulsante e l'account è abilitato per l'uso con l'accesso basato su SMS e la precedente registrazione di Azure Multi-Factor Authentication o SSPR.

Per ulteriori informazioni sull'esperienza dell'utente finale, vedere Esperienza utente di accesso SMS per il numero di [telefono (anteprima)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Errore durante il tentativo di impostare un numero di telefono nell'account di un utente

Se viene visualizzato un errore quando si tenta di impostare un numero di telefono per un account utente nel portale di Azure, esaminare la procedura di risoluzione dei problemi seguente:If you receive an error when you try to set a phone number for a user account in the Azure portal, review the following troubleshooting steps:

1. Assicurati di essere abilitato per l'anteprima di accesso basata su SMS.
1. Verificare che l'account utente sia abilitato nel criterio Del metodo di autenticazione *SMS.*
1. Assicurarsi di impostare il numero di telefono con la formattazione corretta, come convalidato nel portale di Azure (ad esempio *1 4251234567*).
1. Assicurarsi che il numero di telefono non venga usato in un altro punto del tenant.
1. Controlla che non sia impostato alcun numero vocale sull'account. Se è impostato un numero di voce, elimina e riprova a utilizzare il numero di telefono.

## <a name="next-steps"></a>Passaggi successivi

Per altri modi per accedere ad Azure AD senza una password, ad esempio l'app Microsoft Authenticator o le chiavi di sicurezza FIDO2, vedere Opzioni di [autenticazione senza password per Azure AD.][concepts-passwordless]

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
