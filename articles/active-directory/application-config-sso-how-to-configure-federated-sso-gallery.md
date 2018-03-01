---
title: Come configurare l'accesso Single Sign-On federato per un'applicazione della raccolta di Azure AD | Microsoft Docs
description: Informazioni su come configurare l'accesso Single Sign-On federato per un'applicazione nella raccolta di Azure AD e usare le esercitazioni per eseguire l'operazione rapidamente
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
ms.openlocfilehash: e71480910432c6475ca97d20853a72b864195faa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Come configurare l'accesso Single Sign-On federato per un'applicazione della raccolta di Azure AD

Per tutte le applicazioni nella raccolta di Azure AD con funzionalità Enterprise Single Sign-On abilitata è disponibile un'esercitazione dettagliata. Per le istruzioni dettagliate, è possibile accedere all'[Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).

## <a name="overview-of-steps-required"></a>Panoramica dei passaggi necessari
Per configurare un'applicazione della raccolta di Azure AD è necessario:

-   [Aggiungere un'applicazione dalla raccolta di Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurare i valori dei metadati dell'applicazione in Azure AD (URL di accesso, identificatore, URL di risposta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperare il certificato e i metadati di Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurare i valori dei metadati di Azure AD nell'applicazione (URL di accesso, autorità emittente, URL di disconnessione e certificato)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Assegnare utenti all'applicazione](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Aggiungere un'applicazione dalla raccolta di Azure AD

Per aggiungere un'applicazione dalla raccolta di Azure AD, seguire questa procedura:

1.  Aprire il [portale di Azure](https://portal.azure.com) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic sul pulsante **Aggiungi** nell'angolo in alto a destra del riquadro **Applicazioni aziendali**.

6.  Nella casella di testo **Immettere un nome** della sezione **Aggiungi dalla raccolta** digitare il nome dell'applicazione.

7.  Selezionare l'applicazione che si vuole configurare con l'accesso Single Sign-On.

8.  Prima di aggiungere l'applicazione, è possibile modificarne il nome usando la casella di testo **Nome**.

9.  Fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

Dopo un breve periodo di tempo, sarà possibile visualizzare il riquadro di configurazione dell'applicazione.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurare l'accesso Single Sign-On per un'applicazione della raccolta di Azure AD

Per configurare un accesso Single Sign-On per un'applicazione, seguire i passaggi seguenti:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8.  Selezionare **Accesso basato su SAML** dal menu a discesa **Modalità**.

9.  Immettere i valori necessari in **URL e dominio**. È necessario ottenere questi valori dal fornitore dell'applicazione.

   1. Per configurare l'applicazione come SSO avviato da provider di servizi, l'URL di accesso è un valore obbligatorio. Per alcune applicazioni, anche l'identificatore è un valore obbligatorio.

   2. Per configurare l'applicazione come SSO avviato da IdP, l'URL di risposta è un valore obbligatorio. Per alcune applicazioni, anche l'identificatore è un valore obbligatorio.

10. **Facoltativo:** fare clic su **Mostra impostazioni URL avanzate** se si vuole vedere i valori non obbligatori.

11. In **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**.

12. **Facoltativo:** fare clic su **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi che dovranno essere inviati all'applicazione nel token SAML all'accesso degli utenti.

  Per aggiungere un attributo:
   
   1. Fare clic su **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

   1. Fare clic su **Salva**. Il nuovo attributo verrà visualizzato nella tabella.

13. Fare clic su **Configura &lt;nome applicazione&gt;** per accedere alla documentazione che illustra come configurare l'accesso Single Sign-On nell'applicazione. Sono inoltre disponibili il certificato e gli URL dei metadati necessari per configurare l'accesso SSO con l'applicazione.

14. Fare clic su **Salva** per salvare la configurazione.

15. Assegnare gli utenti all'applicazione.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione

Per selezionare l'identificatore utente o aggiungere gli attributi dell'utente, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8.  Nella sezione **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**. L'opzione selezionata deve corrispondere al valore previsto nell'applicazione per autenticare l'utente.

  >[!NOTE] 
  >Azure AD seleziona il formato per l'attributo NameID (identificatore utente) in base al valore selezionato o al formato richiesto dall'applicazione nell'oggetto AuthRequest SAML. Per altre informazioni, vedere l'articolo relativo al [protocollo SAML per Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sotto la sezione NameIDPolicy.
  >
  >

9.  Per aggiungere gli attributi utente, fare clic su **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi che dovranno essere inviati all'applicazione nel token SAML all'accesso degli utenti.

   Per aggiungere un attributo:
  
   1. Fare clic su **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

   2. Fare clic su **Save**. Il nuovo attributo verrà visualizzato nella tabella.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Scaricare il certificato o i metadati di Azure AD

Per scaricare il certificato o i metadati dell'applicazione da Azure AD, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  *  Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8.  Passare alla sezione **Certificato di firma SAML** e quindi fare clic sul valore della colonna **Download**. A seconda di quale applicazione richiede la configurazione dell'accesso Single Sign-On, è visibile l'opzione per scaricare il codice XML dei metadati o l'opzione per scaricare il certificato.

Azure AD non fornisce URL per ottenere i metadati. I metadati possono essere recuperati solo come file XML.

## <a name="assign-users-to-the-application"></a>Assegnare utenti all'applicazione

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7.  Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di spostamento di sinistra dell'applicazione.

8.  Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione**.

9.  Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

10. Nella casella di ricerca **Cerca per nome o indirizzo di posta** digitare il **nome completo**  o l'**indirizzo di posta elettronica** dell'utente oggetto dell'assegnazione.

11. Passare il puntatore sull'**utente** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **selezionato**.

13. Dopo avere selezionato gli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione agli utenti selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati potranno avviare queste applicazioni usando i metodi illustrati nella sezione Descrizione della soluzione.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personalizzazione delle attestazioni SAML inviate a un'applicazione

Per informazioni su come personalizzare le attestazioni degli attributi SAML inviate all'applicazione, vedere [Claims mapping in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) (Mapping di attestazioni in Azure Active Directory).

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)



