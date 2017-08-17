---
title: Come vengono visualizzate le applicazioni nel pannello di accesso | Microsoft Docs
description: Risolvere i problemi relativi alla visualizzazione di un'applicazione nel panello di accesso
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
ms.reviewr: japere
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f8ccf2cf66b49940bc7f2b9f4764020efc04838e
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="how-applications-appear-on-the-access-panel"></a>Come vengono visualizzate le applicazioni nel pannello di accesso

Il pannello di accesso è un portale basato sul Web e consente a un utente che ha un account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD) di visualizzare e avviare applicazioni basate su cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Queste applicazioni sono configurate per conto dell'utente nel portale di Azure AD. L'amministratore può effettuare il provisioning dell'applicazione direttamente per un utente o un gruppo di cui fa parte l'utente. Come risultato di questa operazione, l'applicazione viene visualizzata nel pannello di accesso dell'utente.

## <a name="general-issues-to-check-first"></a>Problemi generali da verificare prima

-   Se un'applicazione è stata appena rimossa da un utente o da un gruppo di cui l'utente è membro, provare ad accedere e a disconnettersi di nuovo dal pannello di accesso dell'utente dopo alcuni minuti per vedere se l'applicazione è stata rimossa.

-   Se è stata appena rimossa una licenza da un utente o da un gruppo di cui l'utente è membro, l'aggiornamento della visualizzazione potrebbe richiedere molto tempo a seconda delle dimensioni e della complessità del gruppo. Attendere un tempo più lungo prima di accedere al pannello di accesso.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemi relativi all'assegnazione di applicazioni agli utenti

Un utente potrebbe vedere un'applicazione nel proprio pannello di accesso perché è stato assegnato precedentemente all'applicazione. Di seguito sono elencate alcuni modi per verificare questa situazione:

-   [Controllare se un utente è assegnato all'applicazione](#check-if-a-user-is-assigned-to-the-application)

-   [Controllare se un utente è incluso nella licenza relativa all'applicazione](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Controllare se un utente è assegnato all'applicazione

Per controllare se un utente è assegnato all'applicazione, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

6.  **Cercare** il nome dell'applicazione in questione.

7.  Fare clic su **Utenti e gruppi**.

8.  Controllare se un utente è assegnato all'applicazione.

  * Se si vuole rimuovere l'utente dall'applicazione, **fare clic sulla riga** dell'utente e selezionare **Elimina**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Controllare se un utente è incluso nella licenza relativa all'applicazione

Per controllare le licenze assegnate a un utente, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Cercare** l'utente desiderato e **fare clic sulla riga corrispondente** per selezionarlo.

7.  Fare clic su **Licenze** per verificare quali licenze sono assegnate all'utente.

   * Se l'utente è assegnato a una licenza Office, ciò abilita le applicazioni Office di prima entità ad essere visualizzate nel pannello di accesso dell'utente.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemi relativi all'assegnazione di applicazioni a gruppi

Un utente può visualizzare un'applicazione nel pannello di accesso perché è membro di un gruppo assegnato all'applicazione. Alcune procedure per verificare questa situazione sono elencate di seguito:

-   [Controllare le appartenenze a gruppi dell'utente ](#check-a-users-group-memberships)

-   [Controllare se un utente è membro di un gruppo assegnato a una licenza](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Controllare le appartenenze a gruppi dell'utente

Per controllare l'appartenenza a un gruppo, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Ricercare** l'utente per cui si vuole verificare la licenza e **fare clic sulla relativa riga** per selezionarlo.

7.  Fare clic su **gruppi.**

8.  Controllare se un utente è membro di un gruppo assegnato all'applicazione.

   * Se si vuole rimuovere l'utente dal gruppo, **fare clic sulla riga** del gruppo e selezionare Elimina.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Controllare se un utente è membro di un gruppo assegnato a una licenza

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Utenti e gruppi** nel menu di navigazione.

5.  Fare clic su **Tutti gli utenti**.

6.  **Ricercare** l'utente per cui si vuole verificare la licenza e **fare clic sulla relativa riga** per selezionarlo.

7.  Fare clic su **gruppi.**

8.  Fare clic sulla riga di un gruppo specifico.

9.  Fare clic su **Licenze** per vedere le licenze assegnate al gruppo.

  * Se il gruppo è assegnato a una licenza Office, ciò potrebbe abilitare alcune applicazioni Office di prima entità ad essere visualizzate nel pannello di accesso dell'utente.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se questi passaggi di risoluzione dei problemi non risolvono il problema

aprire un ticket di supporto con le informazioni seguenti, se disponibili:

-   ID errore di correlazione

-   UPN (indirizzo di posta elettronica dell'utente)

-   ID tenant

-   Tipo di browser

-   Fuso orario e ora o intervallo di tempo durante il quale si verifica l'errore

-   Tracce Fiddler

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)

