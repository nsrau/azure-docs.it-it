---
title: 'Esercitazione: Integrazione di Azure Active Directory con Egnyte | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7201b7d3f9f7e8333b18a087f58b6211101b1304
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233560"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Esercitazione: Integrazione di Azure Active Directory con Egnyte

Questa esercitazione descrive come integrare Egnyte con Azure Active Directory (Azure AD).
L'integrazione di Egnyte con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Egnyte.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Egnyte con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Egnyte sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Egnyte abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Egnyte supporta l'accesso SSO avviato da **SP**

## <a name="adding-egnyte-from-the-gallery"></a>Aggiunta di Egnyte dalla raccolta

Per configurare l'integrazione di Egnyte in Azure AD è necessario aggiungere Egnyte dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Egnyte dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Egnyte**, selezionare **Egnyte** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Egnyte nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Egnyte usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Egnyte.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Egnyte è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Egnyte](#configure-egnyte-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Egnyte](#create-egnyte-test-user)** : per avere una controparte di Britta Simon in Egnyte collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Egnyte, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Egnyte** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Egnyte](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di Egnyte](https://www.egnyte.com/corp/contact_egnyte.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

5. Nella sezione **Configura Egnyte** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-egnyte-single-sign-on"></a>Configurare l'accesso Single Sign-On per Egnyte

1. In un'altra finestra del Web browser accedere al sito aziendale di Egnyte come amministratore.

2. Fare clic su **Impostazioni**.
   
    ![Impostazioni](./media/egnyte-tutorial/ic787819.png "Impostazioni")

3. Scegliere **Settings**dal menu.

    ![Impostazioni](./media/egnyte-tutorial/ic787820.png "Impostazioni")

4. Fare clic sulla scheda **Configuration** (Configurazione) e quindi fare clic su **Security** (Sicurezza).

    ![Sicurezza](./media/egnyte-tutorial/ic787821.png "Security")

5. Nella sezione **Single Sign-On Authentication** seguire questa procedura:

    ![Autenticazione Single Sign-On](./media/egnyte-tutorial/ic787822.png "Single Sign On Authentication")   
    
    a. In **Single sign-on authentication** (Autenticazione Single Sign-On) selezionare **SAML 2.0**.
   
    b. In **Identity provider** (Provider di identità) selezionare **AzureAD**.
   
    c. Incollare il valore di **URL di accesso** copiato dal portale di Azure nella casella di testo **Identity provider login URL** (URL di accesso provider di identità).
   
    d. Incollare l'**identificatore di Azure AD** copiato dal portale di Azure nella casella di testo **Identity provider entity ID** (ID entità del provider di identità).
      
    e. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Identity provider certificate** (Certificato provider di identità).
   
    f. In **Default user mapping** (Mapping utente predefinito) selezionare **Email address** (Indirizzo posta elettronica).
   
    g. In **Use domain-specific issuer value** (Usa valore autorità emittente specifica del dominio) selezionare **disabled** (disabilitato).
   
    h. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Egnyte.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Egnyte**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Egnyte**.

    ![Collegamento di Egnyte nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-egnyte-test-user"></a>Creare l'utente di test di Egnyte

Per consentire agli utenti di Azure AD di accedere a Egnyte, è necessario effettuare il provisioning degli utenti in Egnyte. Nel caso di Egnyte, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Egnyte** come amministratore.

2. Passare a **Settings (Impostazioni) \> Users & Groups (Utenti e gruppi)** .

3. Fare clic su **Add New User**e quindi selezionare il tipo di utente da aggiungere.
   
    ![Utenti](./media/egnyte-tutorial/ic787824.png "Utenti")

4. Nella sezione **New Power User** (Nuovo utente esperto) seguire questa procedura:
    
    ![Nuovo utente standard](./media/egnyte-tutorial/ic787825.png "New Standard User")   

    a. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon\@contoso.com**.

    b. Nella casella di testo **Username** (Nome utente) digitare il nome dell'utente, ad esempio **Brittasimon**.

    c. Selezionare **Single Sign-On** per **Authentication Type** (Tipo di autenticazione).
   
    d. Fare clic su **Save**.
    
    >[!NOTE]
    >Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica di notifica.
    >

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da Egnyte per effettuare il provisioning degli account utente di Azure AD.
>

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Egnyte nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Egnyte per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

