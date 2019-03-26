---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Creative Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35bd52904ab081e41cb43a346288234c18a7f43b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904049"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Creative Cloud

Questa esercitazione descrive come integrare Adobe Creative Cloud con Azure Active Directory (Azure AD).
L'integrazione di Adobe Creative Cloud con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad Adobe Creative Cloud.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad Adobe Creative Cloud con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Adobe Creative Cloud, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Adobe Creative Cloud abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Adobe Creative Cloud supporta l'accesso SSO avviato da **SP**

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Aggiungere Adobe Creative Cloud dalla raccolta

Per configurare l'integrazione di Adobe Creative Cloud in Azure AD, è necessario aggiungere Adobe Creative Cloud dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Adobe Creative Cloud dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Adobe Creative Cloud** selezionare **Adobe Creative Cloud** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Adobe Creative Cloud nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adobe Creative Cloud usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Creative Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adobe Creative Cloud, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Adobe Creative Cloud](#configure-adobe-creative-cloud-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)**: per avere una controparte di Britta Simon in Adobe Creative Cloud collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Adobe Creative Cloud, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Adobe Creative Cloud** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Adobe Creative Cloud](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare il valore nel formato: `https://adobe.com`.

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Il valore dell'identificatore non è reale. è necessario aggiornare questo valore con l'ID effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. L'applicazione Adobe Creative Cloud prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![image](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | Nome | Attributo di origine|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail

    a. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Per Origine selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    f. Fare clic su **Ok**

    g. Fare clic su **Save**.

    > [!NOTE]
    > Gli utenti devono disporre di una licenza di Office 365 ExO valida per il valore di attestazione della posta elettronica da inserire nella risposta SAML.

7. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

8. Nella sezione **Configura Adobe Creative Cloud** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Configurare l'accesso Single Sign-On di Adobe Creative Cloud

1. In un'altra finestra del Web browser accedere ad [Adobe Admin Console](https://adminconsole.adobe.com) come amministratore.

2. Passare a **Impostazioni** nella barra di spostamento superiore e quindi scegliere **Identità**. Viene aperto l'elenco dei domini. Fare clic sul collegamento **Configura** per il proprio dominio. Eseguire i passaggi descritti di seguito nella sezione di **configurazione dell'accesso Single Sign-On**. Per altre informazioni, vedere [Setup a domain](https://helpx.adobe.com/enterprise/using/set-up-domain.html) (Configurare un dominio).

    ![Impostazioni](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Impostazioni")

    a. Fare clic su **Browse** (Sfoglia) per caricare il certificato scaricato da Azure AD in **IDP Certificate** (Certificato IDP).

    b. Nella casella di testo **IDP issuer** (Autorità di certificazione IDP) inserire il valore dell'**ID entità SAML** copiato in precedenza dalla sezione **Configura accesso** del portale di Azure.

    c. Nella casella di testo **IDP Login URL** (URL di accesso IDP) inserire il valore dell'**URL del servizio SSO SAML** copiato in precedenza dalla sezione **Configura accesso** del portale di Azure.

    d. Selezionare **HTTP - Redirect** (HTTP - Reindirizzamento) per **IDP Binding** (Binding IDP).

    e. Selezionare **Email Address** (Indirizzo di posta elettronica) per **User Login Setting** (Impostazione accesso utente).

    f. Fare clic sul pulsante **Salva** .

3. Il dashboard visualizza ora il file XML **"Download Metadata"**, che contiene l'URL EntityDescriptor e l'URL AssertionConsumerService di Adobe. Aprire il file e configurare gli URL nell'applicazione Azure AD.

    ![Configurazione accesso Single Sign-On sul lato app](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Usare il valore di EntityDescriptor indicato da Adobe per l'**identificatore** nella finestra di dialogo **Configurare le impostazioni dell'app**.

    b. Usare il valore di AssertionConsumerService specificato da Adobe per **URL di risposta** nella finestra di dialogo **Configurare le impostazioni dell'app**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Adobe Creative Cloud.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Adobe Creative Cloud**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Adobe Creative Cloud**.

    ![Il collegamento ad Adobe Creative Cloud nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-adobe-creative-cloud-test-user"></a>Creare l'utente di test di Adobe Creative Cloud

Per consentire agli utenti di Azure AD di accedere ad Adobe Creative Cloud, è necessario eseguirne il provisioning in Adobe Creative Cloud. Nel caso di Adobe Creative Cloud il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito [Adobe Admin Console](https://adminconsole.adobe.com) come amministratore.

2. Aggiungere l'utente nella console di Adobe come ID federato e assegnarlo a un profilo di prodotto. Per informazioni dettagliate sull'aggiunta di utenti, vedere [Manage Users](https://helpx.adobe.com/enterprise/using/users.html#Addusers) (Gestire gli utenti). 

3. A questo punto, digitare l'indirizzo di posta elettronica/upn nel modulo di registrazione, premere sulla scheda e si dovrebbe essere federati di nuovo in Azure AD:
   * Accesso Web: www\.adobe.com > sign-in (accedi)
   * All'interno dell'utilità dell'app del desktop > sign-in (accedi)
   * All'interno dell'applicazione > help (guida) > sign-in (accedi)

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Adobe Creative Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adobe Creative Cloud per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Set up a domain (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html) (Configurare un dominio)
  
- [Configure Azure for use with Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html) (Configurare Azure per l'uso con Adobe SSO)
