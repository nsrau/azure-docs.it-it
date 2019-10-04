---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091681"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign

Questa esercitazione descrive come integrare SAP Business ByDesign con Azure Active Directory (Azure AD).
L'integrazione di SAP Business ByDesign con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SAP Business ByDesign.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SAP Business ByDesign con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Business ByDesign, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SAP Business ByDesign abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Business ByDesign supporta l'accesso SSO avviato da **SP**

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Aggiunta di SAP Business ByDesign dalla raccolta

Per configurare l'integrazione di SAP Business ByDesign in Azure AD, è necessario aggiungere SAP Business ByDesign dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SAP Business ByDesign dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SAP Business ByDesign**, selezionare **SAP Business ByDesign** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SAP Business ByDesign nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Business ByDesign con un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Business ByDesign.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Business ByDesign, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SAP Business ByDesign](#configure-sap-business-bydesign-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di SAP Business ByDesign](#create-sap-business-bydesign-test-user)** : per avere una controparte di Britta Simon in SAP Business ByDesign collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SAP Business ByDesign, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [SAP Business ByDesign](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Business ByDesign](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<servername>.sapbydesign.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione SAP Business ByDesign prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Fare clic sull'icona **Modifica** per modificare il **valore dell'identificatore Nome**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Nella sezione **Gestisci attestazioni utente** seguire questa procedura: ![immagine](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selezionare **Trasformazione** come **Origine**.

    b. Nell'elenco a discesa **Trasformazione**, selezionare **ExtractMailPrefix()** .

    c. Nell'elenco a discesa **Parametro 1** selezionare l'attributo utente da usare per l'implementazione. Ad esempio, se si vuole usare il valore EmployeeID come identificatore utente univoco e il valore dell'attributo è stato archiviato in ExtensionAttribute2, selezionare user.extensionattribute2.

    d. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Configura SAP Business ByDesign** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Configurare il single Sign-On per SAP Business ByDesign

1. Accedere al portale di SAP Business ByDesign con diritti di amministratore.

2. Passare a **Application and User Management Common Task** (Attività comuni di gestione utenti e applicazioni) e fare clic sulla scheda **Identity Provider** (Provider di identità).

3. Fare clic su **New Identity Provider** (Nuovo provider di identità) e selezionare il file XML metadati scaricato dal portale di Azure. Quando si importano i metadati, il sistema carica automaticamente il certificato di firma e il certificato di crittografia necessari.

    ![Configure Single Sign-On](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Per includere l'**URL del servizio consumer di asserzione** nella richiesta SAML, selezionare **Include Assertion Consumer Service URL** (Includi URL del servizio Consumer di asserzione).

5. Fare clic su **Activate Single Sign-On**(Attiva Single Sign-On).

6. Salvare le modifiche.

7. Fare clic sulla scheda **My System** (Sistema locale).

    ![Configure Single Sign-On](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Nella casella di testo **Azure AD Sign-On URL** (URL di accesso SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    ![Configure Single Sign-On](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Specificare se il dipendente può scegliere manualmente tra l'accesso con ID utente e password o SSO selezionando **Manual Identity Provider Selection**(Selezione manuale provider di identità).

10. Nella sezione **SSO URL** (URL SSO), specificare l'URL che deve essere usato dai dipendenti per l'accesso al sistema.
    Nell'elenco a discesa URL Sent to Employee (URL inviato al dipendente) è possibile scegliere tra le opzioni seguenti:

    **Non-SSO URL**

    Il sistema invia al dipendente solo il normale URL di sistema. Il dipendente non può accedere con SSO e deve usare invece la password o il certificato.

    **SSO URL**

    Il sistema invia al dipendente solo l'URL SSO. Il dipendente può accedere con SSO. La richiesta di autenticazione viene reindirizzata tramite IdP.

    **Automatic Selection**

    Se SSO non è attivo, il sistema invia al dipendente il normale URL di sistema. Se SSO è attivo, il sistema verifica se il dipendente ha una password. Se è disponibile una password, vengono inviati al dipendente sia l'URL SSO che l'URL non SSO. Tuttavia, se il dipendente non ha una password, riceverà solo l'URL SSO.

11. Salvare le modifiche.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Business ByDesign.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SAP Business ByDesign**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **SAP Business ByDesign**.

    ![Collegamento di SAP Business ByDesign nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sap-business-bydesign-test-user"></a>Creare un utente di test di SAP Business ByDesign

In questa sezione viene creato un utente di nome Britta Simon in SAP Business ByDesign. Collaborare con il [team di supporto clienti di SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) per aggiungere gli utenti nella piattaforma SAP Business ByDesign. 

> [!NOTE]
> Assicurarsi che il valore di NameID corrisponda al campo username nella piattaforma SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SAP Business ByDesign nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SAP Business ByDesign per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
