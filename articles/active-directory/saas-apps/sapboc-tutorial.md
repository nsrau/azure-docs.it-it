---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Business Object Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Business Object Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c397cd3b535c7bf918eac2ac1e9ae1f967d7b95a
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877504"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Business Object Cloud

Questa esercitazione descrive come integrare SAP Business Object Cloud con Azure Active Directory (Azure AD).
L'integrazione di SAP Business Object Cloud con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a SAP Business Object Cloud.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a SAP Business Object Cloud con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Business Object Cloud, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di SAP Business Object Cloud abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Business Object Cloud supporta l'accesso SSO avviato da **SP**

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Aggiunta di SAP Business Object Cloud dalla raccolta

Per configurare l'integrazione di SAP Business Object Cloud in Azure AD, è necessario aggiungere SAP Business Object Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SAP Business Object Cloud dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **SAP Business Object Cloud**, selezionare **SAP Business Object Cloud** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![SAP Business Object Cloud nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Business Object Cloud con un utente di test di nome**Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Business Object Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Business Object Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SAP Business Object Cloud](#configure-sap-business-object-cloud-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente test di SAP Business Object Cloud](#create-sap-business-object-cloud-test-user)**: per avere una corrispondenza di Britta Simon in SAP Business Object Cloud collegata alla relativa rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con SAP Business Object Cloud, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **SAP Business Object Cloud** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Business Object Cloud](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente:
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il modello seguente:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > I valori in questi URL sono forniti solo a scopo dimostrativo. Aggiornare i valori con l'URL di accesso e l'URL dell'identificatore effettivi. Per ottenere l'URL di accesso, contattare il [team di supporto clienti di SAP Business Object Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Per ottenere l'URL dell'identificatore, scaricare i metadati di SAP Business Object Cloud dalla console di amministrazione. Questo concetto verrà spiegato più avanti nell'esercitazione.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>Configurare l'accesso Single Sign-On di SAP Business Object Cloud

1. In un'altra finestra del Web browser accedere al sito aziendale di SAP Business Object Cloud come amministratore.

2. Selezionare **Menu** > **Sistema** > **Amministrazione**.
    
    ![Selezionare Menu, quindi Sistema e infine Amministrazione](./media/sapboc-tutorial/config1.png)

3. Selezionare l'icona **Modifica** (penna) nella scheda **Sicurezza**.
    
    ![Selezionare l'icona Modifica nella scheda Sicurezza](./media/sapboc-tutorial/config2.png)  

4. Per **Metodo di autenticazione** selezionare **SAML Single Sign-On (SSO)**.

    ![Selezionare SAML Single Sign-On per il metodo di autenticazione](./media/sapboc-tutorial/config3.png)  

5. Per scaricare i metadati del provider di servizi (passaggio 1), selezionare **Download**. Nel file di metadati trovare e copiare il valore **entityID**. Nella finestra di dialogo **Configurazione SAML di base** del portale di Azure incollare il valore nella casella **Identificatore**.

    ![Copiare e incollare il valore di entityID](./media/sapboc-tutorial/config4.png)  

6. Per caricare i metadati del provider di servizi (passaggio 2) nel file scaricato nel portale di Azure, in **Upload Identity Provider metadata** (Caricare i metadati del provider di identità) selezionare **Carica**.  

    ![In Upload Identity Provider metadata (Caricare i metadati del provider di identità) selezionare Carica](./media/sapboc-tutorial/config5.png)

7. Nell'elenco **Attributo utente** selezionare l'attributo utente (passaggio 3) da usare per l'implementazione. Questo attributo utente esegue il mapping al provider di identità. Per immettere un attributo personalizzato nella pagina dell'utente, usare l'opzione **Custom SAML Mapping** (Mapping SAML personalizzato). oppure selezionare **Posta elettronica** o **ID UTENTE** come attributo utente. Nell'esempio è stato selezionato **Posta elettronica** perché è stato eseguito il mapping dell'attestazione dell'ID utente con l'attributo **userprincipalname** nella sezione **Attributi utente e attestazioni** del portale di Azure. Si ottiene così un indirizzo di posta elettronica dell'utente univoco, che viene inviato all'applicazione SAP Business Object Cloud in ogni risposta SAML con esito positivo.

    ![Selezionare Attributo utente](./media/sapboc-tutorial/config6.png)

8. Per verificare l'account con il provider di identità (passaggio 4), nella casella **Login Credential (Email)** (Credenziale di accesso - Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente. Selezionare quindi **Verifica account**. Il sistema aggiunge le credenziali di accesso all'account utente.

    ![Immettere l'indirizzo di posta elettronica e selezionare Verifica account](./media/sapboc-tutorial/config7.png)

9. Selezionare l'icona **Salva**.

    ![Icona Salva](./media/sapboc-tutorial/save.png)

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

In questa sezione Britta Simon viene abilitata all'uso di Single Sign-On di Azure attraverso la concessione dell'accesso a SAP Business Object Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **SAP Business Object Cloud**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SAP Business Object Cloud**.

    ![Collegamento di SAP Business Object Cloud nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sap-business-object-cloud-test-user"></a>Creare un utente di test di SAP Business Object Cloud

È necessario effettuare il provisioning degli utenti di Azure AD in SAP Business Object Cloud perché possano accedere a SAP Business Object Cloud. In SAP Business Object Cloud il provisioning è un'attività manuale.

Per effettuare il provisioning di un account utente:

1. Accedere al sito aziendale di SAP Business Object Cloud come amministratore.

2. Selezionare **Menu** > **Security** (Sicurezza)  > **Users** (Utenti).

    ![Aggiungere un dipendente](./media/sapboc-tutorial/user1.png)

3. Nella pagina **Users** (Utenti) selezionare **+** per aggiungere i dettagli di un nuovo utente. 

    ![Pagina Aggiungi utenti (Aggiungi utenti)](./media/sapboc-tutorial/user4.png)

    Completare quindi i passaggi seguenti:

    a. Nella casella **USER ID** (ID UTENTE) immettere l'ID dell'utente, ad esempio, **Britta**.

    b. Nella casella **FIRST NAME** (NOME) immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella **LAST NAME** (COGNOME) immettere il cognome dell'utente, ad esempio **Simon**.

    d. Nella casella **NOME VISUALIZZATO** immettere nome e cognome dell'utente, ad esempio **Britta Simon**.

    e. Nella casella **E-MAIL** (POSTA ELETTRONICA) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    f. Nella pagina **Select Roles** (Selezione ruoli) selezionare il ruolo appropriato per l'utente e quindi fare clic su **OK**.

      ![Selezionare il ruolo](./media/sapboc-tutorial/user3.png)

    g. Selezionare l'icona **Salva**.    

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SAP Business Object Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SAP Business Object Cloud per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

