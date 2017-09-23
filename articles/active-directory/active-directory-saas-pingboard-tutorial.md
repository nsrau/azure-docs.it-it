---
title: 'Esercitazione: Integrazione di Azure Active Directory con PingBoard | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PingBoard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 008c670a8043da0c67ccefde48d5ef721c75d97c
ms.contentlocale: it-it
ms.lasthandoff: 04/07/2017

---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Esercitazione: Integrazione di Azure Active Directory con PingBoard

Questa esercitazione descrive come integrare PingBoard con Azure Active Directory (Azure AD).

L'integrazione di PingBoard con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a PingBoard
- È possibile abilitare gli utenti per l'accesso automatico a PingBoard (Single Sign-On) con i propri account di Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con PingBoard, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di PingBoard abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di PingBoard dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-pingboard-from-the-gallery"></a>Aggiunta di PingBoard dalla raccolta
Per configurare l'integrazione di PingBoard in Azure AD, è necessario aggiungere PingBoard dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere PingBoard dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **PingBoard**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_search.png)

5. Nel pannello dei risultati selezionare **PingBoard** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con PingBoard con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di PingBoard che corrisponde a un utente di Azure AD. In altre parole, si deve stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PingBoard.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD lo stesso valore di **Nome utente** in PingBoard.

Per configurare e testare l'accesso Single Sign-On di Azure AD con PingBoard, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di PingBoard](#creating-a-pingboard-test-user)**: per avere una controparte di Britta Simon in PingBoard collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione PingBoard.

**Per configurare l'accesso Single Sign-On di Azure AD con PingBoard, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **PingBoard** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. Nella sezione **URL e dominio PingBoard**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_url.png)

    a. Nella casella di testo **Identificatore** digitare il valore `http://<entity-id>.pingboard.com/sp`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<entity-id>.pingboard.com/auth/saml/consume`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di risposta effettivi. In questo caso è consigliabile usare in Identificatore il valore univoco della stringa. Per ottenere questi valori, contattare il [team di supporto clienti di PingBoard](https://support.pingboard.com/). 

4. Selezionare **Mostra impostazioni URL avanzate**, se si desidera configurare l'applicazione in modalità avviata da **SP**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

    a. Nella casella di testo **URL di accesso** digitare il valore come: `http://<sub-domain>.pingboard.com/sign_in`
     
5. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/tutorial_general_400.png)

7. Per configurare il SSO sul lato PingBoard, aprire una nuova finestra del browser e accedere al proprio account di PingBoard. È necessario essere un amministratore di PingBoard per configurare il Single Sign-On.

8. Nel menu principale selezionare **App > Integrazioni**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/Pingboard_integration.png)

9.  Nella pagina **Integrazioni**, trovare e fare clic sul riquadro di **"Azure Active Directory"**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/Pingboard_aad.png)

10. Nella finestra modale che segue fare clic su **"Configura"**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/Pingboard_configure.png)

11. Nella pagina seguente, si noterà che l'"integrazione SSO di Azure è abilitata". Aprire il file XML metadati scaricato in Notepad e incollare il contenuto in **IDP Metadata** (Metadati IDP).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/Pingboard_sso_configure.png)

12. Il file verrà convalidato e verrà abilitato il Single Sign-On se tutto è corretto

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pingboard-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pingboard-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pingboard-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-pingboard-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-pingboard-test-user"></a>Creazione di un utente test PingBoard

Per consentire agli utenti di Azure AD di accedere a PingBoard, è necessario eseguirne il provisioning in PingBoard.  
Nel caso di PingBoard, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di PingBoard come amministratore.

2. Fare clic sul pulsante **"Add Employee"** ("Aggiungi dipendente") nella pagina **Directory**.

    ![Aggiungere un dipendente](./media/active-directory-saas-pingboard-tutorial/create_testuser_add.png)

3. Nella pagina della finestra di dialogo **"Add Employee"** ("Aggiungi dipendente") eseguire la procedura seguente.

    ![Invita persone](./media/active-directory-saas-pingboard-tutorial/create_testuser_name.png)

    a. Nella casella di testo **Nome completo** digitare il nome completo di Britta Simon.

    b. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    c. Nella casella di testo **Posizione** digitare la posizione di Britta Simon.

    d. Nell'elenco a discesa **Località**, selezionare la località di Britta Simon.
    
    e. Fare clic su **Aggiungi**.   

4. Verrà visualizzata una schermata di conferma per confermare l'aggiunta dell'utente.
    
    ![confermare](./media/active-directory-saas-pingboard-tutorial/create_testuser_confirm.png)
        
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione, Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PingBoard.

![Assegna utente][200] 

**Per assegnare Britta Simon a PingBoard, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione con le applicazioni e quindi passare alla visualizzazione con le directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **PingBoard**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di PingBoard nel pannello di accesso, si dovrebbe accedere automaticamente all’applicazione PingBoard.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_203.png

