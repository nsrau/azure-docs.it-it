---
title: 'Esercitazione: Integrazione di Azure Active Directory con Tableau Server | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Tableau Server.
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0849c77c66ac8617e217a69696b5c404be3b5eb4
ms.openlocfilehash: 3aefb8188880fac5c1fcbe237a1e133584089e6a
ms.lasthandoff: 02/03/2017

---

# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Esercitazione: Integrazione di Azure Active Directory con Tableau Server
Questa esercitazione descrive l'integrazione di Tableau Server con Azure Active Directory (Azure AD).

L'integrazione di Tableau Server con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Tableau Server
* È possibile abilitare gli utenti per l'accesso automatico a Tableau Server (Single Sign-On) con i propri account Azure AD
* È possibile gestire gli account da una posizione centrale con il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con Tableau Server, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Sottoscrizione di Tableau Server abilitata per l'accesso Single Sign-On (SSO)

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.
> 

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è testare l'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Tableau Server dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-tableau-server-from-the-gallery"></a>Aggiunta di Tableau Server dalla raccolta
Per configurare l'integrazione di Tableau Server in Azure AD, è necessario aggiungere Tableau Server dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Tableau Server dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Tableau Server**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)
7. Nel riquadro dei risultati selezionare **Tableau Server** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Selezione dell'app nella raccolta](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come configurare e testare l'accesso Single Sign-On di Azure AD con Tableau Server con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Tableau Server che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tableau Server.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in Tableau Server.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Tableau Server, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Tableau Server](#creating-a-tableauserver-test-user)** : per avere una controparte di Britta Simon in Tableau Server collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure classico e configurare l'accesso Single Sign-On nell'applicazione Tableau Server.

L'applicazione Tableau Server si aspetta che le asserzioni SAML abbiano un formato specifico. La schermata seguente illustra un esempio relativo a questa operazione. 

![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png) 

**Per configurare l'accesso Single Sign-On di Azure AD con Tableau Server, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **Tableau Server** del portale di Azure classico fare clic su **Attributi**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png) 
2. Nella finestra di dialogo **Attributi token SAML** seguire questa procedura:

   1. Fare clic su **aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi attributo utente**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png) 
   2. Nella casella di testo **Nome attributo** digitare **username**.
   3. Nell'elenco **Valore attributo** selezionare **user.displayname**.
   4. Fare clic su **Completa**.    

3. Nel menu in alto fare clic su **Avvio rapido**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)  
4. Fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On][6] 
5. Nella pagina **Stabilire come si desidera che gli utenti accedano a Tableau Server** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png) 
6. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png) 

   1. Nella casella di testo **URL di accesso** digitare l'URL del server Tableau. 
   2. Nella **casella Identificatore** copiare l'URL.
   3. Fare clic su **Avanti**.
h
7. Nella pagina **Configura accesso Single Sign-On in Tableau Server** seguire questa procedura e fare clic su **Avanti**:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png) 

   1. Fare clic su **Scarica metadati**e quindi salvare il file nel computer.
   2. Fare clic su **Avanti**.

8. Per configurare l'accesso SSO sull'applicazione, è necessario accedere al tenant di Tableau Server come amministratore.
   
   1. Nella finestra Tableau Server Configuration (Configurazione Tableau Server) fare clic sulla scheda **SAML** .
  
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
   2. Selezionare la casella di controllo **Use SAML for single sign-on (Usa SAML per Single Sign-On)**.
   3. Cercare il file di metadati federazione scaricato dal portale di Azure classico e quindi caricarlo in **SAML Idp metadata file**(File metadati Idp SAML).
   4. Tableau Server return URL (URL restituito di Tableau Server): l'URL a cui accedono gli utenti di Tableau Server, ad esempio http://tableau_server. Non è consigliabile utilizzare http://localhost. Gli URL con barra finale (ad esempio http://tableau_server/) non sono supportati. Copiare **Tableau Server return URL** e incollarlo nella casella di testo **URL di accesso** in Azure AD come illustrato nel passaggio 3
   5. SAML entity ID (ID entità SAML): l'ID entità identifica in modo univoco l'installazione di Tableau Server nel provider di identità. Se si desidera, qui è possibile immettere di nuovo l'URL di Tableau Server, ma non deve essere l'URL di Tableau Server in uso. Copiare **SAML entity ID** e incollarlo nella casella di testo **Identificatore** in Azure AD come illustrato nel passaggio 3.
   6. Fare clic su di **Export Metadata File (Esporta file di metadati)** e aprirlo nell'applicazione editor di testo. Cercare l'URL del servizio consumer di asserzione con Http Post e Indice 0 e copiarlo. A questo punto, incollare l'URL nella casella di testo **URL di risposta** in Azure AD come illustrato nel passaggio 3. 
   7. Fare clic sul pulsante **OK** nella pagina Tableau Server Configuration.
   
    >[!NOTE] 
    >Per assistenza nella configurazione di SAML in Tableau Server, vedere questo articolo: [Configure SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm) (Configurazione di SAML).
    >

9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e fare clic su **Avanti**.
   
    ![Single Sign-On di Microsoft Azure AD][10]
10. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**. 
   
    ![Single Sign-On di Microsoft Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di Azure classico.

* Nell'elenco di utenti selezionare **Britta Simon**.

![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png) 
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 
4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)
5. Nella pagina **Informazioni sull'utente** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png) 
   
   1. In **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
   2. Nella casella di testo **Nome utente** digitare **BrittaSimon**.
   3. Fare clic su **Avanti**.
6. Nella pagina **Profilo utente** seguire questa procedura:
   
   ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png) 
   
   1. Nella casella di testo **Nome** digitare **Britta**.  
   2. Nella casella di testo **Cognome** digitare **Simon**.
   3. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.
   4. Nell'elenco **Ruolo** selezionare **Utente**.
   5. Fare clic su **Avanti**.
7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png) 
8. Nella pagina **Ottieni password temporanea** seguire questa procedura:
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png) 
   
   1. Prendere nota del valore visualizzato in **Nuova password**.
   2. Fare clic su **Completa**.   

### <a name="creating-a-tableau-server-test-user"></a>Creazione di un utente test di Tableau Server
Questa sezione descrive come creare un utente chiamato Britta Simon in Tableau Server. In Tableau Server è necessario eseguire il provisioning di tutti gli utenti. Si noti anche che il valore username dell'utente deve corrispondere al valore configurato nell'attributo personalizzato **username**in Azure AD. Con un mapping corretto, l'integrazione per la [Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-single-sign-on)dovrebbe funzionare.

> [!NOTE]
> Se si deve creare manualmente un utente, è necessario contattare l'amministratore di Tableau Server dell'organizzazione.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD
Questa sezione descrive come abilitare Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Tableau Server.

![Assegna utente][200] 

**Per assegnare Britta Simon a Tableau Server, seguire questa procedura:**

1. Per aprire la visualizzazione applicazioni nel portale di Azure classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.
   
    ![Assegna utente][201] 
2. Nell'elenco delle applicazioni selezionare **Tableau Server**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png) 
3. Scegliere **Utenti**dal menu in alto.
   
    ![Assegna utente][203]
4. Nell'elenco di utenti selezionare **Britta Simon**.
5. Fare clic su **Assegna**sulla barra degli strumenti in basso.

![Assegna utente][205]

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On
Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Tableau Server nel pannello di accesso, viene effettuato automaticamente l'accesso all'applicazione Tableau Server.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png

