---
title: 'Esercitazione: Integrazione di Azure Active Directory con Litmos | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a9fb661e0b43e71cf9543e9b7042155763d1aa1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57856176"
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Esercitazione: Integrazione di Azure Active Directory con Litmos

Questa esercitazione descrive come integrare Litmos con Azure Active Directory (Azure AD).
L'integrazione di Litmos con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Litmos.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Litmos con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Litmos, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Litmos abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Litmos supporta l'accesso SSO avviato da **IDP**

* Litmos supporta il provisioning utenti **Just-In-Time** (JIT)

## <a name="adding-litmos-from-the-gallery"></a>Aggiunta di Litmos dalla raccolta

Per configurare l'integrazione di Litmos in Azure AD, è necessario aggiungere Litmos dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Litmos dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Litmos**, selezionare **Litmos** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Litmos nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Litmos usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Litmos.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Litmos, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Litmos](#configure-litmos-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Litmos](#create-litmos-test-user)**: per avere una controparte di Britta Simon in Litmos collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Litmos, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Litmos** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Informazioni sul Single Sign-On di URL e dominio di Litmos](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<companyname>.litmos.com/account/Login`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'effettivo URL di risposta e identificatore, descritti più avanti nell'esercitazione, o contattare [il team di supporto di Litmos](https://www.litmos.com/contact-us) per ottenere questi valori. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione Litmos prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | NOME |  Source Attribute |
    |---------------|--------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Configura Litmos** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-litmos-single-sign-on"></a>Configurare l'accesso Single Sign-On di Litmos

1. In un'altra finestra del browser Web accedere al sito aziendale di Litmos come amministratore.

2. Nella barra di spostamento sul lato sinistro fare clic su **Accounts**(Account).

    ![Sezione relativa agli account sul lato dell'app][22]

3. Fare clic sulla scheda **Integrations** (Integrazioni).

    ![Scheda Integrazione][23]

4. Nella scheda **Integrations** (Integrazioni) scorrere verso il basso fino a **3rd Party Integrations** (Integrazioni di terze parti) e quindi fare clic sulla scheda **SAML 2.0** (SAML 2.0).

    ![Sezione SAML 2.0][24]

5. Copiare il valore indicato per l'**endpoint SAML per Litmos** e incollarlo nella casella di testo **URL di risposta** della sezione **URL e dominio Litmos** nel portale di Azure.

    ![Endpoint SAML][26]

6. Nell'applicazione **Litmos** seguire questa procedura:

    ![Applicazione Litmos][25]

    a. Fare clic su **Enable SAML**.

    b. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **SAML X.509 Certificate** .

    c. Fare clic su **Salva modifiche**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Litmos.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Litmos**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Litmos**.

    ![Il collegamento Litmos nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-litmos-test-user"></a>Creare un utente di test di Litmos

Questa sezione descrive come creare un utente chiamato Britta Simon in Litmos. L'applicazione Litmos supporta il provisioning JIT (just-in-time). Questo significa che, se necessario, un account utente viene creato automaticamente durante il tentativo di accedere all'applicazione tramite il pannello di accesso.

**Per creare un utente denominato Britta Simon in Litmos, seguire questa procedura:**

1. In un'altra finestra del browser Web accedere al sito aziendale di Litmos come amministratore.

2. Nella barra di spostamento sul lato sinistro fare clic su **Accounts**(Account).

    ![Sezione relativa agli account sul lato dell'app][22]

3. Fare clic sulla scheda **Integrations** (Integrazioni).

    ![Scheda Integrazioni][23]

4. Nella scheda **Integrations** (Integrazioni) scorrere verso il basso fino a **3rd Party Integrations** (Integrazioni di terze parti) e quindi fare clic sulla scheda **SAML 2.0** (SAML 2.0).

    ![SAML 2.0][24]

5. Selezionare **Autogenerate Users**(Genera utenti in automatico)
  
    ![Autogenerate Users (Genera utenti in automatico)][27]

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Litmos nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Litmos per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png
