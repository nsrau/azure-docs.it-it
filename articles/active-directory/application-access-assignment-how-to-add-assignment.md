---
title: Come assegnare utenti e gruppi a un'applicazione | Microsoft Docs
description: Assegnare utenti per concedere l'accesso all'applicazione
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
ms.openlocfilehash: 972ed736e63089a69726de5d26236b930b390a10
ms.contentlocale: it-it
ms.lasthandoff: 04/11/2017

---

# <a name="how-to-assign-users-and-groups-to-an-application"></a>Come assegnare utenti e gruppi a un'applicazione

Prima che gli utenti effettuino una delle operazioni seguenti per un'applicazione specifica, è necessario innanzitutto **assegnarli all'applicazione** per concedere loro l'accesso:

-   Accedere a un'applicazione **passando direttamente all'URL dell'applicazione** (noto anche come accesso avviato da provider di servizi).

-   Accedere a un'applicazione usando l'**URL di accesso utente** nella pagina **Proprietà** di un'applicazione (noto anche come accesso avviato da provider di identità).

-   Visualizzare un'applicazione nel [Pannello di accesso dell'applicazione](https://myapps.microsoft.com/) o l'applicazione per dispositivi mobili.

-   Visualizzare un'applicazione nell'[icona di avvio delle app di Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

## <a name="methods-to-assign-applications-with-azure-active-directory"></a>Metodi per assegnare applicazioni con Azure Active Directory 

Esistono 3 modi per poter assegnare applicazioni con Azure Active Directory:

-   [Assegnare un utente direttamente a un'applicazione come amministratore](#assign-a-user-directly-as-an-administrator)

-   [Assegnare un gruppo direttamente a un'applicazione come amministratore](#assign-a-group-directly-to-an-application-as-an-administrator)

-   [Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni](#enable-self-service-application-access-to-allow-users-to-find-their-own-applications)

## <a name="assign-a-user-directly-as-an-administrator"></a>Assegnare un utente direttamente come amministratore

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory"** nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione che si desidera assegnare a un utente dall'elenco.

7.  Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di navigazione a sinistra dell'applicazione.

8.  Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il pannello **Aggiungi assegnazione**.

9.  Fare clic sul selettore **Utenti e gruppi** nel pannello **Aggiungi assegnazione**.

10. Digitare il **nome completo**  o l'**indirizzo di posta elettronica** dell'utente per cui si desidera eseguire l'assegnazione nella casella di ricerca **Cerca per nome o indirizzo di posta**.

11. Passare il puntatore sull'**utente** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si desidera **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **Selezionato**.

13. Dopo avere selezionato gli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel pannello **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione agli utenti selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati potranno avviare queste applicazioni usando i metodi illustrati nella sezione Descrizione della soluzione.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Assegnare un gruppo direttamente a un'applicazione come amministratore

Per assegnare uno o più gruppi direttamente a un'applicazione, seguire questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory"** nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione che si desidera assegnare a un utente dall'elenco.

7.  Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di navigazione a sinistra dell'applicazione.

8.  Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il pannello **Aggiungi assegnazione**.

9.  Fare clic sul selettore **Utenti e gruppi** nel pannello **Aggiungi assegnazione**.

10. Digitare il **nome completo del gruppo** per cui si vuole eseguire l'assegnazione nella casella di ricerca **Cerca per nome o indirizzo di posta**.

11. Passare il puntatore sul **gruppo** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo del gruppo per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si desidera **aggiungere più di un gruppo**, digitare un altro **nome completo di gruppo** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere il gruppo all'elenco **Selezionato**.

13. Dopo aver selezionato i gruppi, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel pannello **Aggiungi assegnazione** per scegliere un ruolo da assegnare ai gruppi selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione ai gruppi selezionati.

Dopo un breve periodo di tempo, gli utenti nei gruppi selezionati potranno avviare queste applicazioni usando i metodi illustrati nella sezione Descrizione della soluzione. Se i gruppi sono dinamici, potrebbe verificarsi un ritardo di elaborazione aggiuntivo nella visualizzazione di queste assegnazioni per gli utenti dei gruppi assegnati.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni

L'accesso alle applicazioni self-service è un modo efficace per consentire agli utenti di individuare da soli le applicazioni e, facoltativamente, al gruppo aziendale di approvare l'accesso a tali applicazioni. È possibile consentire al gruppo aziendale di gestire le credenziali assegnate agli utenti per il diritto di accesso alle applicazioni Single Sign-On tramite password dal pannello di accesso.

Per abilitare l'accesso self-service per un'applicazione, seguire questa procedura:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory"** nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si desidera abilitare l'accesso self-service dall'elenco.

7.  Dopo il caricamento dell'applicazione, fare clic su **Self-service** nel menu di navigazione a sinistra dell'applicazione.

8.  Per abilitare l'accesso self-service per questa applicazione, impostare l'opzione **Consentire agli utenti di richiedere l'accesso a questa applicazione?** su **Sì**.

9.  Successivamente, per selezionare il gruppo al quale devono essere aggiunti gli utenti che richiedono l'accesso a questa applicazione, fare clic sul selettore accanto all'etichetta **Gruppo a cui devono essere aggiunti gli utenti assegnati** e selezionare un gruppo.

10. **Facoltativo:** se si desidera richiedere un'approvazione aziendale prima che venga consentito l'accesso agli utenti, impostare l'opzione **Richiedere l'approvazione prima di concedere l'accesso a questa applicazione?** su **Sì**.

11. **Facoltativo: per applicazioni che usano solo l'accesso Single Sign-On tramite password,** se si desidera consentire ai responsabili approvazione aziendali di specificare le password inviate a questa applicazione per gli utenti approvati, impostare l'opzione **Consentire ai responsabili approvazione di impostare le password utente per questa applicazione?** su **Sì**.

12. **Facoltativo:** per specificare i responsabili approvazione aziendali che possono approvare l'accesso a questa applicazione, fare clic sul selettore accanto all'etichetta **Utenti autorizzati ad approvare l'accesso a questa applicazione** per selezionare un massimo di 10 singoli responsabili approvazione aziendali.

  >[!NOTE]
  >I gruppi non sono supportati.
  >
  >

13. **Facoltativo:** **per le applicazioni che espongono i ruoli**, se si desidera assegnare un ruolo agli utenti approvati self-service, fare clic sul selettore accanto all'opzione **A quale ruolo è necessario assegnare gli utenti in questa applicazione?** per selezionare il ruolo a cui devono essere assegnati questi utenti.

14. Per terminare, fare clic sul pulsante **Salva** nella parte superiore del pannello.

Dopo aver completato la configurazione dell'applicazione self-service, gli utenti possono accedere al [Pannello di accesso dell'applicazione](https://myapps.microsoft.com/) e fare clic sul pulsante **+Aggiungi** per trovare le app per cui è stato abilitato l'accesso self-service. Anche i responsabili approvazione aziendali visualizzano una notifica nel loro [Pannello di accesso dell'applicazione](https://myapps.microsoft.com/). È possibile abilitare un messaggio di posta elettronica per informare i responsabili dell'approvazione quando un utente richiede l'accesso a un'applicazione per cui è necessaria l'approvazione. 

Tali approvazioni supportano solo flussi di lavoro di approvazione individuali. Se si specificano pertanto più responsabili approvazione, uno qualsiasi di esso potrà approvare l'accesso all'applicazione.

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)

