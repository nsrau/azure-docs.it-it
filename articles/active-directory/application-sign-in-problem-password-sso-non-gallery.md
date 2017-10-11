---
title: Problemi di accesso a un'applicazione della raccolta di Azure AD configurata per il Single Sign-On basato su password | Microsoft Docs
description: Vengono descritte le aree problematiche e offerte linee guida per la risoluzione dei problemi relativi all'accesso alle applicazioni della raccolta di Azure AD configurate per il Single Sign-On basato su password
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c90b61812affb7e7af05cf3e302d045958da59be
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemi di accesso a un'applicazione della raccolta di Azure AD configurata per il Single Sign-On basato su password

Il pannello di accesso è un portale basato sul Web che permette a un utente che ha un account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD) di visualizzare e avviare applicazioni basate sul cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Un utente dotato di edizioni di Azure AD può anche usare le funzionalità di gestione self-service di gruppi e app tramite il pannello di accesso. Il pannello di accesso è separato dal portale di Azure e non richiede una sottoscrizione Azure.

Per usare il servizio Single Sign-On (SSO) basato su password nel pannello di accesso, è necessario installare l'estensione del pannello di accesso nel browser dell'utente. Questa estensione viene scaricata automaticamente quando un utente seleziona un'applicazione configurata per il servizio Single Sign-On basato su password.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Applicazione dei requisiti del browser per il pannello di accesso

Per il pannello di accesso è necessario un browser che supporti JavaScript e in cui sia abilitato CSS. Per usare il servizio Single Sign-On (SSO) basato su password nel pannello di accesso, è necessario installare l'estensione del pannello di accesso nel browser dell'utente. Questa estensione viene scaricata automaticamente quando un utente seleziona un'applicazione configurata per il servizio Single Sign-On basato su password.

Per l'accesso Single Sign-On basato su password il browser dell'utente finale può essere uno dei seguenti:

-   Internet Explorer 8, 9, 10, 11 su Windows 7 o versioni successive

-   Chrome in Windows 7 o versione successiva e MacOS X o versione successiva

-   Firefox 26.0 o versione successiva in Windows XP SP2 o versione successiva e in Mac OS X 10.6 o versione successiva

>[!NOTE]
>L'estensione SSO basata su password sarà disponibile per Edge in Windows 10 quando le estensioni del browser saranno supportate per Edge.
>
>

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

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Impostazione dei Criteri di gruppo per Internet Explorer

È possibile impostare Criteri di gruppo che consentono di installare l'estensione del pannello di accesso per Internet Explorer nei computer degli utenti in modalità remota.

I prerequisiti includono:

-   [Servizi di dominio Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)è già stato configurato e i computer degli utenti sono stati aggiunti al dominio.

-   Per modificare l'oggetto Criteri di gruppo è necessaria l'autorizzazione "Modifica impostazione". Questa autorizzazione è assegnata per impostazione predefinita ai membri dei gruppi di sicurezza seguenti: Domain Administrators, Enterprise Administrators e Group Policy Creator Owners. [Altre informazioni](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Per istruzioni passo passo su come configurare i Criteri di gruppo e distribuirli agli utenti, seguire l'esercitazione [Come distribuire l'estensione Pannello di accesso per Internet Explorer con Criteri di gruppo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Risoluzione dei problemi del pannello di accesso in Internet Explorer

Per accedere a uno strumento diagnostico e per istruzioni passo passo sulla configurazione dell'estensione per IE, seguire la procedura nella sezione sulla [risoluzione dei problemi relativi all'estensione del pannello di accesso per Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-Troubleshoot).

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Come configurare un accesso Single Sign-On basato su password per un'applicazione non inclusa nella raccolta

Per configurare un'applicazione della raccolta di Azure AD è necessario:

-   [Aggiungere un'applicazione non inclusa nella raccolta](#add-a-non-gallery-application)

-   [Configurare l'applicazione per un accesso Single Sign-On basato su password](#configure-the-application-for-password-single-sign-on)

-   [Assegnare gli utenti all'applicazione](#assign-users-to-the-application)

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

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare un accesso Single Sign-On

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Selezionare la modalità **Accesso basato su password**.

9.  Immettere l'**URL di accesso**. Questo è l'URL in cui gli utenti immettono il nome utente e la password per accedere. Verificare che i campi di accesso siano visibili nell'URL.

10. Assegnare gli utenti all'applicazione.

11. È anche possibile specificare le credenziali per conto dell'utente selezionando le righe degli utenti e facendo clic su **Aggiorna credenziali**, quindi immettendo il nome utente e la password per conto degli utenti. In caso contrario, verrà richiesto agli utenti di immettere le credenziali all'avvio.

### <a name="assign-users-to-the-application"></a>Assegnare gli utenti all'applicazione

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

