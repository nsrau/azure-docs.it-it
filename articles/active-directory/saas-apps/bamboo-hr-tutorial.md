---
title: 'Esercitazione: Integrazione di Azure Active Directory con BambooHR | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BambooHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: df61a1b9efafab327b243c3874ac23572350a20c
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808367"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Esercitazione: Integrazione di Azure Active Directory con BambooHR

Questa esercitazione descrive come integrare BambooHR con Azure Active Directory (Azure AD).
L'integrazione di BambooHR con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a BambooHR.
* È possibile abilitare gli utenti per l'accesso automatico a BambooHR (Single Sign-On) con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con BambooHR, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di BambooHR abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* BambooHR supporta l'accesso Single Sign-On avviato da **provider di servizi**

## <a name="adding-bamboohr-from-the-gallery"></a>Aggiunta di BambooHR dalla raccolta

Per configurare l'integrazione di BambooHR in Azure AD, è necessario aggiungere BambooHR dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere BambooHR dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **BambooHR**, selezionare **BambooHR** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![BambooHR nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con BambooHR usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BambooHR.

Per configurare e testare l'accesso Single Sign-On di Azure AD con BambooHR, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di BambooHR](#configure-bamboohr-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di BambooHR](#create-bamboohr-test-user)**: per avere una controparte di Britta Simon in BambooHR collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con BambooHR, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **BambooHR** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di BambooHR](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://<company>.bamboohr.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `BambooHR-SAML`

    > [!NOTE]
    > Poiché il valore **URL accesso** non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di BambooHR](https://www.bamboohr.com/contact.php). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Set up BambooHR** (Configura BambooHR) copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-bamboohr-single-sign-on"></a>Configurare l'accesso Single Sign-On di BambooHR

1. In una nuova finestra accedere al sito aziendale di BambooHR come amministratore.

2. Nella home page seguire questa procedura:
   
    ![Pagina Single Sign-On di BambooHR](./media/bamboo-hr-tutorial/ic796691.png "Single Sign-On")   

    a. Selezionare **Apps** (App).
   
    b. Nel riquadro **Apps** (App) selezionare **Single Sign-On**.
   
    c. Selezionare **SAML Single Sign-On** (Single Sign-On SAML).

3. Nel riquadro **SAML Single Sign-On** (Single Sign-On SAML) seguire questa procedura:
   
    ![Riquadro SAML Single Sign-On (Single Sign-On SAML)](./media/bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On (Single Sign-On SAML)")
   
    a. Nella casella **SSO Login URL** (URL di accesso SSO) incollare l'**URL di accesso** copiato dal portale di Azure nel passaggio 6.
      
    b. Nel Blocco note aprire il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella **X.509 Certificate** (Certificato X.509).
   
    c. Selezionare **Salva**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BambooHR.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **BambooHR**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **BambooHR**.

    ![Collegamento di BambooHR nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-bamboohr-test-user"></a>Creare un utente di test di BambooHR

Per consentire agli utenti di Azure AD di accedere a BambooHR, impostarli manualmente in BambooHR seguendo questa procedura:

1. Accedere al sito di **BambooHR** come amministratore.

2. Nella barra degli strumenti superiore selezionare **Impostazioni**.
   
    ![Pulsante Impostazioni](./media/bamboo-hr-tutorial/IC796694.png "Impostazioni")

3. Selezionare **Panoramica**.

4. Nel riquadro di sinistra selezionare **Sicurezza** > **Utenti**.

5. Digitare il nome utente, la password e l'indirizzo di posta elettronica dell'account di Azure AD valido che si vuole configurare.

6. Selezionare **Salva**.
        
>[!NOTE]
>Per configurare account utente di Azure AD è possibile anche usare le API o gli strumenti di creazione di account utente di BambooHR.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro BambooHR nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione BambooHR per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

