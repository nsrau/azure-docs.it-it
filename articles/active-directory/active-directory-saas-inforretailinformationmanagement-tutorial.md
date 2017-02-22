---
title: "Esercitazione: Integrazione di Azure Active Directory con Infor Retail – Information Management | Documentazione Microsoft"
description: "Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Infor Retail – Information Management."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5ff49168-ef81-4169-8e5e-dc86e24dd5e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9ab2950715850d0c2ee0f398cd44399a5974c1a9
ms.openlocfilehash: 6a9f414334f4dc078a3c47dac0c71bbf9f2c4158


---
# <a name="tutorial-azure-active-directory-integration-with-infor-retail--information-management"></a>Esercitazione: Integrazione di Azure Active Directory con Infor Retail – Information Management

Questa esercitazione descrive come integrare Infor Retail – Information Management con Azure Active Directory (Azure AD).

L'integrazione di Infor Retail – Information Management con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi ha accesso a Infor Retail – Information Management
- È possibile abilitare gli utenti per l'accesso automatico a Infor Retail – Information Management con gli account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Infor Retail – Information Management, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Infor Retail – Information Management abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Infor Retail – Information Management dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-infor-retail--information-management-from-the-gallery"></a>Aggiunta di Infor Retail – Information Management dalla raccolta
Per configurare l'integrazione di Infor Retail – Information Management in Azure AD, è necessario aggiungere Infor Retail – Information Management dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Infor Retail – Information Management dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Infor Retail – Information Management**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_001.png)

5. Nel pannello dei risultati selezionare **Infor Retail – Information Management ** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Infor Retail – Information Management usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Infor Retail – Information Management che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Infor Retail – Information Management.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Infor Retail – Information Management.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Infor Retail – Information Management, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Infor Retail – Information Management](#creating-an-infor-retail---information-management-user)**: per avere una controparte di Britta Simon in Infor Retail – Information Management collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Infor Retail – Information Management.

**Per configurare l'accesso Single Sign-On di Azure AD con Infor Retail – Information Management, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Infor Retail – Information Management** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_01.png)

3. Nella sezione **URL e dominio Infor Retail – Information Management**, se si vuole configurare l'applicazione in **modalità avviata da IDP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_02.png)

    a. Nella casella di testo **Identificatore** digitare l'URL usando il modello seguente: `http://<company name>.mingle.infor.com`
    
    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `http://<company name>.mingle.infor.com:443/sp/ACS.saml2`
    
4. Per configurare l'applicazione in **modalità avviata da SP**, nella sezione **URL e dominio Infor Retail – Information Management** seguire questa procedura:
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_03.png)

    a. Fare clic sull'opzione **Mostra impostazioni URL avanzate**

    b. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company name>.mingle.infor.com/<company code>`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso, l'ID e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di Infor Retail – Information Management](http://www.infor.com/contact/).

5. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_04.png)   

6. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_300.png)

7. Nella sezione **Certificato di firma SAML** selezionare **Rendi attivo il certificato nuovo** e fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_05.png)

8. Nella finestra popup **Certificato di rollover** fare clic su **OK**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_400.png)

9. Nella sezione **Certificato di firma SAML** fare clic su **Scarica** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_06.png) 

10. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il [team di supporto di Infor Retail – Information Management](http://www.infor.com/contact/) e fornire i **metadati** scaricati.
  

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore di **Password**.

    d. Fare clic su **Crea**. 



### <a name="creating-an-infor-retail--information-management-test-user"></a>Creazione di un utente test di Infor Retail – Information Management

In questa sezione viene creato un utente chiamato Britta Simon in Infor Retail – Information Management. Rivolgersi al [team di supporto di Infor Retail – Information Management](http://www.infor.com/contact/) per aggiungere gli utenti nella piattaforma Infor Retail – Information Management.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Infor Retail – Information Management.

![Assegna utente][200] 

**Per assegnare Britta Simon a Infor Retail - Information Management, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione con le applicazioni e quindi passare alla visualizzazione con le directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Infor Retail – Information Management**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Infor Retail – Information Management nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Infor Retail – Information Management.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_203.png


<!--HONumber=Jan17_HO3-->


