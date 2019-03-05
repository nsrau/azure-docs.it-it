---
title: 'Esercitazione: Integrazione di Azure Active Directory con AnswerHub | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d6ff44a8d760150df491b67ee56d62c4daff31
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882791"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Esercitazione: Integrazione di Azure Active Directory con AnswerHub

Questa esercitazione descrive come integrare AnswerHub con Azure Active Directory (Azure AD).
L'integrazione di AnswerHub con Azure AD offre i vantaggi seguenti:

* È possibile usare Azure AD per controllare chi può accedere ad AnswerHub.
* È possibile consentire agli utenti di accedere automaticamente ad AnswerHub con gli account Azure AD personali (Single Sign-On).
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con AnswerHub, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere [qui](https://azure.microsoft.com/pricing/free-trial/) una versione di prova gratuita per un mese.
* Sottoscrizione di AnswerHub abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* AnswerHub supporta l'accesso SSO avviato da SP.

## <a name="add-answerhub-from-the-gallery"></a>Aggiungere AnswerHub dalla raccolta

Per configurare l'integrazione di AnswerHub in Azure AD, è necessario aggiungere AnswerHub dalla raccolta alle app SaaS gestite.

**Per aggiungere AnswerHub dalla raccolta:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **AnswerHub**. Selezionare **AnswerHub** nell'elenco risultati e quindi scegliere **Aggiungi**.

     ![AnswerHub nell'elenco risultati](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con AnswerHub usando un utente di test di nome Britta Simon.
Per l'accesso Single Sign-On, è necessario stabilire un collegamento tra un utente di Azure AD e l'utente corrispondente in AnswerHub.

Per configurare e testare l'accesso Single Sign-On di Azure AD con AnswerHub, è necessario completare queste attività:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Configurare l'accesso Single Sign-On per AnswerHub](#configure-answerhub-single-sign-on) per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) denominata Britta Simon.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Creare un utente di test di AnswerHub](#create-answerhub-test-user) che corrisponde ed è collegato all'utente di test di Azure AD.
6. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si configura l'accesso Single Sign-On di Azure AD nel portale di Azure,

**Per configurare l'accesso Single Sign-On di Azure AD con AnswerHub:**

1. Nella pagina di integrazione dell'applicazione **AnswerHub** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Pulsante per l'accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo di selezione del metodo per l'accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona di modifica per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Pagina Configura l'accesso Single Sign-On con SAML](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** completare la procedura seguente:

    ![Sezione Configurazione SAML di base](common/sp-identifier.png)

    a. Nella casella **URL di accesso** immettere un URL nel formato seguente: `https://<company>.answerhub.com`

    b. Nella casella **Identificatore (ID entità)** immettere un URL nel formato seguente: `https://<company>.answerhub.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con URL di accesso e identificatore effettivi. Per ottenere i valori, contattare il [team di supporto di AnswerHub](mailto:success@answerhub.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** accanto a **Certificato (Base64)**, in base ai propri requisiti, quindi salvare il certificato nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura AnswerHub** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

   È possibile copiare questi URL:
    - URL di accesso

    - Identificatore Azure AD

    - URL disconnessione

### <a name="configure-answerhub-single-sign-on"></a>Configurare l'accesso Single Sign-On per AnswerHub

In questa sezione viene configurato l'accesso Single Sign-On per AnswerHub.  

**Per configurare l'accesso Single Sign-On per AnswerHub:**

1. In un'altra finestra del Web browser accedere al sito aziendale di AnswerHub come amministratore.

    > [!NOTE]
    > Per assistenza per la configurazione di AnswerHub, contattare il [team di supporto di AnswerHub](mailto:success@answerhub.com.).

2. Passare a **Administration**.

3. Nella scheda **User and Groups** (Utenti e gruppi) selezionare **SAML Setup** (Configurazione SAML) nella sezione **Social Settings** (Impostazioni social network).

4. Nella scheda **IDP Config** (Configurazione IDP) completare questa procedura:

    ![Scheda Users and Groups](./media/answerhub-tutorial/ic785172.png "SAML Setup")  
  
    a. Nella casella **IDP Login URL** (URL di accesso IDP) incollare il valore di **URL di accesso** copiato dal portale di Azure.
  
    b. Nella casella **IDP Logout URL** (URL di disconnessione IDP) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    c. Nella casella **IDP Name Identifier Format** (Formato identificatore nome IDP) immettere il valore di **Identificatore** selezionato nella sezione **Attributi utente** del portale di Azure.
  
    d. Selezionare **Keys and Certificates** (Chiavi e certificati).

5. Nella sezione **Keys and Certificates** (Chiavi e certificati) completare questa procedura:

    ![Sezione Keys and Certificates](./media/answerhub-tutorial/ic785173.png "Keys and Certificates")  

    a. Aprire nel Blocco note il certificato con codifica Base64 scaricato dal portale di Azure e quindi incollarne il contenuto nella casella **IDP Public Key (x509 Format)** (Chiave pubblica IDP - formato x509).
  
    b. Selezionare **Save** (Salva).

6. Nella scheda **IDP Config** (Configurazione IDP) selezionare di nuovo **Save** (Salva).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

**Per creare un utente di test di Azure AD:**

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Selezionare Azure Active Directory, Utenti, Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente completare questa procedura.

    ![Proprietà utente](common/user-properties.png)

    a. Nella casella **Nome** immettere **BrittaSimon**.
  
    b. Nella casella **Nome utente** immettere **brittasimon@<dominio.estensione>**.  
    Ad esempio: BrittaSimon@contoso.com.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si configura Britta Simon per l'uso dell'accesso Single Sign-On di Azure AD concedendole l'accesso ad AnswerHub.

**Per assegnare l'utente di test di Azure AD:**

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **AnswerHub**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **AnswerHub**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. 

7. Fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

8. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-an-answerhub-test-user"></a>Creare un utente di test di AnswerHub

Per consentire agli utenti di Azure AD di accedere ad AnswerHub, è necessario aggiungerli ad AnswerHub. In AnswerHub questa attività viene eseguita manualmente.

**Per configurare un account utente:**

1. Accedere al sito aziendale di **AnswerHub** come amministratore.

2. Passare a **Administration**.

3. Selezionare la scheda **Users & Groups** (Utenti e gruppi).

4. Nel riquadro sinistro selezionare **Create or import users** (Crea o importa utenti) nella sezione **Manage Users** (Gestisci utenti) e quindi selezionare **Users & Groups** (Utenti e gruppi).

   Scheda ![Users & Groups](./media/answerhub-tutorial/ic785175.png "Users & Groups")

5. Nelle caselle appropriate immettere l'**indirizzo di posta elettronica**, il **nome utente** e la **password** di un account Azure AD valido da aggiungere e quindi selezionare **Save** (Salva).

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da AnswerHub per configurare gli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di AnswerHub nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione AnswerHub per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

