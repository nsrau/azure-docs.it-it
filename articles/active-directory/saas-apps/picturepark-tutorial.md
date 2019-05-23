---
title: 'Esercitazione: Integrazione di Azure Active Directory con Picturepark | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 617c75024b45dab7ff2466b99bfb71c18cdd778a
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904581"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Esercitazione: Integrazione di Azure Active Directory con Picturepark

Questa esercitazione descrive come integrare Picturepark con Azure Active Directory (Azure AD).
L'integrazione di Picturepark con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Picturepark.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Picturepark con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Picturepark, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Picturepark abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Picturepark supporta l'accesso SSO avviato da **SP**

## <a name="adding-picturepark-from-the-gallery"></a>Aggiunta di Picturepark dalla raccolta

Per configurare l'integrazione di Picturepark in Azure AD, è necessario aggiungere Picturepark dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Picturepark dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Picturepark**, selezionare **Picturepark** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Picturepark nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Picturepark usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Picturepark.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Picturepark, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Picturepark](#configure-picturepark-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Picturepark](#create-picturepark-test-user)**: per avere una controparte di Britta Simon in Picturepark collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Picturepark, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Picturepark** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Picturepark](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.picturepark.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Picturepark](https://picturepark.com/about/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML**  fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML** .

    ![Modificare il certificato di firma SAML](common/edit-certificate.png)

6. Nella sezione **Certificato di firma SAML** copiare il valore **Identificazione personale** e salvarlo nel computer.

    ![Copiare il valore di Identificazione personale](common/copy-thumbprint.png)

7. Nella sezione **Configura Picturepark** copiare gli URL appropriati in base alle esigenze. Per **URL di accesso** usare il valore nel formato seguente: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ è l'ID tenant della sottoscrizione di Azure AD.

    ![Copiare gli URL di configurazione](./media/picturepark-tutorial/configurls.png)

    a. Identificatore di Azure AD

    b. URL di chiusura sessione

### <a name="configure-picturepark-single-sign-on"></a>Configurare l'accesso Single Sign-On di Picturepark

1. In un'altra finestra del Web browser accedere al sito aziendale di Picturepark come amministratore.

2. Nella barra degli strumenti in alto fare clic su **Strumenti di amministrazione**, quindi fare clic su **Console di gestione**.
   
    ![Console di gestione](./media/picturepark-tutorial/ic795062.png "Console di gestione")

3. Fare clic su **Autenticazione**, quindi su **Provider di identità**.
   
    ![Autenticazione](./media/picturepark-tutorial/ic795063.png "Autenticazione")

4. Nella sezione **Configurazione provider di identità** , eseguire questa procedura:
   
    ![Configurazione del provider di identità](./media/picturepark-tutorial/ic795064.png "Configurazione del provider di identità")
   
    a. Fare clic su **Aggiungi**.
  
    b. Digitare un nome per la configurazione.
   
    c. Selezionare **Imposta come predefinito**.
   
    d. Nella casella di testo **Issuer URI** (URI autorità di certificazione) incollare il valore di **URL di accesso** copiato dal portale di Azure.
   
    e. Nella casella di testo **Trusted Issuer Thumb Print** (Identificazione personale autorità emittente attendibile) incollare il valore di **Identificazione personale** copiato dalla sezione **Certificato di firma SAML**. 

5. Fare clic su **JoinDefaultUsersGroup**.

6. Per impostare l'attributo **EmailAddress** nella casella di testo **Claim** (Attestazione), digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` e fare clic su **Salva**.

      ![Configurazione](./media/picturepark-tutorial/ic795065.png "Configurazione")

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Picturepark.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Picturepark**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Picturepark**.

    ![Collegamento di Picturepark nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-picturepark-test-user"></a>Creare l'utente di test di Picturepark

Per consentire agli utenti di Azure AD di accedere a Picturepark, è necessario effettuarne il provisioning in Picturepark. Nel caso di Picturepark, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Picturepark**.

1. Nella barra degli strumenti in alto fare clic su **Strumenti di amministrazione**, quindi su **Utenti**.
   
    ![Utenti](./media/picturepark-tutorial/ic795067.png "Utenti")

1. Nella scheda **Panoramica utenti** fare clic su **Nuovo**.
   
    ![Gestione degli utenti](./media/picturepark-tutorial/ic795068.png "Gestione degli utenti")

1. Nella finestra di dialogo **Crea utente** eseguire i passaggi seguenti per un utente di Azure Active Directory valido di cui si vuole eseguire il provisioning:
   
    ![Crea utente](./media/picturepark-tutorial/ic795069.png "Crea utente")
   
    a. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'**indirizzo di posta elettronica** dell'utente `BrittaSimon@contoso.com`.  
   
    b. Nelle caselle di testo **Password** e **Conferma password** digitare la **password** di BrittaSimon. 
   
    c. Nella casella di testo **Nome** digitare il **nome** dell'utente **Britta**. 
   
    d. Nella casella di testo **Cognome** digitare il **cognome** dell'utente **Simon**.
   
    e. Nella casella di testo **Società** digitare il **nome della società** dell'utente. 
   
    f. Nella casella di testo **Country** (Paese) selezionare il **paese/area geografica** dell'utente.
  
    g. Nella casella **CAP** digitare il **CAP** della città.
   
    h. Nella casella di testo **Città** digitare il **nome della città** dell'utente.

    i. Selezionare una **Language**.
   
    j. Fare clic su **Create**(Crea).

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerta da Picturepark per eseguire il provisioning degli account utente di Azure AD.
>

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Picturepark nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Picturepark per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

