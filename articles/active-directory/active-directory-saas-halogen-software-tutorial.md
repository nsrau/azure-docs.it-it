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
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 06d4b7495f4201387500944758ba2a0916b619d2
ms.openlocfilehash: cbe20975792ab0f5fe8fac64110ede3a964d443a
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Esercitazione: Integrazione di Azure Active Directory con Halogen Software
Questa esercitazione descrive l'integrazione di Halogen Software con Azure Active Directory (Azure AD).

L'integrazione di Halogen Software con Azure AD offre i vantaggi seguenti: 

* È possibile controllare in Azure AD chi può accedere a Halogen Software. 
* È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a Halogen Software (SSO) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Halogen Software, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Halogen Software abilitata per l'accesso SSO

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è quello di testare l'accesso Single Sign-On (SSO) di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Halogen Software dalla raccolta 
2. Configurazione e test dell'accesso Single Sign-On (SSO) di Microsoft Azure AD

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

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD
L'obiettivo di questa sezione è illustrare come configurare e testare l'accesso SSO di Azure AD con Halogen Software in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso SSO, Azure AD deve conoscere qual è l'utente di Halogen Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Halogen Software.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Halogen Software.

Per configurare e testare l'accesso SSO di Azure AD con Halogen Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)**: per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Halogen Software](#creating-a-halogen-software-test-user)** : per avere una controparte di Britta Simon in Halogen Software collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Halogen Software.

**Per configurare SSO di Azure AD con Halogen Software, eseguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Halogen Software** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][8]
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Halogen Software** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][9]
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configurare le impostazioni dell'app][10]
   1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Halogen Software usando il modello seguente: *https://global.hgncloud.com/fabrikam/welcome.jsp*
   2. Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in Halogen Software** fare clic su **Scarica metadati** e quindi salvare il file di metadati in locale nel computer.
   
    ![Cos'è Azure AD Connect][11]
5. In una finestra del browser diversa accedere all'applicazione **Software alogeno** come amministratore.
6. Scegliere la scheda **Options (Opzioni)** . 
   
    ![Cos'è Azure AD Connect][12]
7. Nel riquadro di spostamento a sinistra fare clic su **SAML Configuration**. 
   
    ![Cos'è Azure AD Connect][13]
8. Nella pagina **Configurazione SAML** seguire la procedura seguente: 

    ![Cos'è Azure AD Connect][14]
  1. Come **Identificatore univoco**, selezionare **NameID**.
  2. Come **Unique Identifier Maps To** (L'identificatore univoco mappa verso) selezionare **Username** (Nome utente).
  3. Per caricare il file di metadati fare clic su **Browse** (Sfoglia) per selezionare il file e quindi fare clic su **Upload File** (Carica file).
  4. Per testare la configurazione, fare clic su **Run Test**. 
    >[!NOTE]
    >È necessario attendere la visualizzazione del messaggio"*The SAML test is complete. Please close this window*". chiudere quindi la ginestra del browser aperta. La casella di controllo **Enable SAML** (Abilita SAML) è selezionata solo se il test è stato completato. 
    >
  5. Selezionare **Enable SAML**.
  6. Fare clic su **Salva modifiche**. 
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**. 
   
    ![Cos'è Azure AD Connect][15]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
    
    ![Cos'è Azure AD Connect][16]

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
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
  1. In **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
  2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
  3. Fare clic su Avanti.
6. Nella pagina della finestra di dialogo **Profilo utente** seguire questa procedura: 
   
   ![Cos'è Azure AD Connect][104] 
  1. Nella casella di testo **Nome** digitare **Britta**.  
  2. Nella casella di testo **Cognome** digitare **Simon**.
  3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
  4. Nell'elenco **Ruolo** selezionare **Utente**.
  5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Cos'è Azure AD Connect][105]  
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Cos'è Azure AD Connect][106]   
  1. Prendere nota del valore visualizzato in **Nuova password**.
  2. Fare clic su **Completa**.   

### <a name="create-a-halogen-software-test-user"></a>Creare un utente test di Halogen Software
L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Halogen Software.

**Per creare un utente test denominato Britta Simon in Halogen Software, eseguire la procedura seguente:**

1. Accedere all'applicazione **Halogen Software** come amministratore.
2. Fare clic sulla scheda **User Center** (Centro utenti) e quindi su **Create User** (Crea utente).
   
    ![Cos'è Azure AD Connect][300]  
3. Nella pagina **Nuovo utente** eseguire la procedura seguente:
   
    ![Cos'è Azure AD Connect][301]
  1. Nella casella di testo **Nome** digitare **Britta**. 
  2. Nella casella di testo **Cognome** digitare **Simon**. 
  3. Nella casella di testo **Username** (Nome utente) digitare il **nome utente di Britta Simon nel portale di Azure classico**.
  4. Nella casella di testo **Password** digitare una password per Britta.
  5. Fare clic su **Save**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon a usare l'accesso Single Sign-On di Azure concedendole l'accesso a Halogen Software.

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

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On
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

