---
title: Problemi di accesso a un'applicazione usando un collegamento diretto | Microsoft Docs
description: Come risolvere i problemi di accesso a un'applicazione da un URL con collegamento diretto usando Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: b70290a78255905a92e2bb5432369c499fca3f3b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problemi di accesso a un'applicazione usando un collegamento diretto

Il pannello di accesso è un portale basato sul Web e consente a un utente che ha un account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD) di visualizzare e avviare applicazioni basate su cloud a cui l'amministratore di Azure AD ha concesso l'accesso. 

Queste applicazioni sono configurate per conto dell'utente nel portale di Azure AD. Perché l'applicazione sia visibile nel pannello di accesso, è necessario che sia configurata correttamente e assegnata all'utente o a un gruppo di cui l'utente è membro.

I collegamenti diretti o gli URL di accesso utente sono collegamenti che gli utenti potrebbero utilizzare per accedere alle applicazioni con SSO basato su password direttamente dalla barra degli indirizzi del browser. Usando questo collegamento gli utenti accedono automaticamente all'applicazione senza dover passare prima per il pannello di accesso. Questo è lo stesso collegamento usato dagli utenti per accedere a queste applicazioni dall'applicazione di avvio di Office 365.

## <a name="general-issues-to-check-first"></a>Problemi generali da verificare prima

-   Assicurarsi di usare un **browser** che soddisfi i requisiti minimi per il pannello di accesso.

-   Verificare che il browser dell'utente abbia aggiunto l'URL ai **siti attendibili** dell'applicazione.

-   Assicurarsi di controllare che l'applicazione sia **configurata** correttamente.

-   Verificare che l'account dell'utente sia **abilitato** per l'accesso.

-   Verificare che l'account dell'utente non sia **bloccato**.

-   Verificare che la **password dell'utente non sia scaduta o non sia stata dimenticata**.

-   Verificare che **Multi-Factor Authentication** non blocchi l'accesso utente.

-   Verificare che un criterio di **accesso condizionale** o di **protezione delle identità** non blocchi l'accesso utente.

-   Verificare che le **informazioni di contatto per l'autenticazione** di un utente siano aggiornate per permettere l'applicazione di Multi-Factor Authentication o di criteri di accesso condizionale.

-   Assicurarsi anche di cancellare i cookie del browser e riprovare ad accedere.

## <a name="checking-the-deeplink"></a>Verifica del collegamento diretto

Per verificare se si dispone del collegamento diretto corretto, seguire questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

7.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

8.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

9.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

10. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

11. Selezionare l'applicazione di cui si desidera verificare il .collegamento diretto.

12. Trovare l'etichetta **URL accesso utente**. Il collegamento diretto deve corrispondere a questo URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Come installare l'estensione del browser per il pannello di accesso

Per installare l'estensione del browser per il pannello di accesso, seguire questa procedura:

1.  Aprire il [pannello di accesso](https://myapps.microsoft.com) in uno dei browser supportati e accedere come **utente** ad Azure AD.

2.  Fare clic su un'**applicazione con accesso SSO basato su password** nel pannello di accesso.

3.  Quando viene richiesto di installare il software, selezionare **Installa ora**.

4.  A seconda del browser in uso, si verrà indirizzati al collegamento per il download. **Aggiungere** l'estensione al browser.

5.  Se richiesto dal browser, scegliere se **abilitare** o **consentire** l'uso dell'estensione.

6.  Al termine dell'installazione, **riavviare** la sessione del browser.

7.  Accedere al pannello di accesso e verificare se è possibile **avviare** le applicazioni con accesso SSO basato su password.

È anche possibile scaricare l'estensione per Chrome e Firefox dai collegamenti diretti seguenti:

-   [Estensione Pannello di accesso per Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Estensione Pannello di accesso per Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Come configurare l'accesso Single Sign-On basato su password per un'applicazione della raccolta di Azure AD

Per configurare un'applicazione della raccolta di Azure AD è necessario:

-   [Aggiungere un'applicazione dalla raccolta di Azure AD](#add-an-application-from-the-Azure-AD-gallery)

-   [Configurare l'applicazione con l'accesso Single Sign-On basato su password](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Aggiungere un'applicazione dalla raccolta di Azure AD

Per aggiungere un'applicazione dalla raccolta di Azure AD, seguire questa procedura:

1.  Aprire il [portale di Azure](https://portal.azure.com) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic sul pulsante **Aggiungi** nell'angolo superiore destro del pannello **Applicazioni aziendali**.

6.  Nella casella di testo **Immettere un nome** della sezione **Aggiungi dalla raccolta** digitare il nome dell'applicazione.

7.  Selezionare l'applicazione che si vuole configurare con l'accesso Single Sign-On.

8.  Prima di aggiungere l'applicazione, è possibile modificarne il nome usando la casella di testo **Nome**.

9.  Fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

Dopo un breve periodo di tempo sarà possibile visualizzare il pannello di configurazione dell'applicazione.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurare l'applicazione per un accesso Single Sign-On basato su password

Per configurare un accesso Single Sign-On per un'applicazione, seguire i passaggi seguenti:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Selezionare la modalità **Accesso basato su password**.

9.  [Assegnare gli utenti all'applicazione](#how-to-assign-a-user-to-an-application-directly).

10. È anche possibile specificare le credenziali per conto dell'utente selezionando le righe degli utenti e facendo clic su **Aggiorna credenziali**, quindi immettendo il nome utente e la password per conto degli utenti. In caso contrario, verrà richiesto agli utenti di immettere le credenziali all'avvio.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Come configurare l'accesso Single Sign-On basato su password per un'applicazione non inclusa nella raccolta

Per configurare un'applicazione della raccolta di Azure AD è necessario:

-   [Aggiungere un'applicazione non inclusa nella raccolta](#add-a-non-gallery-application)

-   [Configurare l'applicazione per un accesso Single Sign-On basato su password](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Aggiungere un'applicazione non inclusa nella raccolta

Per aggiungere un'applicazione dalla raccolta di Azure AD, seguire questa procedura:

1.  Aprire il [portale di Azure](https://portal.azure.com) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic sul pulsante **Aggiungi** nell'angolo superiore destro del pannello **Applicazioni aziendali**.

6.  Fare clic su**Applicazione non nella raccolta**.

7.  Immettere il nome dell'applicazione nella casella di testo **Nome**. Fare clic su **Aggiungi**.

Dopo un breve periodo di tempo, sarà possibile visualizzare il pannello di configurazione dell'applicazione.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurare l'applicazione per un accesso Single Sign-On basato su password

Per configurare un accesso Single Sign-On per un'applicazione, seguire i passaggi seguenti:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

    1.  Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Selezionare la modalità **Accesso basato su password**.

9.  Immettere l'**URL di accesso**. Questo è l'URL in cui gli utenti immettono il nome utente e la password per accedere. Verificare che i campi di accesso siano visibili nell'URL.

10. Assegnare gli utenti all'applicazione.

11. È anche possibile specificare le credenziali per conto dell'utente selezionando le righe degli utenti e facendo clic su **Aggiorna credenziali**, quindi immettendo il nome utente e la password per conto degli utenti. In caso contrario, verrà richiesto agli utenti di immettere le credenziali all'avvio.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Come assegnare un utente direttamente a un'applicazione

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7.  Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di navigazione a sinistra dell'applicazione.

8.  Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il pannello **Aggiungi assegnazione**.

9.  Fare clic sul selettore **Utenti e gruppi** nel pannello **Aggiungi assegnazione**.

10. Nella casella di ricerca **Cerca per nome o indirizzo di posta** digitare il **nome completo**  o l'**indirizzo di posta elettronica** dell'utente oggetto dell'assegnazione.

11. Passare il puntatore sull'**utente** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **selezionato**.

13. Dopo avere selezionato gli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel pannello **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione agli utenti selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati saranno in grado di avviare queste applicazioni nel pannello di accesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se questi passaggi di risoluzione dei problemi non risolvono il problema 

aprire un ticket di supporto con le informazioni seguenti, se disponibili:

-   ID errore di correlazione

-   UPN (indirizzo di posta elettronica dell'utente)

-   ID tenant

-   Tipo di browser

-   Fuso orario e ora o intervallo di tempo durante il quale si verifica l'errore

-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)
