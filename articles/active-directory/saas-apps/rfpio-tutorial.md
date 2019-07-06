---
title: 'Esercitazione: Integrazione di Azure Active Directory con RFPIO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d7b6af8ff76c890b98c29ded0e8bdc637b45dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092856"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Esercitazione: Integrazione di Azure Active Directory con RFPIO

Questa esercitazione descrive come integrare RFPIO con Azure Active Directory (Azure AD).
L'integrazione di RFPIO con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a RFPIO.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a RFPIO con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con RFPIO, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione RFPIO abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* RFPIO supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-rfpio-from-the-gallery"></a>Aggiunta di RFPIO dalla raccolta

Per configurare l'integrazione di RFPIO in Azure AD, è necessario aggiungere RFPIO dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere RFPIO dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **RFPIO**, selezionare **RFPIO** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![RFPIO nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con RFPIO usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RFPIO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con RFPIO, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di RFPIO](#configure-rfpio-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di RFPIO](#create-rfpio-test-user)** : per avere una controparte di Britta Simon in RFPIO collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con RFPIO, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **RFPIO** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di RFPIO](common/idp-identifier.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://www.rfpio.com`

    b. Fare clic su **Impostare URL aggiuntivi**.

    c. Nella casella di testo **Stato dell'inoltro** immettere un valore stringa. Per ottenere tale valore, contattare il [team di supporto di RFPIO](https://www.rfpio.com/contact/).

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di RFPIO](common/idp-preintegrated-relay.png)

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![image](common/both-preintegrated-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://www.app.rfpio.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di RFPIO](https://www.rfpio.com/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura RFPIO** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-rfpio-single-sign-on"></a>Configurare l'accesso Single Sign-On di RFPIO

1. In un'altra finestra del Web browser accedere al sito Web **RFPIO** come amministratore.

1. Fare clic sull'elenco a discesa a sinistra nella parte inferiore.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app1.png)

1. Fare clic su di **Impostazioni organizzazione**. 

    ![Configure Single Sign-On](./media/rfpio-tutorial/app2.png)

1. Fare clic sulla sezione relativa a **FUNZIONALITÀ E INTEGRAZIONE**.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app4.png)

1. In **SAML Configurazione SSO** fare clic su **Modifica**.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app3.png)

1. In questa sezione eseguire le seguenti operazioni:

    ![Configure Single Sign-On](./media/rfpio-tutorial/app5.png)
    
    a. Copiare il contenuto del **file XML di metadati scaricato** e incollarlo nella casella della **configurazione identità**.

    > [!NOTE]
    >Per copiare il contenuto del file **XML metadati federazione** scaricato usare **Notepad++** o un **editor XML** appropriato.

    b. Fare clic su **Convalida**.

    c. Dopo aver fatto clic su **Convalida**, impostare **SAML (Abilitato)** su attivato.

    d. Fare clic su **Submit**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RFPIO.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **RFPIO**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **RFPIO**.

    ![Collegamento di RFPIO nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-rfpio-test-user"></a>Creare un utente di test di RFPIO

1. Accedere al sito aziendale di RFPIO come amministratore.

1. Fare clic sull'elenco a discesa a sinistra nella parte inferiore.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app1.png)

1. Fare clic su di **Impostazioni organizzazione**. 

    ![Configure Single Sign-On](./media/rfpio-tutorial/app2.png)

1. Fare clic su **MEMBRI DEL TEAM**.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app6.png)

1. Fare clic su **AGGIUNGI MEMBRI**.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app7.png)

1. Nella sezione **Aggiungere nuovi membri**. eseguire le seguenti operazioni:

    ![Configure Single Sign-On](./media/rfpio-tutorial/app8.png)

    a. Immettere l'**indirizzo di posta elettronica** nella casella che richiede **un indirizzo di posta elettronica per riga**.

    b. Selezionare il **ruolo** in base alle esigenze.

    c. Fare clic su **AGGIUNGI MEMBRI**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di RFPIO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RFPIO per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

