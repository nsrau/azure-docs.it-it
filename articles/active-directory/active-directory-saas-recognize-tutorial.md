---
title: 'Esercitazione: Integrazione di Azure Active Directory con Recognize | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Recognize.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 43e9febd4bc230db69b55ae3f5bb13ff17b2e3f6


---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Esercitazione: Integrazione di Azure Active Directory con Recognize
Questa esercitazione descrive l'integrazione di Recognize con Azure Active Directory (Azure AD).

L'integrazione di Recognize con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Recognize
* È possibile abilitare gli utenti per l'accesso automatico a Recognize (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Recognize, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD
* Sottoscrizione di Recognize abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Recognize dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-recognize-from-the-gallery"></a>Aggiunta di Recognize dalla raccolta
Per configurare l'integrazione di Recognize in Azure AD, è necessario aggiungere Recognize dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Recognize dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Recognize**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)
7. Nel riquadro dei risultati selezionare **Recognize** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Recognize in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Recognize che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Recognize.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Recognize.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Recognize, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Recognize](#creating-a-recognize-test-user)** : per avere una controparte di Britta Simon in Recognize collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Recognize.

**Per configurare Single Sign-On di Azure AD con Recognize, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Recognize** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Recognize** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)
   
    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://recognizeapp.com/<your-domain>/saml/sso`.
   
    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://recognizeapp.com/<your-domain>/saml/metadata`.
   
    c. Fare clic su **Avanti**
   
   > [!NOTE]
   > Se non si conoscono questi URL, digitare gli URL di esempio con il modello di esempio. Per ottenere questi valori, fare riferimento ai dettagli descritti nel passaggio 9 oppure contattare il team di supporto di Recognize <mailto:support@recognizeapp.com>.
   > 
   > 
4. Nella pagina **Configura accesso Single Sign-On in Recognize** fare clic su **Scarica certificato** e quindi salvare il file nel computer:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)
5. In un'altra finestra del browser Web accedere al tenant Recognize come amministratore.
6. In alto a destra fare clic su **Menu**. Passare a **Company Admin** (Amministrazione società).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)
7. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)
8. Nella sezione **SSO Settings** (Impostazioni SSO) seguire questa procedura.
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
   
    a. In **Enable SSO** (Abilita SSO) selezionare **ON**.
   
    b. Nella casella di testo **IDP Entity ID** (ID entità IDP) inserire il valore di **URL autorità di certificazione** dalla configurazione guidata dell'applicazione di Azure AD.
   
    c. Nella casella di testo **Sso target url** (URL di destinazione Single Sign-On) inserire il valore di **URL servizio Single Sign-On** dalla configurazione guidata dell'applicazione di Azure AD.
   
    d. Nella casella di testo **Sso target url** (URL di destinazione Single Sign-Out) inserire il valore di **URL servizio Single Sign-Out** dalla configurazione guidata dell'applicazione di Azure AD.
   
    e. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato**. 
   
    f. Fare clic sul pulsante **Save settings** (Salva impostazioni). 
9. Accanto alla sezione **SSO Settings** (Impostazioni Single Sign-On) copiare l'URL in **Service Provider Metadata url** (URL metadati del provider di servizio).
   
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)
10. In una pagina del browser fare clic sul collegamento **Metadata URL** (URL metadati) per scaricare il documento di metadati. Usare quindi il valore di EntityDescriptor fornito da Recognize per l'**identificatore** nella finestra di dialogo **Configurare le impostazioni dell'app**.
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)
11. Nel portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Avanti**.
    
    ![Single Sign-On di Microsoft Azure AD][10]
12. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente di test chiamato Britta Simon nel portale classico.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)
   
    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png)
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png)
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
   
    b. Fare clic su **Complete**.   

### <a name="creating-a-recognize-test-user"></a>Creazione di un utente test di Recognize
Per consentire agli utenti di Azure AD di accedere a Recognize, è necessario eseguirne il provisioning in Recognize. Nel caso di Recognize, il provisioning è un'attività manuale.

Questa app non supporta il provisioning SCIM, ma dispone di una sincronizzazione utente alternativa che esegue il provisioning degli utenti. 

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:
1. Accedere al sito aziendale di Recognize come amministratore.
2. In alto a destra fare clic su **Menu**. Passare a **Company Admin** (Amministrazione società).
3. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).
4. Nella sezione **User Sync** (Sincronizzazione utente) seguire questa procedura.
   
   ![Nuovo utente](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "New User")
   
   a. In **Sync Enabled** (Sincronizzazione abilitata) selezionare **ON**.
   
   b. In **Choose sync provider** (Scegli provider di sincronizzazione) selezionare **Microsoft/Office 365**.
   
   c. Fare clic su **Run User Sync** (Esegui sincronizzazione utente).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
L'obiettivo di questa sezione è abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Recognize.

![Assegna utente][200]

**Per assegnare Britta Simon a Recognize, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201]
2. Nell'elenco di applicazioni selezionare **Recognize**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Recognize nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Recognize.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


