---
title: Richieste di Multi-Factor Authentication di Azure e durata della sessione
description: Informazioni sulla configurazione consigliata per i prompt di riautenticazione con Multi-Factor Authentication di Azure e su come viene applicata la durata della sessione.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d5ffaf996f51348334f4adeeae150db9eb0defa
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052665"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-multi-factor-authentication"></a>Ottimizzare le richieste di riautenticazione e comprendere la durata della sessione per Azure Multi-Factor Authentication

Azure Active Directory (Azure AD) dispone di più impostazioni che determinano la frequenza con cui gli utenti devono eseguire nuovamente l'autenticazione. Questa riautenticazione può avere un primo fattore, ad esempio password, FIDO o senza Microsoft Authenticator password, oppure per eseguire multi-factor authentication. È possibile configurare queste impostazioni di riautenticazione in base alle esigenze per il proprio ambiente e l'esperienza utente desiderata.

La Azure AD configurazione predefinita per la frequenza di accesso dell'utente è una finestra in sequenza di 90 giorni. La richiesta di credenziali agli utenti è spesso un'operazione sensata, ma può essere riattivata. Se gli utenti vengono sottoposti a training per immettere le proprie credenziali senza pensare, possono fornirle involontariamente a una richiesta di credenziali dannose.

Potrebbe sembrare allarmante non chiedere a un utente di eseguire l'accesso, anche se qualsiasi violazione dei criteri IT revoca la sessione. Alcuni esempi includono una modifica della password, un dispositivo incompliant o un'operazione di disabilitazione dell'account. È anche possibile revocare in modo esplicito le [sessioni degli utenti usando PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

Questo articolo illustra in dettaglio le configurazioni consigliate e il modo in cui le diverse impostazioni funzionano e interagiscono tra loro.

## <a name="recommended-settings"></a>Impostazioni consigliate

Per offrire agli utenti il giusto equilibrio tra sicurezza e facilità d'uso chiedendo loro di accedere alla frequenza corretta, è consigliabile eseguire le configurazioni seguenti:

* Se Azure AD Premium:
    * Abilita Single Sign-On (SSO) tra le applicazioni usando i [dispositivi gestiti](../devices/overview.md) o l'accesso [Single](../hybrid/how-to-connect-sso.md)Sign-on facile.
    * Se è necessaria la riautenticazione, usare un [criterio di frequenza di accesso con](../conditional-access/howto-conditional-access-session-lifetime.md)accesso condizionale.
    * Per gli utenti che accedono da dispositivi non gestiti o da scenari di dispositivi mobili, usare l'accesso condizionale per abilitare le sessioni del browser permanenti e i criteri di frequenza di accesso.
* Se si hanno Microsoft 365 licenze per le app o il livello gratuito Azure AD:
    * Abilita Single Sign-On (SSO) tra le applicazioni usando i [dispositivi gestiti](../devices/overview.md) o l'accesso [Single](../hybrid/how-to-connect-sso.md)Sign-on facile.
    * Mantenere abilitata l'opzione *resta connesso* e consentire agli utenti di accettarla.
* Per gli scenari di dispositivi mobili, assicurarsi che gli utenti usino l'app Microsoft Authenticator. Questa app viene usata come broker per altre Azure AD app federate e riduce le richieste di autenticazione nel dispositivo.

La nostra ricerca mostra che queste impostazioni sono appropriate per la maggior parte dei tenant. Alcune combinazioni di queste impostazioni, ad esempio *ricordano* l'autenticazione a più fattori e *rimangono cantate*, possono richiedere agli utenti di eseguire l'autenticazione troppo spesso. Le normali richieste di riautenticazione non sono valide per la produttività degli utenti e possono renderle più vulnerabili agli attacchi.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Impostazioni di configurazione della durata della sessione Azure AD

Per ottimizzare la frequenza dei messaggi di richiesta di autenticazione per gli utenti, è possibile configurare le opzioni di durata della sessione Azure AD. Comprendere le esigenze dell'azienda e degli utenti e configurare le impostazioni che forniscono il migliore equilibrio per l'ambiente.

### <a name="evaluate-session-lifetime-policies"></a>Valutare i criteri di durata della sessione

Senza alcuna impostazione di durata della sessione, non sono presenti cookie permanenti nella sessione del browser. Ogni volta che un utente chiude e apre il browser, riceve una richiesta di riautenticazione. Nei client Office, il periodo di tempo predefinito è una finestra in sequenza di 90 giorni. Con questa configurazione di Office predefinita, se l'utente ha reimpostato la password o è stato inattività di oltre 90 giorni, l'utente deve ripetere l'autenticazione con tutti i fattori richiesti (primo e secondo fattore).

Un utente potrebbe visualizzare più richieste di autenticazione a più fattori in un dispositivo che non ha un'identità in Azure AD. Vengono restituiti più messaggi di richiesta quando ogni applicazione dispone di un proprio token di aggiornamento OAuth che non è condiviso con altre app client. In questo scenario, l'autenticazione a più fattori richiede più volte poiché ogni applicazione richiede che un token di aggiornamento OAuth venga convalidato con l'autenticazione a più fattori.

In Azure AD, i criteri più restrittivi per la durata della sessione determinano quando l'utente deve ripetere l'autenticazione. Si consideri lo scenario seguente:

* Si Abilita l' *accesso rimanente*, che usa un cookie del browser persistente e
* È anche possibile abilitare l'autenticazione a più fattori *per 14 giorni*

In questo scenario di esempio, l'utente deve ripetere l'autenticazione ogni 14 giorni. Questo comportamento segue i criteri più restrittivi, anche se l'opzione *Mantieni l'accesso* da solo non richiede l'utente per la riautenticazione nel browser.

### <a name="managed-devices"></a>Dispositivi gestiti

I dispositivi aggiunti a Azure AD usando Azure AD join o Aggiunta ad Azure AD ibrido ricevono un [token di aggiornamento primario (PRT)](../devices/concept-primary-refresh-token.md) per usare Single Sign-on (SSO) tra le applicazioni. Questo PRT consente a un utente di accedere una sola volta sul dispositivo e consente al personale IT di assicurarsi che siano soddisfatti gli standard per la sicurezza e la conformità. Se a un utente deve essere richiesto di accedere con maggiore frequenza a un dispositivo aggiunto per alcune app o scenari, è possibile ottenere questo risultato usando la [frequenza di accesso con accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="show-option-to-remain-signed-in"></a>Mostra l'opzione per mantenere l'accesso

Quando un utente seleziona **Sì** nell'opzione *rimane connesso?* durante l'accesso, viene impostato un cookie permanente nel browser. Questo cookie persistente memorizza il primo e il secondo fattore e si applica solo alle richieste di autenticazione nel browser.

![Screenshot della richiesta di esempio per restare connessi](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Se si dispone di una licenza di Azure AD Premium 1, è consigliabile utilizzare i criteri di accesso condizionale per la *sessione del browser permanente*. Questo criterio sovrascrive l'impostazione *Stay connesso?* e offre un'esperienza utente migliorata. Se non si ha una licenza di Azure AD Premium 1, è consigliabile abilitare l'impostazione Stay signed in per gli utenti.

Per ulteriori informazioni sulla configurazione dell'opzione per consentire agli utenti di rimanere connessi, vedere [personalizzare la pagina di accesso Azure ad](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page).

### <a name="remember-multi-factor-authentication"></a>Memorizza Multi-Factor Authentication  

Questa impostazione consente di configurare i valori compresi tra 1-365 giorni e imposta un cookie permanente nel browser quando un utente seleziona l'opzione non visualizzare più questo messaggio **per X giorni** all'accesso.

![Screenshot del prompt di esempio per approvare una richiesta di accesso](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

Sebbene questa impostazione riduca il numero di autenticazioni nelle app Web, aumenta il numero di autenticazioni per i client di autenticazione moderni, ad esempio i client Office. Questi client in genere richiedono solo dopo la reimpostazione della password o l'inattività di 90 giorni. Tuttavia, l'impostazione di questo valore su un valore inferiore a 90 giorni abbrevia le richieste di autenticazione a più fattori predefinite per i client Office e aumenta la frequenza di riautenticazione. Se usato in **combinazione con i criteri di accesso** condizionale o con accesso condizionale, può aumentare il numero di richieste di autenticazione.

Se si usa l'autenticazione a più fattori e si hanno Azure AD Premium 1 licenze, provare *a eseguire la* migrazione di queste impostazioni alla frequenza di accesso con accesso condizionale. In caso contrario, provare *a usare Mantieni l'accesso?* .

Per altre informazioni, vedere [ricordare multi-factor authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

### <a name="authentication-session-management-with-conditional-access"></a>Gestione delle sessioni di autenticazione con accesso condizionale

La **frequenza di accesso** consente all'amministratore di scegliere una frequenza di accesso valida per il primo e il secondo fattore sia nel client che nel browser. È consigliabile usare queste impostazioni, oltre a usare i dispositivi gestiti, in scenari in cui è necessario limitare la sessione di autenticazione, ad esempio per le applicazioni aziendali critiche.

La **sessione persistente del browser** consente agli utenti di rimanere connessi dopo la chiusura e la riapertura della finestra del browser. Analogamente all'impostazione *resta connesso* , imposta un cookie permanente nel browser. Tuttavia, poiché è stato configurato dall'amministratore, non è necessario che l'utente selezioni **Sì** nell'opzione *Stay connesso?* per offrire un'esperienza utente migliore. Se si usa l'opzione *resta connesso* , è consigliabile abilitare invece i criteri di **sessione persistenti del browser** .

Per altre informazioni. vedere [configurare la gestione delle sessioni di autenticazione con accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="configurable-token-lifetimes"></a>Durata del token configurabile

Questa impostazione consente la configurazione della durata per il token emesso da Azure Active Directory. Questo criterio viene sostituito dalla *gestione delle sessioni di autenticazione con accesso condizionale*. Se oggi si usano le *durate dei token configurabili* , è consigliabile avviare la migrazione ai criteri di accesso condizionale.

## <a name="review-your-tenant-configuration"></a>Verificare la configurazione del tenant  

A questo punto, dopo aver compreso il funzionamento delle diverse impostazioni e la configurazione consigliata, è possibile verificare la configurazione dei tenant e apportare modifiche di conseguenza:

Per configurare o rivedere l'opzione Mantieni l' *accesso* , completare i passaggi seguenti:

1. Nel portale di Azure AD cercare e selezionare *Azure Active Directory*.
1. Selezionare informazioni personalizzate distintive dell' **azienda**, quindi, per ogni impostazione locale, scegliere **Mostra opzione per mantenere l'accesso**.
1. Scegliere *Sì*, quindi fare clic su **Salva**.

Per memorizzare le impostazioni di autenticazione a più fattori, attenersi alla procedura seguente:

1. Nel portale di Azure AD cercare e selezionare *Azure Active Directory*.
1. Selezionare **sicurezza** **, quindi autenticazione**a più fattori.
1. In **Configura**selezionare altre impostazioni di autenticazione a più fattori **basate sul cloud**.
1. Nella pagina *impostazioni del servizio di autenticazione* a più fattori scorrere per **memorizzare le impostazioni di autenticazione**a più fattori. Disabilitare l'impostazione deselezionando la casella di controllo.

Per configurare i criteri di accesso condizionale per la frequenza di accesso e la sessione del browser persistente, attenersi alla procedura seguente:

1. Nel portale di Azure AD cercare e selezionare *Azure Active Directory*.
1. Selezionare **sicurezza**, quindi **accesso condizionale**.
1. Configurare un criterio usando le opzioni di gestione delle sessioni consigliate descritte in questo articolo.

Per esaminare le durate dei token, [usare Azure ad PowerShell per eseguire una query su tutti i criteri di Azure ad](../develop/active-directory-configurable-token-lifetimes.md#prerequisites). Disabilitare tutti i criteri disponibili.

Se nel tenant è abilitata più di un'impostazione, è consigliabile aggiornare le impostazioni in base alle licenze disponibili. Se ad esempio si hanno Azure AD licenze Premium, è consigliabile usare solo i criteri di accesso condizionale per la *frequenza* di accesso e la *sessione del browser permanente*. Se si dispone di Microsoft 365 app o Azure AD licenze gratuite, è necessario usare la configurazione *rimanere connessi?* .

Se è stata abilitata la durata del token configurabile, questa funzionalità verrà rimossa a breve. Pianificare una migrazione a un criterio di accesso condizionale.

Nella tabella seguente sono riepilogate le raccomandazioni basate sulle licenze:

|              | App Azure AD Free e Microsoft 365 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | [Azure ad join](../devices/concept-azure-ad-join.md) o [Azure ad ibrido join](../devices/concept-azure-ad-join-hybrid.md)o l'accesso [SSO](../hybrid/how-to-connect-sso.md) facile per i dispositivi non gestiti. | Aggiunta ad Azure AD<br />Aggiunta ad Azure AD ibrido |
| **Impostazioni di riautenticazione** | Resta connesso                  | Usare i criteri di accesso condizionale per la frequenza di accesso e la sessione del browser persistente |

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, completare l'esercitazione per [proteggere gli eventi di accesso utente con azure multi-factor authentication](tutorial-enable-azure-mfa.md) o [usare i rilevamenti dei rischi per gli accessi degli utenti per attivare multi-factor authentication di Azure](tutorial-risk-based-sspr-mfa.md).
