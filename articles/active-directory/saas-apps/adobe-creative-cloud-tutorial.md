---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Adobe Creative Cloud | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 815cffab118f6900c1c9d42a7e44821f8af62532
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081977"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Adobe Creative Cloud

Questa esercitazione descrive come integrare Adobe Creative Cloud con Azure Active Directory (Azure AD). Integrando Adobe Creative Cloud con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere ad Adobe Creative Cloud.
* Abilitare gli utenti per l'accesso automatico ad Adobe Creative Cloud con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Creative Cloud abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Adobe Creative Cloud supporta l'accesso SSO avviato da **SP**





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Aggiungere Adobe Creative Cloud dalla raccolta

Per configurare l'integrazione di Adobe Creative Cloud in Azure AD, è necessario aggiungere Adobe Creative Cloud dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Adobe Creative Cloud** nella casella di ricerca.
1. Selezionare **Adobe Creative Cloud** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per Adobe Creative Cloud

Configurare e testare l'accesso SSO di Azure AD con Adobe Creative Cloud usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Creative Cloud.

Per configurare e testare l'accesso SSO di Azure AD con Adobe Creative Cloud, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Adobe Creative Cloud](#configure-adobe-creative-cloud-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** : per avere una controparte di B.Simon in Adobe Creative Cloud collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Adobe Creative Cloud** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare l'URL `https://adobe.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Il valore dell'identificatore non è reale. è necessario aggiornare questo valore con l'ID effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Adobe Creative Cloud prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione Adobe Creative Cloud prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | NOME | Attributo di origine|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > Gli utenti devono disporre di una licenza di Office 365 ExO valida per il valore di attestazione della posta elettronica da inserire nella risposta SAML.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura Adobe Creative Cloud** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Adobe Creative Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Adobe Creative Cloud**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-adobe-creative-cloud-sso"></a>Configurare l'accesso Single Sign-On di Adobe Creative Cloud

1. In un'altra finestra del Web browser accedere ad [Adobe Admin Console](https://adminconsole.adobe.com) come amministratore.

2. Passare a **Impostazioni** nella barra di spostamento superiore e quindi scegliere **Identità**. Viene aperto l'elenco dei domini. Fare clic sul collegamento **Configura** per il proprio dominio. Eseguire i passaggi descritti di seguito nella sezione di **configurazione dell'accesso Single Sign-On**. Per altre informazioni, vedere [Setup a domain](https://helpx.adobe.com/enterprise/using/set-up-domain.html) (Configurare un dominio).

    ![Impostazioni](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Impostazioni")

    a. Fare clic su **Browse** (Sfoglia) per caricare il certificato scaricato da Azure AD in **IDP Certificate** (Certificato IDP).

    b. Nella casella di testo **IDP Issuer** (Autorità di certificazione IDP) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **IDP Login URL** (URL di accesso IDP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Selezionare **HTTP - Redirect** (HTTP - Reindirizzamento) per **IDP Binding** (Binding IDP).

    e. Selezionare **Email Address** (Indirizzo di posta elettronica) per **User Login Setting** (Impostazione accesso utente).

    f. Fare clic sul pulsante **Salva**.

3. Il dashboard visualizza ora il file XML **"Download Metadata"** , che contiene l'URL EntityDescriptor e l'URL AssertionConsumerService di Adobe. Aprire il file e configurare gli URL nell'applicazione Azure AD.

    ![Configurazione accesso Single Sign-On sul lato app](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Usare il valore di EntityDescriptor indicato da Adobe per l'**identificatore** nella finestra di dialogo **Configurare le impostazioni dell'app**.

    b. Usare il valore di AssertionConsumerService specificato da Adobe per **URL di risposta** nella finestra di dialogo **Configurare le impostazioni dell'app**.

### <a name="create-adobe-creative-cloud-test-user"></a>Creare l'utente di test di Adobe Creative Cloud

Per consentire agli utenti di Azure AD di accedere ad Adobe Creative Cloud, è necessario effettuarne il provisioning in Adobe Creative Cloud. Nel caso di Adobe Creative Cloud il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito [Adobe Admin Console](https://adminconsole.adobe.com) come amministratore.

2. Aggiungere l'utente nella console di Adobe come ID federato e assegnarlo a un profilo di prodotto. Per informazioni dettagliate sull'aggiunta di utenti, vedere [Manage Users](https://helpx.adobe.com/enterprise/using/users.html#Addusers) (Gestire gli utenti). 

3. A questo punto, digitare l'indirizzo di posta elettronica/UPN nel modulo di registrazione, premere sulla scheda e si dovrebbe essere federati di nuovo in Azure AD:
   * Accesso Web: www\.adobe.com > sign-in (accedi)
   * All'interno dell'utilità dell'app del desktop > sign-in (accedi)
   * All'interno dell'applicazione > help (guida) > sign-in (accedi)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Adobe Creative Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Adobe Creative Cloud per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Adobe Creative Cloud con Azure AD](https://aad.portal.azure.com/)

- [Set up a domain (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html) (Configurare un dominio)
  
- [Configure Azure for use with Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html) (Configurare Azure per l'uso con Adobe SSO)

