---
title: 'Esercitazione: Integrazione di Azure Active Directory con Dropbox for Business | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/20/2018
ms.author: jeedes
ms.openlocfilehash: a05a246a3bdf0594484cbbf89998e337d2be5a12
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974965"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Esercitazione: Integrazione di Azure Active Directory con Dropbox for Business

Questa esercitazione descrive come integrare Dropbox for Business con Azure Active Directory (Azure AD).
L'integrazione di Dropbox for Business con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD quali utenti possono accedere a Dropbox for Business.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Dropbox for Business con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Dropbox for Business, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Dropbox for Business abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Dropbox for Business supporta l'accesso SSO avviato da **SP**

* Dropbox for Business supporta il provisioning **Just-In-Time** (JIT) degli utenti

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Aggiunta di Dropbox for Business dalla raccolta

Per configurare l'integrazione di Dropbox for Business in Azure AD, è necessario aggiungere Dropbox for Business dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Dropbox for Business dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Dropbox for Business**, selezionare **Dropbox for Business** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Dropbox for Business nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Dropbox for Business usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Dropbox for Business.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Dropbox for Business, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Dropbox for Business](#configure-dropbox-for-business-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Dropbox for Business](#create-dropbox-for-business-test-user)**: per avere una controparte di Britta Simon in Dropbox for Business collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Dropbox for Business, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Dropbox for Business** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On in Dropbox for Business](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://www.dropbox.com/sso/<id>`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un valore: `Dropbox`

    > [!NOTE]
    > Il valore precedente non è un valore reale. È necessario aggiornarlo con l'URL di accesso effettivo, descritto più avanti nell'esercitazione.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Dropbox for Business** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-dropbox-for-business-single-sign-on"></a>Configurare l'accesso Single Sign-On di Dropbox for Business

1. Per configurare l'accesso Single Sign-On sul lato **Dropbox for Business**, passare al tenant di Dropbox for Business e accedere al proprio tenant di Dropbox for business.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/ic769509.png "Configurare l'accesso Single Sign-On")

2. Fare clic su **Icona utente** e selezionare la scheda **Impostazioni**.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure1.png "Configurare l'accesso Single Sign-On")

3. Nel riquadro di spostamento a sinistra fare clic su **Console di amministrazione**.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure2.png "Configurare l'accesso Single Sign-On")

4. In **Console di amministrazione** fare clic su **Impostazioni** nel riquadro di spostamento a sinistra.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure3.png "Configurare l'accesso Single Sign-On")

5. Selezionare l’opzione **Single sign-on** sotto la sezione **Autenticazione**.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure4.png "Configurare l'accesso Single Sign-On")

6. Nella sezione **Single sign-on**seguire questa procedura:  

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure5.png "Configurare l'accesso Single Sign-On")

    a. Selezionare **necessari** come opzione nell'elenco a discesa per il **Single sign-on**.

    b. Fare clic su **Aggiungi URL di accesso** e quindi nella casella di testo **URL di accesso del provider di identità** incollare il valore di **URL di accesso** copiato dal portale di Azure e selezionare **Fine**.

    ![Configurare l'accesso Single Sign-On](./media/dropboxforbusiness-tutorial/configure6.png "Configurare l'accesso Single Sign-On")

    c. Fare clic su **Carica certificato**e quindi andare al **file di certificato oBase64** scaricato dal portale di Azure.

    d. Fare clic su **Copia il link** e incollare il valore copiato nella casella di testo **URL Sign-on** delle sezioni **dominio e URL di Dropbox for Business** nel portale di Azure.

    e. Fare clic su **Save**.

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

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Dropbox for Business.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Dropbox for Business**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Dropbox for Business**.

    ![Collegamento di Dropbox for Business nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-dropbox-for-business-test-user"></a>Creare un utente di test di Dropbox for Business

In questa sezione viene creato un utente di nome Britta Simon in Dropbox for Business. Dropbox for Business supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Dropbox for Business, ne viene creato uno nuovo dopo l'autenticazione.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto clienti di Dropbox for Business](https://www.dropbox.com/business/contact)

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Dropbox for Business nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Dropbox for Business per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

