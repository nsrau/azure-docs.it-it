---
title: Problemi durante l'accesso all'app galleria di Azure AD configurata per SSO . Documenti Microsoft
description: Come risolvere i problemi relativi a un'applicazione Raccolta Azure AD configurata per l'accesso Single Sign-On con password.
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
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381300"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemi di accesso con un'app raccolta di Azure AD configurata per SSOSign-in problems with an Azure AD gallery app configured for SSO

Il pannello di accesso è un portale basato sul Web. Consente agli utenti che dispongono di account aziendali o dell'istituto di istruzione di Azure Active Directory (Azure AD) di accedere alle app basate su cloud per cui dispongono delle autorizzazioni. Gli utenti che dispongono di edizioni di Azure AD possono anche usare funzionalità self-service per la gestione di app e gruppi tramite il pannello di accesso.

Il pannello di accesso è separato dal portale di Azure.Access Panel is separate from the Azure portal. Gli utenti non hanno bisogno di una sottoscrizione di Azure per usare il pannello di accesso.

Per utilizzare L'accesso Single Sign-On (SSO) basato su password nel Pannello di accesso, è necessario che nel browser sia installata l'estensione Pannello di accesso. L'estensione viene scaricata automaticamente quando selezioni un'app configurata per SSO basato su password.

## <a name="browser-requirements-for-access-panel"></a>Requisiti del browser per il pannello di accesso

Il pannello di accesso richiede un browser che supporta JavaScript e ha CSS abilitato.

I seguenti browser supportano SSO basato su password:

- Internet Explorer 8, 9, 10 e 11 in Windows 7 o versioni successive

- Chrome su Windows 7 o versioni successive o su MacOS X o versioni successive

- Firefox 26.0 o versioni successive su Windows XP SP2 o versioni successive o su Mac OS X 10.6 o versioni successive

>[!NOTE]
>L'estensione SSO basata su password diventa disponibile per Microsoft Edge in Windows 10 quando è stato aggiunto il supporto per le estensioni del browser a Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Installare l'estensione Browser del pannello di accesso

A tale scopo, seguire questa procedura:

1. Aprire [il pannello](https://myapps.microsoft.com) di accesso in un browser supportato e accedere come utente in Azure AD.

2. Selezionare un'app abilitata per SSO per la password nel Pannello di accesso.

3. Quando richiesto, selezionare **Installa ora**.

4. Verrai indirizzato a un link di download basato sul tuo browser. Selezionare **Aggiungi** per installare l'estensione del browser.

5. Se richiesto, selezionare **Abilita** o **Consenti**.

6. Dopo l'installazione, riavviare il browser.

7.  Accedere al pannello di accesso e verificare se è possibile avviare le app abilitate per SSO con password.

Puoi anche scaricare direttamente le estensioni per Chrome e Firefox attraverso questi link:

-   [Estensione del pannello di accesso Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione Firefox Access Panel](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurare un criterio di gruppo per Internet Explorer

È possibile impostare un criterio di gruppo che consente di installare in remoto l'estensione del Pannello di accesso per Internet Explorer nei computer degli utenti.

Questi sono i prerequisiti:

-   [Servizi](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) di dominio Active Directory deve essere configurato e i computer degli utenti devono essere aggiunti al dominio.

-   Si dispone dell'autorizzazione "Modifica impostazioni" per modificare l'oggetto Criteri di gruppo (GPO). Questa autorizzazione è assegnata per impostazione predefinita ai membri dei gruppi di sicurezza seguenti: Domain Administrators, Enterprise Administrators e Group Policy Creator Owners. [Scopri di più](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Per configurare i criteri di gruppo e distribuirlo agli utenti, vedere [Come distribuire l'estensione Del Pannello](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)di accesso per Internet Explorer utilizzando Criteri di gruppo .

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Risolvere i problemi relativi al pannello di accesso in Internet Explorer

Per accedere a uno strumento di diagnostica e alle istruzioni per configurare l'estensione, vedere [Risolvere i problemi relativi all'estensione del Pannello](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)di accesso per Internet Explorer.

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Configurare l'accesso SSO con password per un'app della raccolta di Azure ADConfigure password SSO for an Azure AD gallery app

Per configurare un'app dalla raccolta di Azure AD, è necessario eseguire le operazioni seguenti:To configure an app from the Azure AD gallery, you need to do these things:

-   Aggiungere l'app dalla raccolta di Azure ADAdd the app from the Azure AD gallery
-   [Configurare l'app per l'accesso Single Sign-On con password](#configure-the-app-for-password-sso)
-   [Assegnare utenti all'app](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Aggiungere l'app dalla raccolta di Azure ADAdd the app from the Azure AD gallery

A tale scopo, seguire questa procedura:

1. Aprire il portale di [Azure](https://portal.azure.com) e accedere come amministratore globale o coamministratore.

2. Selezionare **Tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione di Azure AD.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare Azure Active **Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **Aggiungi** nell'angolo superiore destro del riquadro **Applicazioni aziendali.**

6. Nella sezione **Aggiungi dalla raccolta** digitare il nome dell'app nella casella **Immettere un nome.**

7. Selezionare l'app che si desidera configurare per SSO.

8. *Facoltativo:* Prima di aggiungere l'app, è possibile modificarne il nome nella casella **Nome.**

9. Fare clic su **Aggiungi** per aggiungere l'app.

   Dopo un breve ritardo, sarà possibile visualizzare il riquadro di configurazione dell'app.

### <a name="configure-the-app-for-password-sso"></a>Configurare l'app per l'applicazione SSO password

A tale scopo, seguire questa procedura:

1. Aprire il portale di [Azure](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Selezionare **Tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione di Azure AD.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare Azure Active **Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se l'app desiderata non è visualizzata, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni.** Impostare l'opzione **Mostra su** "Tutte le applicazioni".

6. Selezionare l'app che si desidera configurare per SSO.

7. Dopo il caricamento dell'app, seleziona **Single Sign-On** nel riquadro sul lato sinistro dell'app.

8. Selezionare Modalità **di accesso basata su password.**

9. Assegnare utenti all'app.

10. È inoltre possibile fornire le credenziali per gli utenti. In caso contrario, agli utenti verrà richiesto di immettere le credenziali all'avvio dell'app. A tale scopo, selezionare le righe degli utenti. Selezionare **quindi Aggiorna credenziali** e immettere i nomi utente e le password.

### <a name="assign-users-to-the-app"></a>Assegnare utenti all'app

Per assegnare direttamente gli utenti a un'app, attenersi alla seguente procedura:

1. Aprire il portale di [Azure](https://portal.azure.com/) e accedere come amministratore globale.

2. Selezionare **Tutti i servizi** nel riquadro di spostamento sul lato sinistro per aprire l'estensione di Azure AD.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare Azure Active **Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro di spostamento di Azure AD.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle applicazioni.

   > [!NOTE]
   > Se l'app desiderata non è visualizzata, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni.** Impostare l'opzione **Mostra su** "Tutte le applicazioni".

6. Nell'elenco selezionare l'app a cui si desidera assegnare un utente.

7. Dopo il caricamento dell'applicazione, seleziona **Utenti e gruppi** nel riquadro di spostamento dell'app sul lato sinistro.

8. Selezionare **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il riquadro Aggiungi **assegnazione.**

9. Selezionare **Utenti e gruppi** nel riquadro Aggiungi **assegnazione.**

10. Nella casella **Cerca per nome o indirizzo** di posta elettronica digitare il nome completo o l'indirizzo di posta elettronica dell'utente che si desidera assegnare.

11. Passare il mouse sull'utente nell'elenco. Selezionare la casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato.**

12. *Facoltativo:* Per aggiungere un altro utente, digitare un altro nome o indirizzo di posta elettronica nella casella **Cerca per nome o indirizzo** di posta elettronica e quindi selezionare la casella di controllo per aggiungere l'utente all'elenco **Selezionato.**

13. Al termine della selezione degli utenti, fare clic su **Seleziona** per aggiungerli all'elenco di utenti e gruppi assegnati all'app.

14. *Facoltativo:* Fare clic su **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per selezionare un ruolo da assegnare agli utenti selezionati.

15. Selezionare **Assegna** per assegnare l'app agli utenti selezionati.

    Dopo un breve ritardo, gli utenti saranno in grado di accedere a tali applicazioni dal pannello di accesso.

## <a name="request-support"></a>Richiedere supporto 
Se viene visualizzato un messaggio di errore quando si configura SSO e si assegnano gli utenti, aprire un ticket di supporto. Includere la maggior parte delle seguenti informazioni possibili:

-   ID errore di correlazione
-   UPN (indirizzo di posta elettronica dell'utente)
-   ID tenant
-   Tipo di browser
-   Fuso orario e intervallo di tempo/ora in cui si è verificato l'errore
-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)
