---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con CloudPassage | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0bcf0b4781e7ecb4301672f511a9f01f7fd2082
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561225"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con CloudPassage

Questa esercitazione descrive come integrare CloudPassage con Azure Active Directory (Azure AD). Integrando CloudPassage con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a CloudPassage.
* Abilitare gli utenti per l'accesso automatico a CloudPassage con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di CloudPassage abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* CloudPassage supporta l'accesso SSO avviato da **SP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-cloudpassage-from-the-gallery"></a>Aggiunta di CloudPassage dalla raccolta

Per configurare l'integrazione di CloudPassage in Azure AD, è necessario aggiungere CloudPassage dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **CloudPassage** nella casella di ricerca.
1. Selezionare **CloudPassage** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per CloudPassage

Configurare e testare l'accesso SSO di Azure AD con CloudPassage usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in CloudPassage.

Per configurare e testare l'accesso SSO di Azure AD con CloudPassage, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di CloudPassage](#configure-cloudpassage-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di CloudPassage](#create-cloudpassage-test-user)** : per avere una controparte di B.Simon in CloudPassage collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **CloudPassage** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

     a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://portal.cloudpassage.com/saml/init/accountid`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://portal.cloudpassage.com/saml/consume/accountid`. È possibile ottenere il valore di questo attributo facendo clic su **SSO Setup documentation** (Documentazione della configurazione SSO) nella sezione **Single Sign-on Settings** (Impostazioni Single Sign-On) del portale di CloudPassage.

    ![Configure Single Sign-On](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto clienti di CloudPassage](https://www.cloudpassage.com/company/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione CloudPassage prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione CloudPassage prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura CloudPassage** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a CloudPassage.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **CloudPassage**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cloudpassage-sso"></a>Configurare l'accesso Single Sign-On di CloudPassage

1. In un'altra finestra del Web browser accedere al sito aziendale di CloudPassage come amministratore.

1. Nel menu in alto fare clic su **Settings** (Impostazioni) e quindi su **Site Administration** (Amministrazione sito). 
   
    ![Configure Single Sign-On][12]

1. Fare clic sulla scheda **Authentication Settings** (Impostazioni autenticazione). 
   
    ![Configure Single Sign-On][13]

1. Nella sezione **Single Sign-On Settings** (Impostazioni Single Sign-On) seguire questa procedura: 
   
    ![Configure Single Sign-On][14]

    a. Selezionare la casella di controllo **Enable Single sign-on(SSO)(SSO Setup Documentation)** (Abilita Single Sign-On (SSO) (Documentazione di configurazione di SSO).
    
    b. Incollare il valore di **Identificatore Azure AD** nella casella di testo **SAML issuer URL** (URL autorità di certificazione SAML).
  
    c. Incollare il valore di **URL di accesso** nella casella di testo **SAML endpoint URL** (URL endpoint SAML).
  
    d. Incollare il valore di **URL disconnessione** nella casella di testo **Logout landing page** (Pagina di destinazione disconnessione).
  
    e. Aprire il certificato scaricato nel Blocco note, copiare il contenuto del certificato negli Appunti e quindi incollarlo nella casella di testo **Certificato X.509**.
  
    f. Fare clic su **Salva**.

### <a name="create-cloudpassage-test-user"></a>Creare l'utente di test di CloudPassage

Questa sezione descrive come creare un utente di nome B.Simon in CloudPassage.

**Per creare un utente di nome B.Simon in CloudPassage, seguire questa procedura:**

1. Accedere al sito aziendale di **CloudPassage** come amministratore. 

1. Nella barra degli strumenti in alto fare clic su **Settings** (Impostazioni) e quindi su **Site Administration** (Amministrazione sito). 
   
    ![Creazione di un utente test CloudPassage][22] 

1. Fare clic sulla scheda **Users** (Utenti) e quindi fare clic su **Add New User** (Aggiungi nuovo utente). 
   
    ![Creazione di un utente test CloudPassage][23]

1. Nella sezione **Aggiungi nuovo utente** eseguire la procedura seguente: 
   
    ![Creazione di un utente test CloudPassage][24]
    
    a. Nella casella di testo **Nome** digitare Britta. 
  
    b. Nella casella di testo **Cognome** digitare Simon.
  
    c. Nelle caselle di testo **Username** (Nome utente), **Email** (Indirizzo di posta elettronica) e **Retype Email** (Digitare nuovamente l'indirizzo di posta elettronica) digitare il nome utente di Britta in Azure AD.
  
    d. In **Access Type** (Tipo di accesso) selezionare **Enable Halo Portal Access** (Abilita accesso portale Halo).
  
    e. Scegliere **Aggiungi**.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di CloudPassage nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione CloudPassage per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare CloudPassage con Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

