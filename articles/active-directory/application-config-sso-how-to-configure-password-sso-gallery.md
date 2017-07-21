---
title: Come configurare un accesso Single Sign-On basato su password per un'applicazione della raccolta di Azure AD | Microsoft Docs
description: "Come configurare un'applicazione con un accesso Single Sign-On sicuro basato su password quando è già elencata nella raccolta delle applicazioni di Azure AD"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: d4678afa4d7a9000761d3a60a54539fc1730f76a
ms.contentlocale: it-it
ms.lasthandoff: 04/11/2017

---

# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Come configurare un accesso Single Sign-On basato su password per un'applicazione della raccolta di Azure AD

Quando si aggiunge un'applicazione della [raccolta di applicazioni di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery), è possibile scegliere come gli utenti accedono a tale applicazione. È possibile configurare questa opzione in qualsiasi momento selezionando l'elemento di navigazione **Single Sign-On** in un'applicazione aziendale nel [Portale di Azure](https://portal.azure.com/).

Uno dei metodi di accesso disponibili è l'opzione [Accesso Single Sign-On basato su password](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work). Questo metodo è efficace per iniziare rapidamente l'integrazione di applicazioni in Azure AD e consente di:

-   Abilitare l'**accesso Single Sign-On per gli utenti** archiviando e riproducendo in modo sicuro i nomi utente e password per l'applicazione integrata con Azure AD

-   **Supportare applicazioni che richiedono più campi di accesso** per applicazioni che richiedono non solo i campi di nome utente e password per accedere

-   **Personalizzare le etichette** dei campi di input di nome utente e password visualizzati nel [pannello di accesso all'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando si immettono le credenziali

-   Consentire agli **utenti** di immettere i nomi utente e le password per tutti gli account di applicazioni esistenti digitati manualmente nel [pannello di accesso all'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Consentire a un **membro del gruppo aziendale** di specificare i nome utente e le password assegnati a un utente tramite la funzionalità di [accesso all'applicazione self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Consentire a un **amministratore** di specificare i nomi utente e le password assegnati a un utente tramite la funzionalità di credenziali di aggiornamento quando [viene assegnato un utente a un'applicazione](#assign-a-user-to-an-application-directly)

-   Consentire un **amministratore** di specificare i nomi utente e le password condivisi usati da un gruppo di utenti tramite la funzionalità di credenziali di aggiornamento quando [viene assegnato un gruppo a un'applicazione](#assign-an-application-to-a-group-directly)

Di seguito viene illustrato come abilitare l'[accesso Single Sign-On basato su password](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) per un'applicazione che si trova già nella [raccolta di applicazioni di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

## <a name="overview-of-steps-required"></a>Panoramica dei passaggi necessari
Per configurare un'applicazione della raccolta di Azure AD è necessario:

-   [Aggiungere un'applicazione dalla raccolta di Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurare l'applicazione per un accesso Single Sign-On basato su password](#configure-the-application-for-password-single-sign-on)

-   [Assegnare l'applicazione a un utente o un gruppo](#assign-the-application-to-a-user-or-a-group)

    -   [Assegnare un utente direttamente a un'applicazione](#assign-a-user-to-an-application-directly)

    -   [Assegnare un'applicazione direttamente a un gruppo ](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Aggiungere un'applicazione dalla raccolta di Azure AD

Per aggiungere un'applicazione dalla raccolta di Azure AD, seguire i passaggi seguenti:

1.  Aprire il [Portale di Azure](https://portal.azure.com) e accedere come **amministratore globale** o **coamministratore**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic sul pulsante **Aggiungi** nell'angolo superiore destro del pannello **Applicazioni aziendali**

6.  Nella casella di testo **Immettere un nome** della sezione **Aggiungi dalla raccolta** digitare il nome dell'applicazione

7.  Selezionare l'applicazione che si vuole configurare per un accesso Single Sign-On

8.  Prima di aggiungere l'applicazione, è possibile modificarne il nome dalla casella di testo **Nome**.

9.  Fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

Dopo un breve periodo di tempo, sarà possibile visualizzare il pannello di configurazione dell'applicazione.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurare l'applicazione per un accesso Single Sign-On basato su password

Per configurare un accesso Single Sign-On per un'applicazione, seguire i passaggi seguenti:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **coamministratore.**

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare un accesso Single Sign-On

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

8.  Selezionare la modalità **Accesso basato su password**.

9.  [Assegnare gli utenti all'applicazione](#assign-a-user-to-an-application-directly).

10. È anche possibile specificare le credenziali per conto dell'utente selezionando le righe degli utenti e facendo clic su **Aggiorna credenziali** e immettere il nome utente e la password per conto degli utenti. In caso contrario, verrà richiesto agli utenti di immettere le credenziali all'avvio.

## <a name="assign-a-user-to-an-application-directly"></a>Assegnare un utente direttamente a un'applicazione

Per assegnare uno o più utenti direttamente a un'applicazione, seguire la procedura seguente:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7.  Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di navigazione a sinistra dell'applicazione.

8.  Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il pannello **Aggiungi assegnazione**.

9.  Fare clic sul selettore **Utenti e gruppi** nel pannello **Aggiungi assegnazione**.

10. Digitare il **nome completo**  o l'**indirizzo di posta elettronica** dell'utente a cui si vuole eseguire l'assegnazione nella casella di ricerca **Cerca per nome o indirizzo di posta**.

11. Passare il puntatore sull'**utente** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un utente**, digitare un altro **nome completo** o un **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **selezionato**.

13. Al termine della selezione degli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel pannello **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione agli utenti selezionati.

## <a name="assign-an-application-to-a-group-directly"></a>Assegnare un'applicazione direttamente a un gruppo

Per assegnare uno o più gruppi direttamente a un'applicazione, seguire la procedura seguente:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7.  Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di navigazione a sinistra dell'applicazione.

8.  Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il pannello **Aggiungi assegnazione**.

9.  Fare clic sul selettore **Utenti e gruppi** nel pannello **Aggiungi assegnazione**.

10. Digitare il **nome completo del gruppo** a cui si vuole eseguire l'assegnazione nella casella di ricerca **Cerca per nome o indirizzo di posta**.

11. Passare il puntatore sul **gruppo** nell'elenco per visualizzare un **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo del gruppo per aggiungere l'utente all'elenco **selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un gruppo**, digitare un altro **nome completo di gruppo** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere il gruppo all'elenco **selezionato**.

13. Al termine della selezione dei gruppi, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel pannello **Aggiungi assegnazione** per scegliere un ruolo da assegnare ai gruppi selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione ai gruppi selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati saranno in grado di avviare queste applicazioni nel riquadro di accesso.

## <a name="next-steps"></a>Passaggi successivi
[Specificare un accesso Single Sign-On per le app con un proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)

