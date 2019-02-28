---
title: 'Esercitazione: Integrazione di Azure Active Directory con CloudPassage | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45d5e0cfe39436b9af3a2b671a194bce88b91720
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883488"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Esercitazione: Integrazione di Azure Active Directory con CloudPassage

Questa esercitazione descrive come integrare CloudPassage con Azure Active Directory (Azure AD).
L'integrazione di CloudPassage con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a CloudPassage.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a CloudPassage con gli account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con CloudPassage, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di CloudPassage abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* CloudPassage supporta l'accesso SSO avviato da **SP**

## <a name="adding-cloudpassage-from-the-gallery"></a>Aggiunta di CloudPassage dalla raccolta

Per configurare l'integrazione di CloudPassage in Azure AD, è necessario aggiungere CloudPassage dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere CloudPassage dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **CloudPassage**, selezionare **CloudPassage** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![CloudPassage nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con CloudPassage usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in CloudPassage.

Per configurare e testare l'accesso Single Sign-On di Azure AD con CloudPassage, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per CloudPassage](#configure-cloudpassage-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di CloudPassage](#create-cloudpassage-test-user)**: per avere una controparte di Britta Simon in CloudPassage collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con CloudPassage, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **CloudPassage** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di CloudPassage](common/sp-reply.png)

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://portal.cloudpassage.com/saml/init/accountid`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://portal.cloudpassage.com/saml/consume/accountid`. È possibile ottenere il valore di questo attributo facendo clic su **SSO Setup documentation** (Documentazione della configurazione SSO) nella sezione **Single Sign-on Settings** (Impostazioni Single Sign-On) del portale di CloudPassage.

    ![Configure Single Sign-On](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto clienti di CloudPassage](https://www.cloudpassage.com/company/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione CloudPassage prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** modificare le attestazioni usando l'**icona Modifica** o aggiungere le attestazioni usando l'opzione **Aggiungi nuova attestazione** per configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura: 

    | NOME | Source Attribute|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Set up CloudPassage** (Configura CloudPassage) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-cloudpassage-single-sign-on"></a>Configurare l'accesso Single Sign-On per CloudPassage

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
  
    f. Fare clic su **Save**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a CloudPassage.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **CloudPassage**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **CloudPassage**.

    ![Collegamento di CloudPassage nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-cloudpassage-test-user"></a>Creare l'utente di test di CloudPassage

Questa sezione descrive come creare un utente chiamato Britta Simon in CloudPassage.

**Per creare un utente test denominato Britta Simon in CloudPassage, seguire questa procedura:**

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
  
    e. Fare clic su **Aggiungi**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di CloudPassage nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione CloudPassage per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

