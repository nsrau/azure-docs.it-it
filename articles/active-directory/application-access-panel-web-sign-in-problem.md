---
title: Problemi di accesso al sito Web del pannello di accesso | Microsoft Docs
description: Linee guida per risolvere i possibili problemi durante il tentativo di accedere per usare il pannello di accesso
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
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: bbd57547a0cc486a9cf7c8030dccaaf6f46c9860
ms.lasthandoff: 04/11/2017


---

# <a name="problem-signing-in-to-the-access-panel-website"></a>Problemi di accesso al sito Web del pannello di accesso

Il pannello di accesso è un portale basato sul Web che permette a un utente che ha un account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD) di visualizzare e avviare applicazioni basate sul cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Un utente dotato di edizioni di Azure AD può anche usare le funzionalità di gestione self-service di gruppi e app tramite il pannello di accesso. Il pannello di accesso è separato dal portale di Azure e non richiede una sottoscrizione Azure.

Gli utenti possono accedere al pannello di accesso se hanno un account aziendale o dell'istituto di istruzione in Azure AD.

-   Gli utenti possono essere autenticati direttamente tramite Azure AD.

-   Gli utenti possono essere autenticati tramite Active Directory Federation Services (AD FS).

-   Gli utenti possono essere autenticati tramite Windows Server Active Directory.

Se un utente ha una sottoscrizione per Azure o un abbonamento a Office 365 e ha usato il portale di Azure o un'applicazione di Office 365, potrà usare il pannello di accesso in tutta semplicità senza dover eseguire di nuovo l'accesso. Gli utenti non autenticati dovranno accedere usando il nome utente e la password del proprio account in Azure AD. Se l'organizzazione ha configurato la federazione, sarà sufficiente digitare il nome utente.

## <a name="general-issues-to-check-first"></a>Problemi generali da verificare per primi 

-   Verificare che l'utente acceda all'**URL corretto**: <https://myapps.microsoft.com>

-   Verificare che il browser dell'utente abbia aggiunto l'URL ai **siti attendibili**

-   Verificare che l'account dell'utente sia **abilitato** per l'accesso.

-   Verificare che l'account dell'utente non sia **bloccato**.

-   Verificare che la **password dell'utente non sia scaduta o non sia stata dimenticata**.

-   Verificare che **Multi-Factor Authentication** non blocchi l'accesso utente.

-   Verificare che un criterio di **accesso condizionale** o di **protezione delle identità** non blocchi l'accesso utente.

-   Verificare che le **informazioni di contatto per l'autenticazione** di un utente siano aggiornate per permettere l'applicazione di Multi-Factor Authentication o di criteri di accesso condizionale.

-   Assicurarsi anche di cancellare i cookie del browser e riprovare ad accedere.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Applicazione dei requisiti del browser per il pannello di accesso

Per il pannello di accesso è necessario un browser che supporti JavaScript e in cui sia abilitato CSS. Per usare Single Sign-On (SSO) basato su password, è necessario installare l'estensione del pannello di accesso nel browser dell'utente. Questa estensione viene scaricata automaticamente quando un utente seleziona un'applicazione configurata per il servizio Single Sign-On basato su password.

Per l'accesso Single Sign-On basato su password il browser dell'utente finale può essere uno dei seguenti:

-   Internet Explorer 8, 9, 10, 11 su Windows 7 o versioni successive

-   Chrome in Windows 7 o versione successiva e MacOS X o versione successiva

-   Firefox 26.0 o versione successiva in Windows XP SP2 o versione successiva e in Mac OS X 10.6 o versione successiva

>[!NOTE] 
>L'estensione SSO basata su password sarà disponibile per Edge in Windows 10 quando le estensioni del browser saranno supportate per Edge.
>
>

## <a name="problems-with-the-users-account"></a>Problemi relativi all'account dell'utente

L'accesso al pannello di accesso può essere bloccato a causa di un problema relativo all'account dell'utente. Di seguito sono riportate alcune soluzioni per i problemi relativi agli utenti e alle impostazioni degli account:

-   [Controllare se esiste un account utente in Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Controllare lo stato dell'account di un utente](#check-a-users-account-status)

-   [Reimpostare la password di un utente](#reset-a-users-password)

-   [Abilitare la reimpostazione self-service delle password](#enable-self-service-password-reset)

-   [Controllare lo stato di autenticazione a più fattori di un utente](#check-a-users-multi-factor-authentication-status)

-   [Controllare le informazioni di contatto per l'autenticazione di un utente](#check-a-users-authentication-contact-info)

-   [Controllare le appartenenze ai gruppi di un utente ](#check-a-users-group-memberships)

-   [Controllare le licenze assegnate di un utente](#check-a-users-assigned-licenses)

-   [Assegnare una licenza a un utente](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Controllare se esiste un account utente in Azure Active Directory

Per controllare se l'account di un utente è presente, eseguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca di filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Controllare le proprietà dell'oggetto utente per assicurarsi che vengano specificate come previsto e che non manchi alcun dato.

### <a name="check-a-users-account-status"></a>Controllare lo stato dell'account di un utente

Per controllare lo stato dell'account di un utente, eseguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca di filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Profilo**.

8.  In **Impostazioni** assicurarsi che l'opzione **Blocca l'accesso** sia impostata su **No**.

### <a name="reset-a-users-password"></a>Reimpostare la password di un utente

Per reimpostare la password di un utente, eseguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca di filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic sul pulsante **Reimposta password** nella parte superiore del pannello dell'utente.

8.  Fare clic sul pulsante **Reimposta password** nel pannello **Reimposta password** visualizzato.

9.  Copiare la **password temporanea** o **immettere una nuova password** per l'utente.

10. Comunicare questa nuova password all'utente, che potrebbe doverla modificare durante il successivo accesso ad Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

Per abilitare la reimpostazione self-service delle password, eseguire questa procedura di distribuzione:

-   [Consentire agli utenti di reimpostare le password di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Consentire agli utenti di reimpostare o modificare le password locali di Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Controllare lo stato di autenticazione a più fattori di un utente

Per controllare lo stato di autenticazione a più fattori di un utente, eseguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca di filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  Fare clic sul pulsante **Multi-Factor Authentication** nella parte superiore del pannello.

7.  Quando viene caricato il **portale di amministrazione di Multi-Factor Authentication**, assicurarsi di passare alla scheda **Utenti**.

8.  Trovare l'utente nell'elenco degli utenti tramite una ricerca, l'applicazione di un filtro o l'ordinamento.

9.  Selezionare l'utente nell'elenco di utenti e scegliere **Abilita**, **Disabilita** o **Applica** per l'autenticazione a più fattori nel modo desiderato.

   >[!NOTE]
   >Se lo stato di un utente è impostato su **Applicato**, è possibile impostarlo temporaneamente su **Disattivato** per permettere all'utente di accedere di nuovo al proprio account. Quando l'utente è connesso, è possibile modificarne di nuovo lo stato in **Attivato** per chiedergli di registrare di nuovo le informazioni di contatto durante il successivo accesso. In alternativa, è possibile eseguire la procedura descritta in [Controllare le informazioni di contatto per l'autenticazione di un utente](#check-a-users-authentication-contact-info) per verificare o impostare questi dati per l'utente.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Controllare le informazioni di contatto per l'autenticazione di un utente

Per controllare le informazioni di contatto per l'autenticazione di un utente usate per l'autenticazione a più fattori, l'accesso condizionale, la protezione delle identità e la reimpostazione delle password, eseguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca di filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Profilo**.

8.  Scorrere fino a **Informazioni di contatto per l'autenticazione**.

9.  Fare clic su **Verifica** per controllare i dati registrati per l'utente e aggiornarli nel modo necessario.

### <a name="check-a-users-group-memberships"></a>Controllare le appartenenze ai gruppi di un utente

Per controllare le appartenenze ai gruppi di un utente, eseguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca di filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Gruppi** per visualizzare i gruppi di cui l'utente è membro.

### <a name="check-a-users-assigned-licenses"></a>Controllare le licenze assegnate di un utente

Per controllare le licenze assegnate di un utente, eseguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca di filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Licenze** per visualizzare le licenze attualmente assegnate all'utente.

### <a name="assign-a-user-a-license"></a>Assegnare una licenza a un utente 

Per assegnare una licenza a un utente, eseguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca di filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Licenze** per visualizzare le licenze attualmente assegnate all'utente.

8.  Fare clic sul pulsante **Assegna** .

9.  Selezionare **uno o più prodotti** nell'elenco dei prodotti disponibili.

10. **Facoltativo**: fare clic sulla voce **Opzioni di assegnazione** per assegnare in modo granulare i prodotti. Fare clic su **OK** al termine.

11. Fare clic sul pulsante **Assegna** per assegnare queste licenze all'utente.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se questi passaggi per la risoluzione dei problemi non risolvono il problema

Aprire un ticket di supporto con le informazioni seguenti, se disponibili:

-   ID errore di correlazione

-   UPN (indirizzo di posta elettronica dell'utente)

-   ID tenant

-   Tipo di browser

-   Fuso orario e ora o intervallo di tempo durante il quale si verifica l'errore

-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)

