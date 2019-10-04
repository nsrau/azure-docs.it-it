---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Analytics Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Analytics Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd01c86086c7491d22f655fcba5da237286412f
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347789"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Esercitazione: Integrare SAP Analytics Cloud con Azure Active Directory

Questa esercitazione descrive come integrare SAP Analytics Cloud con Azure Active Directory (Azure AD). Integrando SAP Analytics Cloud con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a SAP Analytics Cloud.
* Abilitare gli utenti per l'accesso automatico a SAP Analytics Cloud con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di SAP Analytics Cloud abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAP Analytics Cloud supporta l'accesso SSO avviato da **SP**

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Aggiunta di SAP Analytics Cloud dalla raccolta

Per configurare l'integrazione di SAP Analytics Cloud in Azure AD, è necessario aggiungere SAP Analytics Cloud dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAP Analytics Cloud** nella casella di ricerca.
1. Selezionare **SAP Analytics Cloud** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con SAP Analytics Cloud usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Analytics Cloud.

Per configurare e testare l'accesso SSO di Azure AD con SAP Analytics Cloud, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di SAP Analytics Cloud](#configure-sap-analytics-cloud-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di SAP Analytics Cloud](#create-sap-analytics-cloud-test-user)** : per avere una controparte di B.Simon in SAP Analytics Cloud collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAP Analytics Cloud** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente:

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente:
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > I valori in questi URL sono forniti solo a scopo dimostrativo. Aggiornare i valori con l'URL di accesso e l'URL dell'identificatore effettivi. Per ottenere l'URL di accesso, contattare il [team di supporto clienti di SAP Analytics Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Per ottenere l'URL dell'identificatore, scaricare i metadati di SAP Analytics Cloud dalla console di amministrazione. Questo concetto verrà spiegato più avanti nell'esercitazione.

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura SAP Analytics Cloud** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Configurare l'accesso SSO di SAP Analytics Cloud

1. In un'altra finestra del Web browser accedere al sito aziendale di SAP Analytics Cloud come amministratore.

2. Selezionare **Menu** > **Sistema** > **Amministrazione**.
    
    ![Selezionare Menu, quindi Sistema e infine Amministrazione](./media/sapboc-tutorial/config1.png)

3. Selezionare l'icona **Modifica** (penna) nella scheda **Sicurezza**.
    
    ![Selezionare l'icona Modifica nella scheda Sicurezza](./media/sapboc-tutorial/config2.png)  

4. Per **Metodo di autenticazione** selezionare **SAML Single Sign-On (SSO)** .

    ![Selezionare SAML Single Sign-On per il metodo di autenticazione](./media/sapboc-tutorial/config3.png)  

5. Per scaricare i metadati del provider di servizi (passaggio 1), selezionare **Download**. Nel file di metadati trovare e copiare il valore **entityID**. Nella finestra di dialogo **Configurazione SAML di base** del portale di Azure incollare il valore nella casella **Identificatore**.

    ![Copiare e incollare il valore di entityID](./media/sapboc-tutorial/config4.png)  

6. Per caricare i metadati del provider di servizi (passaggio 2) nel file scaricato nel portale di Azure, in **Upload Identity Provider metadata** (Caricare i metadati del provider di identità) selezionare **Carica**.  

    ![In Upload Identity Provider metadata (Caricare i metadati del provider di identità) selezionare Carica](./media/sapboc-tutorial/config5.png)

7. Nell'elenco **Attributo utente** selezionare l'attributo utente (passaggio 3) da usare per l'implementazione. Questo attributo utente esegue il mapping al provider di identità. Per immettere un attributo personalizzato nella pagina dell'utente, usare l'opzione **Custom SAML Mapping** (Mapping SAML personalizzato). oppure selezionare **Posta elettronica** o **ID UTENTE** come attributo utente. Nell'esempio è stato selezionato **Posta elettronica** perché è stato eseguito il mapping dell'attestazione dell'ID utente con l'attributo **userprincipalname** nella sezione **Attributi utente e attestazioni** del portale di Azure. Si ottiene così un indirizzo di posta elettronica dell'utente univoco, che viene inviato all'applicazione SAP Analytics Cloud in ogni risposta SAML con esito positivo.

    ![Selezionare Attributo utente](./media/sapboc-tutorial/config6.png)

8. Per verificare l'account con il provider di identità (passaggio 4), nella casella **Login Credential (Email)** (Credenziale di accesso - Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente. Selezionare quindi **Verifica account**. Il sistema aggiunge le credenziali di accesso all'account utente.

    ![Immettere l'indirizzo di posta elettronica e selezionare Verifica account](./media/sapboc-tutorial/config7.png)

9. Selezionare l'icona **Salva**.

    ![Icona Salva](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Analytics Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAP Analytics Cloud**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sap-analytics-cloud-test-user"></a>Creare l'utente di test di SAP Analytics Cloud

È necessario effettuare il provisioning degli utenti di Azure AD in SAP Analytics Cloud perché possano accedere a SAP Business Object Cloud. In SAP Analytics Cloud il provisioning è un'attività manuale.

Per effettuare il provisioning di un account utente:

1. Accedere al sito aziendale di SAP Analytics Cloud come amministratore.

2. Selezionare **Menu** > **Security** (Sicurezza)  > **Users** (Utenti).

    ![Aggiungere un dipendente](./media/sapboc-tutorial/user1.png)

3. Nella pagina **Users** (Utenti) selezionare **+** per aggiungere i dettagli di un nuovo utente. 

    ![Pagina Aggiungi utenti (Aggiungi utenti)](./media/sapboc-tutorial/user4.png)

    Completare quindi i passaggi seguenti:

    a. Nella casella **USER ID** (ID UTENTE) immettere l'ID dell'utente, ad esempio **B**.

    b. Nella casella **NAME** (NOME) immettere il nome dell'utente, ad esempio **B**.

    c. Nella casella **LAST NAME** (COGNOME) immettere il cognome dell'utente, ad esempio **Simon**.

    d. Nella casella **DISPLAY NAME** (NOME VISUALIZZATO) immettere nome e cognome dell'utente, ad esempio **B.Simon**.

    e. Nella casella **E-MAIL** (POSTA ELETTRONICA) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `b.simon@contoso.com`.

    f. Nella pagina **Select Roles** (Selezione ruoli) selezionare il ruolo appropriato per l'utente e quindi fare clic su **OK**.

      ![Selezionare il ruolo](./media/sapboc-tutorial/user3.png)

    g. Selezionare l'icona **Salva**.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di SAP Analytics Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SAP Analytics Cloud per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

