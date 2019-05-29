---
title: 'Esercitazione: Integrazione di Azure Active Directory con OpsGenie | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 98e4d1870c57c9718e2d4293157b21ead8ea44e1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65987480"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Esercitazione: Integrazione di Azure Active Directory con OpsGenie

Questa esercitazione descrive come integrare OpsGenie con Azure Active Directory (Azure AD).
L'integrazione di OpsGenie con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a OpsGenie.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a OpsGenie con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OpsGenie, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di OpsGenie abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* OpsGenie supporta l'accesso SSO avviato da **SP**

## <a name="adding-opsgenie-from-the-gallery"></a>Aggiunta di OpsGenie dalla raccolta

Per configurare l'integrazione di OpsGenie in Azure AD, è necessario aggiungere OpsGenie dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere OpsGenie dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **OpsGenie**, selezionare **OpsGenie** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![OpsGenie nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OpsGenie usando un utente di test di nome **B. Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OpsGenie.

Per configurare e testare l'accesso Single Sign-On di Azure AD con OpsGenie, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per OpsGenie](#configure-opsgenie-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di OpsGenie](#create-opsgenie-test-user)** : per avere una controparte di B. Simon in OpsGenie collegata alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare Single Sign-On di Azure AD con OpsGenie, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **OpsGenie** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di OpsGenie](common/sp-signonurl.png)

    Nella casella di testo **URL di accesso** digitare un URL: `https://app.opsgenie.com/auth/login`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

6. Nella sezione **Configura OpsGenie** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-opsgenie-single-sign-on"></a>Configurare l'accesso Single Sign-On per OpsGenie

1. Aprire un'altra istanza del browser e quindi accedere a OpsGenie come amministratore.

2. Fare clic su **Settings** e quindi selezionare la scheda **Single Sign On**.
   
    ![Accesso Single Sign-On di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Per abilitare l'accesso Single Sign-On, selezionare **Enabled**.
   
    ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. Nella sezione **Provider** fare clic sulla scheda **Azure Active Directory**.
   
    ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Nella pagina Azure Active Directory, seguire questa procedura:
   
    ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Nella casella di testo **SAML 2.0 Endpoint** (Endpoint SAML 2.0) incollare il valore di **URL di accesso** copiato dal portale di Azure.
    
    b. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.
    
    c. Fare clic su **Salva modifiche**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

In questa sezione viene creato un utente di test di nome B. Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **B. Simon**.
  
    b. Nel campo **Nome utente** digitare **bsimon@yourcompanydomain.extension**  
    Ad esempio: BSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione B. Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OpsGenie.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **OpsGenie**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **OpsGenie**.

    ![Collegamento di OpsGenie nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **B. Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-opsgenie-test-user"></a>Creare un utente di test di OpsGenie

In questa sezione viene creato un utente di nome B. Simon in OpsGenie. 

1. In una finestra del Web browser accedere al tenant di OpsGenie come amministratore.

2. Passare all'elenco di utenti facendo clic su **Users** (Utenti) nel pannello a sinistra.
   
    ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Fare clic su **Add User**.

4. Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:
   
    ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica di B. Simon in Azure Active Directory.
   
    b. Nella casella di testo **Full Name** (Nome completo) digitare **B. Simon**.
   
    c. Fare clic su **Save**. 

>[!NOTE]
>B. Simon riceve un messaggio di posta elettronica con istruzioni per la configurazione del profilo.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di OpsGenie nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione OpsGenie per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

