---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cezanne HR Software | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cezanne HR Software.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con Cezanne HR Software
Questa esercitazione descrive l'integrazione di Cezanne HR Software con Azure Active Directory (Azure AD).

L'integrazione di Cezanne HR Software con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Cezanne HR Software.
* È possibile abilitare gli utenti per l'accesso automatico a Cezanne HR Software (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisiti
Per configurare l'integrazione di Azure AD con Cezanne HR Software, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Cezanne HR Software abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Cezanne HR Software dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## Aggiunta di Cezanne HR Software dalla raccolta
Per configurare l'integrazione di Cezanne HR Software in Azure AD, è necessario aggiungere Cezanne HR Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Cezanne HR Software dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Active Directory][1] 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni][2] 
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3] 
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4] 
6. Nella casella di ricerca, digitare **Cezanne HR Software**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png) 
7. Nel riquadro dei risultati selezionare **Cezanne HR Software** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png) 

## Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Cezanne HR Software in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Cezanne HR Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cezanne HR Software.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Cezanne HR Software.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cezanne HR Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Cezanne HR Software](#creating-a-cezanne-hr-software-test-user)**: per avere una controparte di Britta Simon in Cezanne HR Software collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione nel portale classico viene abilitato l'accesso Single Sign-On di Azure AD, che viene configurato nell'applicazione Cezanne HR Software.

**Per configurare Single Sign-On di Azure AD con Cezanne HR Software, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Cezanne HR Software** del portale classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Cezanne HR Software** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png) 
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png) 
   
    a. Nella casella di testo **URL accesso** digitare un URL corrispondente al modello seguente: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.
   
    b. Nella casella di testo **Identificatore** digitare: `https://w3.cezanneondemand.com/CezanneOnDemand/`.
   
    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.
   
    d. Fare clic su **Avanti**
   
   > [!NOTE]
   > È necessario aggiornare questi valori con l'URL di accesso, l'ID e l'URL di risposta effettivo. Per ottenere questi valori contattare il team di supporto di Cezanne HR Software all'indirizzo <mailto:info@cezannehr.com>.
   > 
   > 
4. Nella pagina **Configura accesso Single Sign-On in Cezanne HR Software** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png) 
   
    a. Fare clic su **Scarica certificato** e quindi salvare il file nel computer.
   
    b. Fare clic su **Next**.
5. In un'altra finestra del browser Web accedere al tenant Cezanne HR Software come amministratore.
6. Nella barra di spostamento a sinistra fare clic su **System Setup** (Configurazione di sistema). Passare a **Security Settings** (Impostazioni di sicurezza). Passare quindi a **Single Sign-On Configuration** (Configurazione Single Sign-On).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png) 
7. Nel riquadro **Allow users to log in using the following Single Sign-On (SSO) Service** (Consenti agli utenti di accedere usando il servizio Single Sign-On (SSO) seguente) selezionare la casella **SAML 2.0** e quindi selezionare l'opzione **Advanced Configuration** (Configurazione avanzata) accanto ad essa.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png) 
8. Fare clic sul pulsante **Add New** (Aggiungi nuovo).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png) 
9. Seguire questa procedura nella sezione **SAML 2.0 IDENTITY PROVIDERS** (PROVIDER DI IDENTITÀ SAML 2.0).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png) 
   
    a. Immettere il nome del provider di identità in **Display Name** (Nome visualizzato).
   
    b. Nella casella di testo **Entity Identifier** (Identificatore entità) inserire il valore di **ID entità** dalla configurazione guidata dell'applicazione di Azure AD.
   
    c. Modificare il valore **SAML Binding** (Associazione SAML) in 'POST'.
   
    d. Nella casella di testo **Security Token Service Endpoint** (Endpoint del servizio token di sicurezza) inserire il valore di **URL servizio Single Sign-On** dalla configurazione guidata dell'applicazione di Azure AD.
   
    e. Immettere 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' in **User ID Attribute Name** (Nome attributo ID utente).
   
    f. Fare clic sull'icona **Upload** (Carica) per caricare il certificato scaricato da Azure AD.
   
    g. Fare clic sul pulsante **OK**.
10. Fare clic sul pulsante **Salva**.
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png) 
11. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
    
    ![Accesso Single Sign-On di Azure AD][10] 
12. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.
    
    ![Accesso Single Sign-On di Azure AD][11] 

### Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20] 

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti** nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 
5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Next**.
6. Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png) 
   
   a. Nella casella di testo **Nome** digitare **Britta**.
   
   b. Nella casella di testo **Last Name** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo**, selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png) 
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.

### Creazione di un utente test di Cezanne HR Software
Per consentire agli utenti di Azure AD di accedere a Cezanne HR Software, è necessario eseguirne il provisioning in Cezanne HR Software. Nel caso di Cezanne HR Software, il provisioning è un'attività manuale.

#### Per eseguire il provisioning di un account utente, seguire questa procedura:
1. Accedere al sito aziendale di Cezanne HR Software come amministratore.
2. Nella barra di spostamento a sinistra fare clic su **System Setup** (Configurazione di sistema). Passare a **Gestisci utenti**. Passare quindi ad **Add New User** (Aggiungi nuovo utente).
   
   ![New User](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "New User") 
3. Nella sezione **Person Details** (Dettagli persona) seguire questa procedura:
   
   ![New User](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "New User") 
   
   a. Impostare **Internal User** (Utente interno) su OFF.
   
   b. Nella casella di testo **First Name** digitare **Britta**.
   
   c. Nella casella di testo **Last Name** digitare **Simon**.
   
   d. Nella casella di testo **E-mail** digitare l'indirizzo di posta elettronica dell'account di Britta Simon.
4. Nella sezione **Account Information** (Informazioni account) seguire questa procedura:
   
   ![New User](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "New User") 
   
   a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di Britta Simon.
   
   b. Nella casella di testo **Password** digitare la password dell'account di Britta Simon.
   
   c. Selezionare **HR Professional** (Professionista HR) come **Security Role** (Ruolo di sicurezza).
   
   d. Fare clic su **OK**.
5. Passare alla scheda **Single Sign-On** e selezionare **Add New** (Aggiungi nuovo) nell'area **SAML 2.0 Identifiers** (Identificatori SAML 2.0).
   
    ![Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utente") 
6. Scegliere il provider di identità per **Identity Provider** (Provider di identità) e nella casella di testo **User Identifier** (Identificatore utente) immettere l'indirizzo di posta elettronica dell'account Britta Simon.
   
    ![Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utente") 
7. Fare clic sul pulsante **Salva**.
   
    ![Utente](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utente") 

### Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cezanne HR Software.

![Assegna utente][200] 

**Per assegnare Britta Simon a Cezanne HR Software, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione Directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco di applicazioni selezionare **Cezanne HR Software**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png) 
3. Scegliere **Utenti** dal menu in alto.
   
    ![Assegna utente][203] 
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna** sulla barra degli strumenti in basso.
   
    ![Assegna utente][205] 

### Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cezanne HR Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cezanne HR Software.

## Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references--> 

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->