---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cisco Webex | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 503de723894388a198abbb687221cb1403a6fa84
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104437"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cisco Webex

Questa esercitazione descrive come integrare Cisco Webex con Azure Active Directory (Azure AD). Integrando Cisco Webex con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Cisco Webex.
* Abilitare gli utenti per l'accesso automatico a Cisco Webex con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Cisco Webex abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Cisco Webex supporta l'accesso SSO avviato da **SP** e il provisioning utenti **Automatico**.

## <a name="adding-cisco-webex-from-the-gallery"></a>Aggiunta di Cisco Webex dalla raccolta

Per configurare l'integrazione di Cisco Webex in Azure AD, è necessario aggiungere Cisco Webex dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Cisco Webex** nella casella di ricerca.
1. Selezionare **Cisco Webex** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Cisco Webex

Configurare e testare l'accesso SSO di Azure AD con Cisco Webex usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cisco Webex.

Per configurare e testare l'accesso SSO di Azure AD con Cisco Webex, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
2. **[Configurare Cisco Webex](#configure-cisco-webex)** per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
    1. **[Creare un utente di test di Cisco Webex](#create-cisco-webex-test-user)** per avere una controparte di B. Simon in Cisco Webex collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Cisco Webex** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** caricare il **file di metadati del provider di servizi** e configurare l'applicazione seguendo questa procedura:

    >[!Note]
    >Si otterrà il file di metadati del provider di servizi in base alla procedura descritta più avanti nella sezione **Configurare Cisco Webex** dell'esercitazione. 

    a. Fare clic su **Carica il file di metadati**.

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    c. Al termine del caricamento del file di metadati del provider di servizi, i valori dell'**Identificatore** e dell'**URL di risposta** vengono popolati automaticamente nella sezione **Configurazione SAML di base**:

    Nella casella di testo **URL di accesso** incollare il valore **URL di risposta**, che viene compilato automaticamente dal caricamento del file di metadati del provider di servizi.

5. L'applicazione Cisco Webex prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

6. Oltre quelli elencati in precedenza, l'applicazione Cisco Webex prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:
    
    | Nome |  Attributo di origine|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **OK**.

    g. Fare clic su **Salva**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Cisco Webex** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Webex.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Cisco Webex**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B. Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cisco-webex"></a>Configurare Cisco Webex

1. Accedere a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) con le credenziali di amministratore complete.

2. Selezionare **Settings** (Impostazioni) e quindi nella sezione **Authentication** (Autenticazione) fare clic su **Modify** (Modifica).

    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
3. Selezionare **Integrate a 3rd-party identity provider. (Advanced)** (Integra provider di terze parti - Avanzate) e passare alla schermata successiva.

4. Nella pagina **Import Idp Metadata** (Importa metadati Idp) trascinare il file di metadati di Azure AD nella pagina o usare l'opzione di esplorazione dei file per individuare e caricare il file di metadati di Azure AD. Selezionare quindi **Require certificate signed by a certificate authority in Metadata (more secure)** (Richiedi certificato firmato da un'autorità di certificazione in metadati - più sicura) e fare clic su **Next** (Avanti).

    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

5. Selezionare **Test SSO Connection** (Test connessione SSO) e, quando viene aperta una nuova scheda del browser, eseguire l'autenticazione con Azure AD effettuando l'accesso.

6. Tornare alla scheda **Cisco Cloud Collaboration Management** del browser. Se il test ha avuto esito positivo, selezionare l'opzione **This test was successful. Enable Single Sign-On**  (Test riuscito. Abilita Single Sign-On), quindi fare clic su **Next** (Avanti).

### <a name="create-cisco-webex-test-user"></a>Creare un utente di test per Cisco Webex

In questa sezione viene creato un utente di nome B.Simon in Cisco Webex. In questa sezione viene creato un utente di nome B.Simon in Cisco Webex.

1. Accedere a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) con le credenziali di amministratore complete.

2. Fare clic su **Users** (Utenti) e quindi su **Manage Users** (Gestisci utenti).
   
    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. Nella finestra **Manage User** (Gestisci utenti) selezionare **Manually add or modify users** (Aggiungi o modifica manualmente gli utenti) e fare clic su **Next** (Avanti).

4. Selezionare **Names and Email address** (Nomi e indirizzi di posta elettronica). Compilare quindi la casella di testo come indicato di seguito:

    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Nella casella di testo **First Name** (Nome) digitare il nome dell'utente, ad esempio **B**.

    b. Nella casella di testo **Last name** (Cognome) digitare il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio b.simon@contoso.com.

5. Fare clic sul segno più per aggiungere B.Simon. Quindi fare clic su **Next**.

6. Nella finestra **Add Services for Users** (Aggiungi servizi per utenti) fare clic su **Save** (Salva) e quindi su **Finish** (Fine).

## <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Cisco Webex nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cisco Webex per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Cisco Webex con Azure AD](https://aad.portal.azure.com)