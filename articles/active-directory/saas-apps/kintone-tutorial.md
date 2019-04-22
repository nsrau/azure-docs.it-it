---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kintone | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: a66d1cb16f47ebe7aeba3685b3567620ae4a8a51
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564850"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Esercitazione: Integrazione di Azure Active Directory con Kintone

Questa esercitazione descrive come integrare Kintone con Azure Active Directory (Azure AD).
L'integrazione di Kintone con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Kintone.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Kintone con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Kintone sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Kintone abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Kintone supporta l'accesso SSO avviato da **SP**

## <a name="adding-kintone-from-the-gallery"></a>Aggiunta di Kintone dalla raccolta

Per configurare l'integrazione di Kintone in Azure AD è necessario aggiungere Kintone dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Kintone dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Kintone**, selezionare **Kintone** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Kintone nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Kintone usando un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kintone.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Kintone è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Kintone](#configure-kintone-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Kintone](#create-kintone-test-user)**: per avere una controparte di Britta Simon in Kintone collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Kintone, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **Kintone**, selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Kintone](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://<companyname>.kintone.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente:
    
    | |
    |--|
    | `https://<companyname>.cybozu.com` |
    | `https://<companyname>.kintone.com` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Kintone](https://www.kintone.com/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Kintone** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-kintone-single-sign-on"></a>Configurare l'accesso Single Sign-On di Kintone

1. In un'altra finestra del Web browser accedere al sito aziendale di **Kintone** come amministratore.

1. Fare clic sull'**icona Impostazioni**.

    ![Impostazioni](./media/kintone-tutorial/ic785879.png "Impostazioni")

1. Fare clic su **Amministrazione utenti e di sistema**.

    ![Amministrazione utenti e di sistema](./media/kintone-tutorial/ic785880.png "Amministrazione utenti e di sistema")

1. In **Amministrazione di sistema \> Sicurezza** fare clic su **Accesso**.

    ![Account di accesso](./media/kintone-tutorial/ic785881.png "Account di accesso")

1. Fare clic su **Abilita autenticazione SAML**.

    ![Autenticazione SAML](./media/kintone-tutorial/ic785882.png "Autenticazione SAML")

1. Nella sezione SAML Authentication seguire questa procedura:

    ![Autenticazione SAML](./media/kintone-tutorial/ic785883.png "Autenticazione SAML")

    a. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **URL disconnessione** incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    c. Fare clic su **Sfoglia** per caricare il certificato scaricato dal portale di Azure.

    d. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`.  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Kintone.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Kintone**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Kintone**.

    ![Collegamento a Kintone nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-kintone-test-user"></a>Creare un utente di test di Kintone

Per consentire agli utenti di Azure AD di accedere a Kintone, è necessario eseguirne il provisioning in Kintone. Nel caso di Kintone, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito aziendale di **Kintone** come amministratore.

1. Fare clic sull'**icona Impostazioni**.

    ![Impostazioni](./media/kintone-tutorial/ic785879.png "Impostazioni")

1. Fare clic su **Amministrazione utenti e di sistema**.

    ![Amministrazione utenti e di sistema](./media/kintone-tutorial/ic785880.png "Amministrazione utenti e di sistema")

1. In **Amministrazione Utente** fare clic su **Reparti e Utenti**.

    ![Reparto e utenti](./media/kintone-tutorial/ic785888.png "Reparto e utenti")

1. Fare clic su **Nuovo utente**.

    ![Nuovi utenti](./media/kintone-tutorial/ic785889.png "Nuovi utenti")

1. Nella sezione **Nuovo utente** seguire questa procedura:

    ![Nuovi utenti](./media/kintone-tutorial/ic785890.png "Nuovi utenti")

    a. Nelle caselle di testo **Nome visualizzato**, **Nome di accesso**, **Nuova password**, **Conferma password** e **Indirizzo di posta elettronica** digitare i valori richiesti e gli altri dettagli di un account Azure AD valido di cui si vuole eseguire il provisioning.

    b. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Kintone per eseguire il provisioning degli account utente di AAD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Kintone nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Kintone per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
