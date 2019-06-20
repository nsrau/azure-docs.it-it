---
title: Problema durante l'accesso a un'app della raccolta di Azure AD configurata per SSO basato su password | Microsoft Docs
description: Come risolvere i problemi relativi a un'applicazione della raccolta di Azure AD configurato per single sign-on basato su password.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190319"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemi di accesso con un'app della raccolta di Azure AD configurata per l'accesso SSO

Pannello di accesso è un portale basato sul web. Consente agli utenti che dispongono di lavoro di Azure Active Directory (Azure AD) o dell'istituto di istruzione per accedere alle App basate su cloud che dispongono delle autorizzazioni per account. Gli utenti che dispongono di edizioni di Azure AD possono usare anche gruppi self-service e le funzionalità di gestione delle app dal Pannello di accesso.

Pannello di accesso è separato dal portale di Azure. Gli utenti non debbano una sottoscrizione di Azure per usare il pannello di accesso.

Per usare basato su password single sign-on (SSO) nel Pannello di accesso, è necessario installare l'estensione Pannello di accesso nel browser. L'estensione viene scaricato automaticamente quando si seleziona un'app che è configurata per l'accesso SSO basato su password.

## <a name="browser-requirements-for-access-panel"></a>Requisiti del browser per il pannello di accesso

Pannello di accesso è necessario un browser che supporti JavaScript e abbia CSS abilitato.

I browser seguenti supportano l'accesso SSO basato su password:

- Internet Explorer 8, 9, 10 e 11 su Windows 7 o versioni successive

- Chrome in Windows 7 o versione successiva o in MacOS X o versione successiva

- Firefox 26.0 o versione successiva in Windows XP SP2 o versione successiva o in Mac OS X 10.6 o versione successiva

>[!NOTE]
>L'estensione di accesso SSO basato su password sarà disponibile per Microsoft Edge in Windows 10 quando il supporto per le estensioni del browser è stato aggiunto per Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Installare l'estensione del Browser del pannello accesso

A tale scopo, seguire questa procedura:

1. Aprire [Pannello di accesso](https://myapps.microsoft.com) in un browser supportato e accedere come utente di Azure AD.

2. Selezionare un'app abilitate per la password-SSO nel Pannello di accesso.

3. Quando richiesto, selezionare **Installa ora**.

4. Si verrà indirizzati a un collegamento di download basato sul browser. Selezionare **Add** per installare l'estensione del browser.

5. Se richiesto, selezionare **abilitare** oppure **Consenti**.

6. Dopo l'installazione, riavviare il browser.

7.  Accedere al pannello di accesso e verificare se è possibile avviare le app abilitate per la password-SSO.

È possibile scaricare anche direttamente le estensioni per Chrome e Firefox tramite i collegamenti seguenti:

-   [Estensione Pannello di accesso per Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione Pannello di accesso di Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurare un criterio di gruppo per Internet Explorer

È possibile impostare criteri di gruppo che consente di installare in modalità remota Access Panel extension per Internet Explorer nei computer degli utenti.

Questi sono i prerequisiti:

-   [Servizi di dominio Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) deve essere configurato, e i computer degli utenti devono appartenere al dominio.

-   Si dispone dell'autorizzazione "Modifica impostazioni" per modificare l'oggetto Criteri di gruppo (GPO). Per impostazione predefinita, i membri dei gruppi di sicurezza seguenti hanno questa autorizzazione: Amministratori di dominio, Amministratori dell'organizzazione e Proprietari autori criteri di gruppo. [Altre informazioni](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

Per configurare i criteri di gruppo e distribuirli agli utenti, vedere [come distribuire Access Panel extension per Internet Explorer con criteri di gruppo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Risolvere i problemi di Pannello di accesso in Internet Explorer

Per accedere a uno strumento di diagnostica e le istruzioni per configurare l'estensione, vedere [risolvere i problemi di Access Panel extension per Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Configurare SSO basato su password per un'app della raccolta di Azure AD

Per configurare un'app per la raccolta di Azure AD, è necessario eseguire queste operazioni:

-   Aggiungere l'app dalla raccolta di Azure AD
-   [Configurare l'app per la password single sign-on](#configure-the-app-for-password-sso)
-   [Assegnare utenti all'app](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Aggiungere l'app dalla raccolta di Azure AD

A tale scopo, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com) e accedere come un amministratore globale o un coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento a sinistra per aprire l'estensione Azure AD.

3. Tipo di **Azure Active Directory** nella casella di ricerca filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** dal riquadro di spostamento di Azure AD.

5. Selezionare **Add** nell'angolo superiore destro delle **applicazioni aziendali** riquadro.

6. Nel **Aggiungi dalla raccolta** , quindi digitare il nome dell'app nel **immettere un nome** casella.

7. Selezionare l'app che si desidera configurare per SSO.

8. *Facoltativo:* Prima di aggiungere l'app, è possibile modificarne il nome nel **nome** casella.

9. Fare clic su **Add** per aggiungere l'app.

   Dopo un breve ritardo, sarà possibile visualizzare il riquadro di configurazione dell'app.

### <a name="configure-the-app-for-password-sso"></a>Configurare l'app per la password SSO

A tale scopo, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come un amministratore globale o un coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento a sinistra per aprire l'estensione Azure AD.

3. Tipo di **Azure Active Directory** nella casella di ricerca filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'app che vuole, usare il **filtro** nella parte superiore del controllo il **elenco tutte le applicazioni**. Impostare il **mostrare** opzione per "Tutte le applicazioni."

6. Selezionare l'app che si desidera configurare per SSO.

7. Una volta caricata l'app, selezionare **l'accesso Single sign-on** nel riquadro a sinistra dell'app.

8. Selezionare **Password-based sign-on** modalità.

9. Assegnare utenti all'app.

10. È anche possibile fornire le credenziali per gli utenti. (In caso contrario, gli utenti verranno chiesto di immettere le credenziali all'avvio dell'app.) A tale scopo, selezionare le righe degli utenti. Quindi selezionare **Aggiorna credenziali** e immettere i relativi nomi utente e password.

### <a name="assign-users-to-the-app"></a>Assegnare utenti all'app

Per assegnare gli utenti direttamente a un'app, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come amministratore globale.

2. Selezionare **tutti i servizi** nel riquadro di spostamento a sinistra per aprire l'estensione Azure AD.

3. Tipo di **Azure Active Directory** nella casella di ricerca filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle applicazioni.

   > [!NOTE]
   > Se non viene visualizzata l'app che vuole, usare il **filtro** nella parte superiore del controllo il **elenco tutte le applicazioni**. Impostare il **mostrare** opzione per "Tutte le applicazioni."

6. Nell'elenco, selezionare l'app che si desidera assegnare a un utente.

7. Dopo il caricamento dell'applicazione, selezionare **utenti e gruppi** dal riquadro di spostamento dell'app sul lato sinistro.

8. Selezionare **Add** nella parte superiore del **utenti e gruppi** elenco per aprire il **Aggiungi assegnazione** riquadro.

9. Selezionare **utenti e gruppi** nel **Aggiungi assegnazione** riquadro.

10. Nel **Cerca per nome o indirizzo di posta** casella, digitare il nome completo o posta elettronica dell'utente che si vuole assegnare.

11. Passare il mouse sull'utente nell'elenco. Selezionare la casella di controllo accanto alla foto del profilo dell'utente o il logo per aggiungere l'utente per il **Selected** elenco.

12. *Facoltativo:* Per aggiungere un altro utente, digitare un altro nome o indirizzo di posta nel **Cerca per nome o indirizzo di posta** e quindi selezionare la casella di controllo per aggiungere l'utente per il **selezionati** elenco.

13. Al termine della selezione utenti, fare clic su **seleziona** per aggiungerli all'elenco di utenti e gruppi di utenti assegnati all'app.

14. *Facoltativo:* Fare clic su **selezionare il ruolo** nel **Aggiungi assegnazione** riquadro per selezionare un ruolo da assegnare agli utenti che è stato selezionato.

15. Selezionare **assegnare** assegnare l'app agli utenti selezionati.

    Dopo un breve ritardo, gli utenti saranno in grado di accedere alle App dal Pannello di accesso.

## <a name="request-support"></a>Richiedere supporto 
Se viene visualizzato un messaggio di errore quando si imposta l'accesso SSO e assegnare gli utenti, aprire un ticket di supporto. Includere tutte le informazioni seguenti come possibili:

-   ID errore di correlazione
-   UPN (indirizzo di posta elettronica dell'utente)
-   ID tenant
-   Tipo di browser
-   Fuso orario e ora o intervallo di tempo quando si è verificato l'errore
-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)
