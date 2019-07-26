---
title: Problemi di accesso a Azure AD app della raccolta configurata per SSO | Microsoft Docs
description: Come risolvere i problemi relativi a un'applicazione raccolta Azure AD configurata per l'accesso Single Sign-on basato su password.
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
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381300"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemi di accesso con un'app della raccolta Azure AD configurata per SSO

Il pannello di accesso è un portale basato sul Web. Consente agli utenti che dispongono di account aziendali o dell'Istituto di istruzione Azure Active Directory (Azure AD) di accedere alle app basate sul cloud per le quali dispongono di autorizzazioni. Gli utenti che hanno Azure AD edizioni possono anche usare le funzionalità di gestione self-service di gruppi e app tramite il pannello di accesso.

Il pannello di accesso è separato dal portale di Azure. Gli utenti non hanno bisogno di una sottoscrizione di Azure per usare il pannello di accesso.

Per usare l'accesso Single Sign-on (SSO) basato su password nel pannello di accesso, è necessario installare l'estensione del pannello di accesso nel browser. L'estensione viene scaricata automaticamente quando si seleziona un'app configurata per l'accesso SSO basato su password.

## <a name="browser-requirements-for-access-panel"></a>Requisiti del browser per il pannello di accesso

Il pannello di accesso richiede un browser che supporta JavaScript ed è abilitato per CSS.

I browser seguenti supportano l'accesso SSO basato su password:

- Internet Explorer 8, 9, 10 e 11 in Windows 7 o versione successiva

- Chrome in Windows 7 o versioni successive o MacOS X o versione successiva

- Firefox 26,0 o versione successiva in Windows XP SP2 o versione successiva o in Mac OS X 10,6 o versione successiva

>[!NOTE]
>L'estensione SSO basata su password diventa disponibile per Microsoft Edge in Windows 10 quando è stato aggiunto il supporto per le estensioni del browser a Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Installare l'estensione del browser per il pannello di accesso

Attenersi ai passaggi riportati di seguito.

1. Aprire il [Pannello di accesso](https://myapps.microsoft.com) in un browser supportato e accedere come utente in Azure ad.

2. Selezionare un'app abilitata per la password con accesso SSO nel pannello di accesso.

3. Quando viene richiesto, selezionare **Installa ora**.

4. Si verrà indirizzati a un collegamento di download basato sul browser. Selezionare **Aggiungi** per installare l'estensione del browser.

5. Se richiesto, selezionare **Abilita** o **Consenti**.

6. Al termine dell'installazione, riavviare il browser.

7.  Accedere al pannello di accesso e verificare se è possibile avviare le app abilitate per la password.

È anche possibile scaricare direttamente le estensioni per Chrome e Firefox tramite i collegamenti seguenti:

-   [Estensione pannello di accesso Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione pannello di accesso di Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurare un criterio di gruppo per Internet Explorer

È possibile configurare un criterio di gruppo che consente di installare in modalità remota l'estensione del pannello di accesso per Internet Explorer nei computer degli utenti.

Questi sono i prerequisiti:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) deve essere configurato e i computer degli utenti devono essere aggiunti al dominio.

-   Si dispone dell'autorizzazione "modifica impostazioni" per modificare l'oggetto Criteri di gruppo (GPO). Per impostazione predefinita, i membri dei gruppi di sicurezza seguenti hanno questa autorizzazione: Amministratori di dominio, Amministratori dell'organizzazione e Proprietari autori criteri di gruppo. [Altre informazioni](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

Per configurare i criteri di gruppo e distribuirli agli utenti, vedere [come distribuire l'estensione pannello di accesso per Internet Explorer con criteri di gruppo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Risolvere i problemi del pannello di accesso in Internet Explorer

Per accedere a uno strumento di diagnostica e istruzioni per configurare l'estensione, vedere [risolvere i problemi relativi all'estensione del pannello di accesso per Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Configurare l'accesso SSO basato su password per un'app della raccolta Azure AD

Per configurare un'app dalla raccolta di Azure AD, è necessario eseguire queste operazioni:

-   Aggiungere l'app dalla raccolta di Azure AD
-   [Configurare l'app per l'accesso Single Sign-on basato su password](#configure-the-app-for-password-sso)
-   [Assegnare gli utenti all'app](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Aggiungere l'app dalla raccolta di Azure AD

Attenersi ai passaggi riportati di seguito.

1. Aprire il [portale di Azure](https://portal.azure.com) e accedere come amministratore globale o coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione Azure ad.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** dal riquadro di spostamento Azure ad.

5. Selezionare **Aggiungi** nell'angolo in alto a destra del riquadro **applicazioni aziendali** .

6. Nella sezione **Aggiungi dalla raccolta** Digitare il nome dell'app nella casella **immettere un nome** .

7. Selezionare l'app che si vuole configurare per SSO.

8. *Facoltativo:* Prima di aggiungere l'app, è possibile modificarne il nome nella casella **nome** .

9. Fare clic su **Aggiungi** per aggiungere l'app.

   Dopo un breve ritardo, sarà possibile visualizzare il riquadro di configurazione dell'app.

### <a name="configure-the-app-for-password-sso"></a>Configurare l'app per la password SSO

Attenersi ai passaggi riportati di seguito.

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come amministratore globale o coamministratore.

2. Selezionare **tutti i servizi** nella parte superiore del riquadro di spostamento sul lato sinistro per aprire l'estensione Azure ad.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento Azure ad.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se non viene visualizzata l'app desiderata, usare il controllo **filtro** all'inizio dell' **elenco tutte le applicazioni**. Impostare l'opzione **Mostra** su "tutte le applicazioni".

6. Selezionare l'app che si vuole configurare per SSO.

7. Al termine del caricamento dell'app, selezionare **Single Sign-on** nel riquadro sul lato sinistro dell'app.

8. Selezionare la modalità **accesso basato su password** .

9. Assegnare gli utenti all'app.

10. È anche possibile fornire le credenziali per gli utenti. In caso contrario, agli utenti verrà richiesto di immettere le credenziali all'avvio dell'app. A tale scopo, selezionare le righe degli utenti. Selezionare quindi **Aggiorna credenziali** e immettere i nomi utente e le password.

### <a name="assign-users-to-the-app"></a>Assegnare utenti all'app

Per assegnare gli utenti direttamente a un'app, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come amministratore globale.

2. Selezionare **tutti i servizi** nel riquadro di spostamento sul lato sinistro per aprire l'estensione Azure ad.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **applicazioni aziendali** nel riquadro di spostamento Azure ad.

5. Selezionare **tutte le applicazioni** per visualizzare un elenco delle applicazioni.

   > [!NOTE]
   > Se non viene visualizzata l'app desiderata, usare il controllo **filtro** all'inizio dell' **elenco tutte le applicazioni**. Impostare l'opzione **Mostra** su "tutte le applicazioni".

6. Dall'elenco selezionare l'app a cui si vuole assegnare un utente.

7. Al termine del caricamento dell'applicazione, selezionare **utenti e gruppi** dal riquadro di spostamento dell'app sul lato sinistro.

8. Selezionare **Aggiungi** nella parte superiore dell'elenco **utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione** .

9. Selezionare **utenti e gruppi** nel riquadro **Aggiungi assegnazione** .

10. Nella casella **Cerca per nome o indirizzo di posta elettronica** Digitare il nome completo o l'indirizzo di posta elettronica dell'utente che si desidera assegnare.

11. Passare il puntatore del mouse sull'utente nell'elenco. Selezionare la casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **selezionato** .

12. *Facoltativo:* Per aggiungere un altro utente, digitare un altro nome o indirizzo di posta elettronica nella casella **Cerca per nome o indirizzo di posta elettronica** , quindi selezionare la casella di controllo per aggiungere l'utente all'elenco **selezionato** .

13. Al termine della selezione degli utenti, fare clic su **Seleziona** per aggiungerli all'elenco di utenti e gruppi assegnati all'app.

14. *Facoltativo:* Fare clic su **selezionare un ruolo** nel riquadro **Aggiungi assegnazione** per selezionare un ruolo da assegnare agli utenti selezionati.

15. Selezionare **assegna** per assegnare l'app agli utenti selezionati.

    Dopo un breve ritardo, gli utenti saranno in grado di accedere a tali app dal pannello di accesso.

## <a name="request-support"></a>Richiedere supporto 
Se viene visualizzato un messaggio di errore quando si configura SSO e si assegnano utenti, aprire un ticket di supporto. Includere la maggior parte delle informazioni riportate di seguito:

-   ID errore di correlazione
-   UPN (indirizzo di posta elettronica dell'utente)
-   ID tenant
-   Tipo di browser
-   Fuso orario e tempo/intervallo di tempo in cui si è verificato l'errore
-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)
