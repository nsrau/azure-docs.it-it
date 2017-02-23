---
title: 'Esercitazione: Integrazione di Azure Active Directory con ServiceNow | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e ServiceNow e ServiceNow Express.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: d134b91131321f633471893d9c2d5ffa041aa99c


---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Esercitazione: Integrazione di Azure Active Directory con ServiceNow
Questa esercitazione descrive come integrare ServiceNow e ServiceNow Express con Azure Active Directory (Azure AD).

L'integrazione di ServiceNow e ServiceNow Express con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a ServiceNow e ServiceNow Express
* È possibile abilitare gli utenti per l'accesso automatico a ServiceNow e ServiceNow Express (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con ServiceNow e ServiceNow Express, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Per ServiceNow, un'istanza o un tenant di ServiceNow, versione Calgary o successiva
* Per ServiceNow Express, un'istanza di ServiceNow Express, versione Helsinki o successiva
* Nel tenant di ServiceNow deve essere abilitato il [plug-in Multiple Provider Single Sign On](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0). Questa operazione può essere eseguita [inviando una richiesta di servizio](https://hi.service-now.com). 

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di ServiceNow dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD per ServiceNow o ServiceNow Express

## <a name="adding-servicenow-from-the-gallery"></a>Aggiunta di ServiceNow dalla raccolta
Per configurare l'integrazione di ServiceNow o ServiceNow Express in Azure AD, è necessario aggiungere ServiceNow dalla raccolta al proprio elenco di app SaaS gestite. 

**Per aggiungere ServiceNow dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **ServiceNow**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. Nel riquadro dei risultati selezionare **ServiceNow** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ServiceNow o ServiceNow Express in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di ServiceNow e che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ServiceNow.
La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Username** (Nome utente) in ServiceNow. Per configurare e testare l'accesso Single Sign-On di Azure AD con ServiceNow, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On per ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Configurazione dell'accesso Single Sign-On per ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)**: per abilitare gli utenti all'uso di questa funzionalità.
3. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Creazione di un utente test per ServiceNow](#creating-a-servicenow-test-user)**: per avere una controparte di Britta Simon in ServiceNow collegata alla relativa rappresentazione in Azure AD.
5. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
6. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

> [!NOTE]
> Se si vuole configurare ServiceNow, ignorare il passaggio 2. Se si vuole configurare ServiceNow Express, ignorare il passaggio 1.
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configurazione dell'accesso Single Sign-On per ServiceNow
1. Nella pagina di integrazione dell'applicazione **ServiceNow** del portale di Azure AD classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a ServiceNow** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurare l'URL dell'app")
   
    a. Nella casella di testo **URL di accesso ServiceNow** digitare l'URL usato dagli utenti per accedere all'applicazione ServiceNow, seguendo il modello `https://<instance-name>.service-now.com`.
   
    b. Nella casella di testo **Identificatore** digitare l'URL usato dagli utenti per accedere all'applicazione ServiceNow, seguendo il modello `https://<instance-name>.service-now.com`.
   
    c. Fare clic su **Avanti**

4. Per fare in modo che Azure AD configuri automaticamente ServiceNow per l'autenticazione basata su SAML, immettere il nome dell'istanza ServiceNow, il nome utente amministratore e la password di amministratore nel modulo **Configura automaticamente l'accesso Single Sign-On** e fare clic su *Configura*. Affinché la procedura funzioni, il nome utente con diritti amministrativi fornito deve avere il ruolo **security_admin** assegnato in ServiceNow. In caso contrario, per configurare manualmente ServiceNow per usare Azure AD come provider di identità SAML, fare clic su **Configura manualmente questa applicazione per l'accesso Single Sign-On**, quindi fare clic su **Avanti** e completare la procedura seguente.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurare l'URL dell'app")

5. Nella pagina **Configura accesso Single Sign-On in ServiceNow** fare clic su **Download certificato** per scaricare il file del certificato e salvarlo nel computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurare l'accesso Single Sign-On")

6. Accedere all'applicazione ServiceNow come amministratore.

7. Attivare il plug-in *Integration - Multiple Provider Single Sign-On Installer* seguendo questa procedura:
   
    a. Nel riquadro di spostamento a sinistra, passare alla sezione **definizione sistema** e quindi fare clic su **Plugins** (Plug-in).
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Attivare il plug-in")
   
    b. Cercare *Integration - Multiple Provider Single Sign-On Installer*.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Attivare il plug-in")
   
    c. Selezionare il plug-in. Fare clic e selezionare **Activate/Upgrade** (Attiva/Aggiorna).
   
    d. Fare clic sul pulsante **Activate** (Attiva).

8. Nel riquadro di spostamento a sinistra fare clic su **Properties**.  
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurare l'URL dell'app")

9. Nella finestra di dialogo **Multiple Provider SSO Properties** seguire questa procedura:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurare l'URL dell'app")
   
    a. Per **Enable multiple provider SSO** selezionare **Yes**.
   
    b. Per **Enable debug logging got the multiple provider SSO integration** selezionare **Yes**.
   
    c. Nella casella di testo **The field on the user table that...** digitare **user_name**.
   
    d. Fare clic su **Save**.

10. Nel riquadro di spostamento a sinistra fare clic su **x509 Certificates**.
    
     ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurare l'accesso Single Sign-On")

11. Nella finestra di dialogo **X.509 Certificates** fare clic su **New**.
    
     ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurare l'accesso Single Sign-On")

12. Nella finestra di dialogo **X.509 Certificates** seguire questa procedura:
    
     ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurare l'accesso Single Sign-On")
    
     a. Fare clic su **New**.
    
     b. Nella casella di testo **Name** digitare un nome per la configurazione, ad esempio **TestSAML2.0**.
    
     c. Selezionare **Active**.
    
     d. Per **Format** selezionare **PEM**.
    
     e. Per **Type** selezionare **Trust Store Cert**.
    
     f. Aprire il certificato con codifica Base64 in Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **PEM Certificate**.
    
     g. Fare clic su **Update**.

13. Nel riquadro di spostamento a sinistra fare clic su **Identity Providers**.
    
     ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurare l'accesso Single Sign-On")

14. Nella finestra di dialogo **Identity Providers** fare clic su **New**:
    
     ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurare l'accesso Single Sign-On")

15. Nella finestra di dialogo **Identity Providers** fare clic su **SAML2 Update1?**:
    
     ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurare l'accesso Single Sign-On")

16. Nella finestra di dialogo SAML2 Update1 Properties seguire questa procedura:
    
     ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurare l'accesso Single Sign-On")

    a. Nella casella di testo **Nome** digitare un nome per la configurazione (ad esempio **SAML 2.0**).

    b. Nella casella di testo **User Field** (Campo utente) digitare **email** o **user_id**, a seconda di quale campo viene usato per identificare in modo univoco gli utenti nella distribuzione ServiceNow. 

    > [!NOTE] 
    > Per configurare Azure AD per generare l'ID utente di Azure AD, ovvero il nome dell'entità utente, o l'indirizzo di posta elettronica come identificatore univoco nel token SAML, passare alla sezione **ServiceNow > Attributi > Single Sign-On** del portale di Azure classico ed eseguire il mapping del campo scelto all'attributo **nameidentifier**. Il valore archiviato per l'attributo selezionato in Azure AD (ad esempio nome dell'entità utente) deve corrispondere al valore archiviato in ServiceNow per il campo immesso (ad esempio user_id)

    c. Nel portale di Azure AD classico copiare il valore in **ID provider di identità** e incollarlo nella casella di testo **Identity Provider URL** (ID provider di identità).

    d. Nel portale di Azure AD classico copiare il valore in **URL richiesta di autenticazione** e incollarlo nella casella di testo **Identity Provider's AuthnRequest** (Richiesta di autenticazione provider di identità).

    e. Nel portale di Azure AD classico copiare il valore in **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Identity Provider's SingleLogoutRequest** (Richiesta Single Sign-Out del provider di identità).

    f. Nella casella di testo **ServiceNow Homepage** digitare l'URL della home page dell'istanza di ServiceNow.

    > [!NOTE] 
    > L'URL della home page dell'istanza di ServiceNow è una concatenazione dell'**URL del tenant di ServiceNow** e **/navpage.do**, ad esempio `https://fabrikam.service-now.com/navpage.do`.

    g. Nella casella di testo **Entity ID / Issuer** digitare l'URL del tenant ServiceNow.

    h. Nella casella di testo **Audience URL** digitare l'URL del tenant ServiceNow. 

    i. Nella casella di testo **Protocol Binding for the IDP's SingleLogoutRequest** digitare **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. Nella casella di testo NameID Policy digitare **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Fare clic su **Create an AuthnContextClass**.

    l. In **AuthnContextClassRef Method** (Metodo AuthnContextClassRef) digitare `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Questa operazione è necessaria soltanto per le organizzazioni basate solo su cloud. Se si usa AD FS o MFA in locale per l'autenticazione, non è necessario configurare questo valore. 

    m. Nella casella di testo **Clock Skew** digitare **60**.

    n. Per **Single Sign On Script** selezionare **MultiSSO_SAML2_Update1**.

    o. Per **x509 Certificate**selezionare il certificato creato nel passaggio precedente.

    p. Fare clic su **Submit**. 

1. Nel portale di Azure AD classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. 
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurare l'accesso Single Sign-On")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configurazione dell'accesso Single Sign-On per ServiceNow Express
1. Nella pagina di integrazione dell'applicazione **ServiceNow** del portale di Azure AD classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a ServiceNow** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurare l'URL dell'app")
   
    a. Nella casella di testo **URL di accesso ServiceNow** digitare l'URL usato dagli utenti per accedere all'applicazione ServiceNow, seguendo il modello `https://<instance-name>.service-now.com`.
   
    b. Nella casella di testo **URL autorità di certificazione** digitare l'URL usato dagli utenti per accedere all'applicazione ServiceNow, seguendo il modello `https://<instance-name>.service-now.com`.
   
    c. Fare clic su **Avanti**

4. Fare clic su **Configura manualmente questa applicazione per l'accesso Single Sign-On**, quindi fare clic su **Avanti** e seguire questa procedura.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurare l'URL dell'app")

5. Nella pagina **Configura accesso Single Sign-On in ServiceNow** fare clic su **Download certificato** per scaricare il file del certificato e salvarlo nel computer e quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurare l'accesso Single Sign-On")

6. Accedere all'applicazione ServiceNow Express come amministratore.

7. Nel riquadro di spostamento a sinistra fare clic su **Single Sign-On**.  
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurare l'URL dell'app")

8. Nella finestra di dialogo **Single Sign-On** fare clic sull'icona di configurazione nell'angolo superiore destro e impostare le proprietà seguenti:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurare l'URL dell'app")
   
    a. Spostare **Enable multiple provider SSO** (Abilita SSO per più provider) verso destra.
   
    b. Spostare **Enable debug logging for the multiple provider SSO integration** (Abilita la registrazione debug per l'integrazione SSO per più provider) verso destra.
   
    c. Nella casella di testo **The field on the user table that...** digitare **user_name**.
9. Nella finestra di dialogo **Single Sign-On** fare clic su **Add New Certificate** (Aggiungi nuovo certificato).
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurare l'accesso Single Sign-On")
10. Nella finestra di dialogo **X.509 Certificates** seguire questa procedura:
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurare l'accesso Single Sign-On")
    
    a. Nella casella di testo **Name** digitare un nome per la configurazione, ad esempio **TestSAML2.0**.
    
    b. Selezionare **Active**.
    
    c. Per **Format** selezionare **PEM**.
    
    d. Per **Type** selezionare **Trust Store Cert**.
    
    e. Creare un file con codifica Base64 dal certificato scaricato.
    
    > [!NOTE]
    > Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).
    > 
    > 
    
    f. Aprire il certificato con codifica Base64 in Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **PEM Certificate**.
    
    g. Fare clic su **Update**.
11. Nella finestra di dialogo **Single Sign-On** fare clic su **Add New IdP** (Aggiungi nuovo IdP).
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurare l'accesso Single Sign-On")
12. Nella finestra di dialogo **Add New Identity Provider** (Aggiungi nuovo provider di identità) in **Configure Identity Provider** (Configura provider di identità) seguire questa procedura:
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurare l'accesso Single Sign-On")

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione, ad esempio **SAML 2.0**.

    b. Nel portale di Azure AD classico copiare il valore in **ID provider di identità** e incollarlo nella casella di testo **Identity Provider URL** (ID provider di identità).

    c. Nel portale di Azure AD classico copiare il valore in **URL richiesta di autenticazione** e incollarlo nella casella di testo **Identity Provider's AuthnRequest** (Richiesta di autenticazione provider di identità).

    d. Nel portale di Azure AD classico copiare il valore in **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Identity Provider's SingleLogoutRequest** (Richiesta Single Sign-Out del provider di identità).

    e. Per **Identity Provider Certificate** (Certificato del provider di identità) selezionare il certificato creato nel passaggio precedente.


1. Fare clic su **Advanced Settings** (Impostazioni avanzate). In **Additional Identity Provider Properties** (Proprietà del provider di identità aggiuntivo) seguire questa procedura:
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurare l'accesso Single Sign-On")
   
    a. Nella casella di testo **Protocol Binding for the IDP's SingleLogoutRequest** digitare **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.
   
    b. Nella casella di testo **NameID Policy** (Criteri NameID) digitare **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.    
   
    c. Nel **metodo AuthnContextClassRef** digitare **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
   
    d. Fare clic su **Create an AuthnContextClass**.

2. In **Additional Service Provider Properties** (Proprietà del provider di identità aggiuntivo) seguire questa procedura:
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurare l'accesso Single Sign-On")
   
    a. Nella casella di testo **ServiceNow Homepage** digitare l'URL della home page dell'istanza di ServiceNow.
   
    > [!NOTE]
    > L'URL della home page dell'istanza di ServiceNow è una concatenazione dell'**URL del tenant di ServiceNow** e **/navpage.do**, ad esempio `https://fabrikam.service-now.com/navpage.do`.
    > 
    > 
   
    b. Nella casella di testo **Entity ID / Issuer** digitare l'URL del tenant ServiceNow.
   
    c. Nella casella di testo **Audience URI** (URI destinatario) digitare l'URL del tenant ServiceNow. 
   
    d. Nella casella di testo **Clock Skew** digitare **60**.
   
    e. Nella casella di testo **User Field** (Campo utente) digitare **email** o **user_id**, a seconda di quale campo viene usato per identificare in modo univoco gli utenti nella distribuzione ServiceNow.
   
    > [!NOTE]
    > Per configurare Azure AD per generare l'ID utente di Azure AD, ovvero il nome dell'entità utente, o l'indirizzo di posta elettronica come identificatore univoco nel token SAML, passare alla sezione **ServiceNow > Attributi > Single Sign-On** del portale di Azure classico ed eseguire il mapping del campo scelto all'attributo **nameidentifier**. Il valore archiviato per l'attributo selezionato in Azure AD (ad esempio nome dell'entità utente) deve corrispondere al valore archiviato in ServiceNow per il campo immesso (ad esempio user_id)
    > 
    > 
   
    f. Fare clic su **Save**. 

3. Nel portale di Azure AD classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**. 
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurare l'accesso Single Sign-On")

4. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurare l'accesso Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Questa sezione descrive come abilitare il provisioning degli account utente di Azure Active Directory in ServiceNow.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Nella pagina di integrazione dell'applicazione **ServiceNow** del portale di gestione di Azure classico fare clic su **Configura provisioning utenti**. 
   
    ![Provisioning degli utenti](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Provisioning degli utenti")

2. Nella pagina **Enter your ServiceNow credentials to enable automatic user provisioning** (Immettere le credenziali Jive per abilitare il provisioning utenti automatico) specificare le impostazioni di configurazione seguenti:
   
     a. Nella casella di testo **Nome istanza ServiceNow** digitare il nome dell'istanza di ServiceNow.
   
     b. Nella casella di testo **Nome utente amministratore ServiceNow** digitare il nome dell'account amministratore ServiceNow.
   
     c. Nella casella di testo **Password amministratore ServiceNow** digitare la password dell'account.
   
     d. Fare clic su **Convalida** per verificare la configurazione.
   
     e. Fare clic sul pulsante **Avanti** per aprire la pagina **Passaggi successivi**.
   
     f. Se si vuole eseguire il provisioning di tutti gli utenti nell'applicazione, selezionare "**Esegui automaticamente il provisioning di tutti gli account utente della directory in questa applicazione**". 
   
    ![Passaggi successivi](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Passaggi successivi")
   
     g. Nella pagina **Passaggi successivi** fare clic su **Completa** per salvare la configurazione.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.

6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-servicenow-test-user"></a>Creazione di un utente test di ServiceNow
In questa sezione viene creato un utente di nome Britta Simon in ServiceNow. In questa sezione viene creato un utente di nome Britta Simon in ServiceNow. Se non si sa come aggiungere un utente all'account ServiceNow o ServiceNow Express, contattare il team di supporto di ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ServiceNow.

![Assegna utente][200] 

**Per assegnare Britta Simon a ServiceNow, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 

2. Nell'elenco di applicazioni, selezionare **ServiceNow**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203] 

4. Nell'elenco Tutti gli utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ServiceNow nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ServiceNow.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


