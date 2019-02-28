---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69ca6edb9dd125350e1e557633cdfd6f07217c06
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867644"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Cloud Platform Identity Authentication

Questa esercitazione descrive la modalità di integrazione di SAP Cloud Platform Identity Authentication con Azure Active Directory (Azure AD).
L'integrazione di SAP Cloud Platform Identity Authentication con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SAP Cloud Platform Identity Authentication.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SAP Cloud Platform Identity Authentication con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Cloud Platform Identity Authentication, è necessario quanto segue:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SAP Cloud Platform Identity Authentication abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Cloud Platform Identity Authentication supporta l'accesso SSO avviato da **SP** e **IDP**

Prima di approfondire i dettagli tecnici, è fondamentale comprendere i concetti che si desidera esaminare. SAP Cloud Platform Identity Authentication e Active Directory Federation Services consentono di implementare l'accesso SSO per applicazioni o servizi protetti da Azure AD (come IdP) con applicazioni e servizi SAP protetti da SAP Cloud Platform Identity Authentication.

Attualmente SAP Cloud Platform Identity Authentication funge da provider di identità proxy per applicazioni SAP. Azure Active Directory a sua volta agisce come provider di identità iniziale in questa configurazione. 

Il diagramma seguente illustra questa relazione:

![Creazione di un utente test di Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Con questa configurazione, il tenant di SAP Cloud Platform Identity Authentication viene configurato come applicazione attendibile in Azure Active Directory.

Tutte le applicazioni e i servizi SAP che si desidera proteggere in questo modo vengono configurati successivamente nella console di gestione di SAP Cloud Platform Identity Authentication.

L'autorizzazione per la concessione dell'accesso a servizi e applicazioni SAP deve pertanto avvenire in SAP Cloud Platform Identity Authentication (diversamente dalla configurazione dell'autorizzazione in Azure Active Directory).

Tramite la configurazione di SAP Cloud Platform Identity Authentication come un'applicazione tramite Marketplace di Azure Active Directory, non è necessario configurare singole attestazioni o asserzioni SAML.

> [!NOTE]
> Attualmente solo Web SSO è stato testato da entrambe le parti. I flussi necessari per la comunicazione da app ad API o da API ad API dovrebbero funzionare ma non sono ancora stati testati. I test verranno eseguiti durante le attività successive.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Aggiunta di SAP Cloud Platform Identity Authentication dalla raccolta

Per configurare l'integrazione di SAP Cloud Platform Identity Authentication in Azure AD, è necessario aggiungere SAP Cloud Platform Identity Authentication dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SAP Cloud Platform Identity Authentication dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SAP Cloud Platform Identity Authentication** selezionare **SAP Cloud Platform Identity Authentication** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![SAP Cloud Platform Identity Authentication nell'elenco di risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con [Nome applicazione] usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in [Nome applicazione].

Per configurare e testare l'accesso Single Sign-On di Azure AD con [Nome applicazione], è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di SAP Cloud Platform Identity Authentication](#create-sap-cloud-platform-identity-authentication-test-user)**: per avere una controparte di Britta Simon in SAP Cloud Platform Identity Authentication collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con [Nome applicazione], seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SAP Cloud Platform Identity Authentication** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IdP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform Identity Authentication](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Contattare il [team di supporto di SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) per ottenere questi valori. Se non si conosce il valore dell'identificatore, seguire la documentazione di SAP Cloud Platform Identity Authentication in [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) (Configurazione del tenant SAML 2.0).

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Cloud Platform Identity Authentication](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Usare l'URL del Sign-on dell'applicazione aziendale specifica. Contattare il [team di supporto di SAP Cloud Platform Identity Authentication Client](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) in caso di dubbi.

6. L'applicazione SAP Cloud Platform Identity Authentication prevede le asserzioni SAML in un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

7. Se l'applicazione SAP prevede un attributo, come **firstName**, aggiungere l'attributo **firstName** nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente**, configurare l'attributo del token SAML come illustrato nell'immagine precedente e seguire questa procedura:

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo firstName.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo user.givenname.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

8. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

9. Nella sezione **Set up SAP Cloud Platform Identity Authentication** (Configura SAP Cloud Platform Identity Authentication) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Configurare l'accesso Single Sign-On per SAP Cloud Platform Identity Authentication

1. Per configurare SSO per l'applicazione, passare alla console di amministrazione di SAP Cloud Platform Identity Authentication. L'URL ha il formato seguente: `https://<tenant-id>.accounts.ondemand.com/admin`. Leggere quindi la documentazione relativa a SAP Cloud Platform Identity Authentication disponibile nella pagina [Integration with Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html) (Integrazione con Microsoft Azure AD).

2. Nel portale di Azure selezionare il pulsante **Salva**.

3. Continuare la procedura seguente solo se si vuole aggiungere e abilitare l'accesso SSO per un'altra applicazione SAP. Ripetere la procedura descritta nella sezione **Aggiunta di SAP Cloud Platform Identity Authentication dalla raccolta**.

4. Nel portale di Azure, nella pagina di integrazione di **SAP Cloud Platform Identity Authentication** selezionare **Accesso collegato**.

    ![Configurare l'accesso collegato](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Salvare la configurazione.

> [!NOTE]
> La nuova applicazione sfrutterà la configurazione del processo SSO dell'applicazione SAP precedente. Assicurarsi di usare gli stessi provider di identità aziendale nella console di amministrazione di SAP Cloud Platform Identity Authentication.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a SAP Cloud Platform Identity Authentication.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SAP Cloud Platform Identity Authentication**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SAP Cloud Platform Identity Authentication**.

    ![Collegamento di SAP HANA Cloud Platform Identity Authentication nell'elenco di applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Creare l'utente di test di SAP Cloud Platform Identity Authentication

Non è necessario creare un utente in SAP Cloud Platform Identity Authentication. Gli utenti presenti nell'archivio utenti di Azure AD possono usare la funzionalità di accesso SSO.

SAP Cloud Platform Identity Authentication supporta l'opzione di federazione delle identità. Questa opzione consente all'applicazione di controllare se gli utenti autenticati dal provider di identità aziendale sono presenti nell'archivio utenti di SAP Cloud Platform Identity Authentication.

Per impostazione predefinita l'opzione di federazione delle identità è disabilitata. Se la federazione delle identità è abilitata, solo gli utenti che vengono importati in SAP Cloud Platform Identity Authentication sono in grado di accedere all'applicazione.

Per altre informazioni su come abilitare o disabilitare la federazione delle identità con SAP Cloud Platform Identity Authentication, vedere Enable Identity Federation with SAP Cloud Platform Identity Authentication (Abilitare la federazione delle identità con SAP Cloud Platform Identity Authentication) in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Configurare la federazione delle identità con l'archivio utenti di SAP Cloud Platform Identity Authentication).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SAP Cloud Platform Identity Authentication nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SAP Cloud Platform Identity Authentication per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
