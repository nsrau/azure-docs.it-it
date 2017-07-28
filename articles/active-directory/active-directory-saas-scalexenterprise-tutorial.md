---
title: 'Esercitazione: Integrazione di Azure Active Directory con ScaleX Enterprise | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 0ebed0c2605862426384c0e219e52c9d626b6246
ms.contentlocale: it-it
ms.lasthandoff: 05/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con ScaleX Enterprise

Questa esercitazione descrive come integrare ScaleX Enterprise con Azure Active Directory (Azure AD).

L'integrazione di ScaleX Enterprise con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a ScaleX Enterprise
- È possibile abilitare gli utenti per l'accesso automatico a ScaleX Enterprise (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure Active Directory, vedere: Informazioni sull'accesso alle applicazioni e Single Sign-On con [Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ScaleX Enterprise, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di ScaleX Enterprise abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di ScaleX Enterprise dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Aggiunta di ScaleX Enterprise dalla raccolta
Per configurare l'integrazione di ScaleX Enterprise in Azure AD, è necessario aggiungere ScaleX Enterprise dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere ScaleX Enterprise dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **ScaleX Enterprise**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. Nel pannello dei risultati selezionare **ScaleX Enterprise** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ScaleX Enterprise con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di ScaleX Enterprise che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ScaleX Enterprise.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **nome utente** in ScaleX Enterprise.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ScaleX Enterprise, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)**: per avere una controparte di Britta Simon in ScaleX Enterprise collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ScaleX Enterprise.

**Per configurare l'accesso Single Sign-On di Azure AD con ScaleX Enterprise, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **ScaleX Enterprise** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. Nella sezione **URL e dominio ScaleX Enterprise**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. Nella casella di testo **Identificatore** digitare il valore adottando il modello seguente: `https://platform.rescale.com/saml2/<company id>/`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://platform.rescale.com/saml2/<company id>/acs/`

4. Selezionare **Mostra impostazioni URL avanzate**, se si desidera configurare l'applicazione in modalità avviata da **SP**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Questi non sono i valori reali. Aggiornare questi valori con l'identificatore, l'URL di risposta o l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ScaleX Enterprise](http://info.rescale.com/contact_sales). 

5. L'applicazione ScaleX prevede un formato specifico per le asserzioni SAML. È quindi necessario modificare mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Fare clic sulla casella di controllo **Visualizza e modifica tutti gli altri attributi utente** per aprire le impostazioni degli attributi personalizzati.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Fare clic con il pulsante destro del mouse sul **nome** attributo, quindi fare clic su Elimina.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/delete_attribute_name.png)

    b. Fare clic sull'attributo **emailaddress** per aprire la finestra Modifica attributo. Modificare il relativo valore da **user.mail** a **user.userprincipalname** e fare clic su OK.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/edit_email_attribute.png)    
    
5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_400.png)
    
7. Nella sezione **Configurazione di ScaleX Enterprise** fare clic su **Configura ScaleX Enterprise** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** e l'**URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. Per configurare l'accesso Single Sign-On sul lato **ScaleX Enterprise**, accedere al sito Web aziendale di ScaleX Enterprise come amministratore.

9. Fare clic sul menu in alto a destra e seleziona **Contoso Administration** (Amministrazione Contoso).

    > [!NOTE] 
    > Contoso è solo un esempio. Deve essere il nome effettivo della società. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/Test_Admin.png) 

10. Selezionare **Integrations** (Integrazioni) nel menu superiore e selezionare **Single Sign-On**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/admin_sso.png) 

11. Compilare il modulo come segue:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Selezionare **Create any user who can authenticate with SSO** (Crea un utente qualsiasi che può eseguire l'autenticazione con SSO).

    b. **Service Provider saml** (Provider del servizio SAML): incollare il valore ***urn:oasis:names:tc:SAML:2.0:nameid-format:persistent***

    c. **Name of Identity Provider email field in ACS response** (Nome del campo e-mail del provider d'identità nella risposta ACS): incollare il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identity Provider EntityDescriptor Entity ID** (ID entità EntityDescriptor del provider d'identità): incollare il valore **ID entità SAML** copiato dal portale di Azure.

    e. **Identity Provider SingleSignOnService URL** (URL servizio Single Sign-On del provider d'identità): incollare l'**URL servizio Single Sign-On SAML** dal portale di Azure.

    f. **Identity Provider public X509 certificate** (Certificato X509 pubblico provider d'identità): aprire il certificato X509 scaricato da Azure nel blocco note e incollare i contenuti in questa casella. Verificare che non vi siano interruzioni di riga nella parte centrale dei contenuti del certificato.
    
    g. Selezionare le caselle di controllo seguenti: **Enabled, Encrypt NameID e Sign AuthnRequests** (Abilitato, Esegui crittografia dell'ID nome e Firma richieste di autenticazione).

    h. Fare clic su **Update SSO Settings** (Aggiorna impostazioni SSO) per salvare le impostazioni.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Creazione di un utente test ScaleX Enterprise

Per consentire agli utenti di Azure AD di accedere a ScaleX Enterprise, è necessario eseguirne il provisioning in ScaleX Enterprise. Nel caso di ScaleX Enterprise, il provisioning è un'attività automatica e non sono necessarie procedure manuali. Per tutti gli utenti che eseguono l'autenticazione con le credenziali SSO verrà automaticamente eseguito il provisioning sul lato ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ScaleX Enterprise.

![Assegna utente][200] 

**Per assegnare Britta Simon a ScaleX Enterprise, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **ScaleX Enterprise**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ScaleX Enterprise nel pannello di accesso, si accede automaticamente all'applicazione ScaleX Enterprise. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_203.png


