---
title: 'Esercitazione: Integrazione di Azure Active Directory con Halogen Software'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Halogen Software.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 21d31eb33a838d42b4ba8f4191b89e6094206f6e


---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Esercitazione: Integrazione di Azure Active Directory con Halogen Software
Questa esercitazione descrive l'integrazione di Halogen Software con Azure Active Directory (Azure AD).

L'integrazione di Halogen Software con Azure AD offre i vantaggi seguenti: 

* È possibile controllare in Azure AD chi può accedere a Halogen Software. 
* È possibile abilitare gli utenti per l'accesso automatico a Halogen Software (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Halogen Software, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Halogen Software abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Halogen Software dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-halogen-software-from-the-gallery"></a>Aggiunta di Halogen Software dalla raccolta
Per configurare l'integrazione di Halogen Software in Azure AD, è necessario aggiungere Halogen Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Halogen Software dalla raccolta, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.    
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **halogen software**.
   
    ![Applicazioni][5]
7. Nel riquadro dei risultati selezionare **Halogen Software**, quindi fare clic su **Completa** per aggiungere l'applicazione.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
L'obiettivo di questa sezione è illustrare come configurare e testare l'accesso Single Sign-On di Azure AD con Halogen Software in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Halogen Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Halogen Software.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Halogen Software.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Halogen Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Halogen Software](#creating-a-halogen-software-test-user)** : per avere una controparte di Britta Simon in Halogen Software collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Halogen Software.

**Per configurare Single Sign-On di Azure AD con Halogen Software, eseguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Halogen Software** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][8]
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Halogen Software** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][9]
3. Nella finestra di dialogo **Configurare le impostazioni dell'app** seguire questa procedura:  ![Configurare le impostazioni dell'app][10]
   
     a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Halogen Software usando il modello seguente: *https://global.hgncloud.com/fabrikam/welcome.jsp*
   
     b. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in Halogen Software** fare clic su **Scarica metadati** e quindi salvare il file di metadati in locale nel computer.
   
    ![Cos'è Azure AD Connect][11]
5. In una finestra del browser diversa accedere all'applicazione **Software alogeno** come amministratore.
6. Scegliere la scheda **Options (Opzioni)** . 
   
    ![Cos'è Azure AD Connect][12]
7. Nel riquadro di spostamento a sinistra fare clic su **SAML Configuration**. 
   
    ![Cos'è Azure AD Connect][13]
8. Nella pagina **Configurazione SAML** seguire la procedura seguente:  ![Cos'è Azure AD Connect][14]
   
    a. Come **Identificatore univoco**, selezionare **NameID**.
   
    b. Come **Unique Identifier Maps To** (L'identificatore univoco mappa verso) selezionare **Username** (Nome utente).
   
    c. Per caricare il file di metadati fare clic su **Browse** (Sfoglia) per selezionare il file e quindi fare clic su **Upload File** (Carica file).
   
    d. Per testare la configurazione, fare clic su **Run Test**. 
   
   > [!NOTE]
   > È necessario attendere la visualizzazione del messaggio"*The SAML test is complete. Please close this window*". chiudere quindi la ginestra del browser aperta. La casella di controllo **Enable SAML** (Abilita SAML) è selezionata solo se il test è stato completato.
   > 
   > 
   
    e. Selezionare **Enable SAML**.
   
    f. Fare clic su **Salva modifiche**. 
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**. 
   
    ![Cos'è Azure AD Connect][15]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Cos'è Azure AD Connect][16]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Cos'è Azure AD Connect][100] 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Cos'è Azure AD Connect][101] 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso. 
   
    ![Cos'è Azure AD Connect][102] 
5. Nella pagina della finestra di dialogo **Informazioni sull'utente** seguire questa procedura:
   
    ![Cos'è Azure AD Connect][103] 
   
    a. In **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
   
    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   
    c. Fare clic su Avanti.
6. Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura: 
   
   ![Cos'è Azure AD Connect][104] 
   
   a. Nella casella di testo **Nome** digitare **Britta**.  
   
   b. Nella casella di testo **Cognome** digitare **Simon**.
   
   c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   
   d. Nell'elenco **Ruolo** selezionare **Utente**.
   
   e. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Cos'è Azure AD Connect][105]  
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Cos'è Azure AD Connect][106]   
   
    a. Prendere nota del valore visualizzato in **Nuova password**.
    b. Fare clic su **Completa**.   

### <a name="creating-a-halogen-software-test-user"></a>Creazione di un utente test di Halogen Software
L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Halogen Software.

**Per creare un utente test denominato Britta Simon in Halogen Software, eseguire la procedura seguente:**

1. Accedere all'applicazione **Halogen Software** come amministratore.
2. Fare clic sulla scheda **User Center** (Centro utenti) e quindi su **Create User** (Crea utente).
   
    ![Cos'è Azure AD Connect][300]  
3. Nella pagina **Nuovo utente** eseguire la procedura seguente:
   
    ![Cos'è Azure AD Connect][301]
   
    a. Nella casella di testo **Nome** digitare **Britta**. 
   
    b. Nella casella di testo **Cognome** digitare **Simon**.
   
    c. Nella casella di testo **Username** (Nome utente) digitare il **nome utente di Britta Simon nel portale di Azure classico**.
   
    d. Nella casella di testo **Password** digitare una password per Britta.
   
    e. Fare clic su **Save**.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Halogen Software.

![Cos'è Azure AD Connect][200]

**Per assegnare Britta Simon a Halogen Software, eseguire la procedura seguente:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Cos'è Azure AD Connect][201]
2. Nell'elenco di applicazioni selezionare **Halogen Software**.
   
    ![Cos'è Azure AD Connect][202]
3. Scegliere **Utenti**dal menu in alto.
   
    ![Cos'è Azure AD Connect][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
   
    ![Cos'è Azure AD Connect][204]
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.
   
    ![Cos'è Azure AD Connect][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Halogen Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Halogen Software.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png



<!--HONumber=Nov16_HO3-->


