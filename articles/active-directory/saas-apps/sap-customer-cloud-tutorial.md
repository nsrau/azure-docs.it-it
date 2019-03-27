---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Cloud for Customer | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba76692ec35ddfd0b6c8c49306d6056709d684e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57840682"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Cloud for Customer

Questa esercitazione descrive come integrare SAP Cloud for Customer con Azure Active Directory (Azure AD).
L'integrazione di SAP Cloud for Customer con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SAP Cloud for Customer.
* È possibile abilitare gli utenti per l'accesso automatico a SAP Cloud for Customer (Single Sign-On) con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Cloud for Customer, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SAP Cloud for Customer abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Cloud for Customer supporta l'accesso SSO avviato da **provider di servizi**

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Aggiunta di SAP Cloud for Customer dalla raccolta

Per configurare l'integrazione di SAP Cloud for Customer in Azure AD, è necessario aggiungere SAP Cloud for Customer dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SAP Cloud for Customer dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SAP Cloud for Customer**, selezionare **SAP Cloud for Customer** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![SAP Cloud for Customer nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Cloud for Customer con un utente test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Cloud for Customer.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Cloud for Customer, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SAP Cloud for Customer](#configure-sap-cloud-for-customer-single-sign-on)**: per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)**: per avere una controparte di Britta Simon in SAP Cloud for Customer collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SAP Cloud for Customer, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SAP Cloud for Customer** del [portale di Azure](https://portal.azure.com/), selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud for Customer](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://<server name>.crm.ondemand.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto client di SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione SAP Cloud for Customer prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Attributi utente e attestazioni** eseguire questa procedura:

    a. Fare clic sull'**icona Modifica** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selezionare **Trasformazione** come **origine**.

    c. Nell'elenco **Trasformazione** selezionare **ExtractMailPrefix()**.

    d. Nell'elenco **Parametro 1** selezionare l'attributo utente da usare per l'implementazione.
    Ad esempio, se si vuole usare il valore EmployeeID come identificatore utente univoco e il valore dell'attributo è stato archiviato in ExtensionAttribute2, selezionare user.extensionattribute2.

    e. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

8. Nella sezione **Configura SAP Cloud for Customer** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-sap-cloud-for-customer-single-sign-on"></a>Configurare l'accesso Single Sign-On di SAP Cloud for Customer
   
1. Accedere al portale di SAP Cloud for Customer con diritti di amministratore.
   
2. Passare a **Attività comuni di gestione utenti e applicazioni** e fare clic sulla scheda **Provider di identità**.
   
3. Fare clic su **New Identity Provider** (Nuovo provider di identità) e selezionare il file XML dei metadati scaricato dal portale di Azure. Quando si importano i metadati, il sistema carica automaticamente il certificato di firma e il certificato di crittografia necessari.
   
    ![Configure Single Sign-On](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
4. Azure Active Directory richiede l'elemento URL del servizio consumer di asserzione nella richiesta SAML, quindi selezionare la casella di controllo **Include Assertion Consumer Service URL** (Includi URL del servizio Consumer di asserzione).
   
5. Fare clic su **Activate Single Sign-On**(Attiva Single Sign-On).
   
6. Salvare le modifiche.
   
7. Fare clic sulla scheda **My System** (Sistema locale).
   
    ![Configure Single Sign-On](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
8. Nella casella di testo **URL di accesso di Azure AD** incollare l'**URL di accesso** copiato dal portale di Azure.
   
    ![Configure Single Sign-On](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
9. Specificare se il dipendente può scegliere manualmente tra l'accesso con ID utente e password o SSO selezionando **Manual Identity Provider Selection**(Selezione manuale provider di identità).
   
10. Nella sezione **SSO URL** (URL SSO), specificare l'URL che deve essere usato dai dipendenti per l'accesso al sistema. 
    Nell'elenco **URL Sent to Employee** (URL inviato al dipendente) è possibile scegliere tra le opzioni seguenti:
   
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
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a SAP Cloud for Customer.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SAP Cloud for Customer**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, digitare e selezionare **SAP Cloud for Customer**.

    ![Collegamento di SAP Cloud for Customer nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Creare un utente di test di SAP Cloud for Customer

In questa sezione viene creato un utente di nome Britta Simon in SAP Cloud for Customer. Per aggiungere gli utenti nella piattaforma SAP Cloud for Customer, contattare il  [team di supporto di SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

> [!NOTE]
> Assicurarsi che il valore di NameID corrisponda al campo username nella piattaforma SAP Cloud for Customer.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SAP Cloud for Customer nel pannello di accesso, si accederà automaticamente all'applicazione SAP Cloud for Customer per cui è stato configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

