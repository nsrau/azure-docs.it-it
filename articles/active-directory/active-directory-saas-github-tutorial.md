---
title: 'Esercitazione: Integrazione di Azure Active Directory con GitHub | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 9dc12bc2e313bcb2000724d035156c5054d14e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Esercitazione: Integrazione di Azure Active Directory con GitHub

Questa esercitazione descrive come integrare GitHub con Azure Active Directory (Azure AD).

L'integrazione di GitHub con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a GitHub
- È possibile abilitare gli utenti per l'accesso automatico a GitHub (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con GitHub, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di GitHub abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di GitHub dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-github-from-the-gallery"></a>Aggiunta di GitHub dalla raccolta
Per configurare l'integrazione di GitHub in Azure AD, è necessario aggiungere GitHub dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere GitHub dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **GitHub.com**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. Nel pannello dei risultati selezionare **GitHub** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con GitHub in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di GitHub che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in GitHub.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** in GitHub.

Per configurare e testare l'accesso Single Sign-On di Azure AD con GitHub, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di GitHub](#creating-a-GitHub-test-user)**: per avere una controparte di Britta Simon in GitHub collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione GitHub.

**Per configurare Single Sign-On di Azure AD con GitHub, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **GitHub** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. Nella sezione **URL e dominio GitHub** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. Nella casella di testo **URL di accesso** digitare il valore come: `https://github.com/orgs/<entity-id>/sso`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. In questo caso è consigliabile usare in Identificatore il valore univoco della stringa. Passare alla sezione Admin di GitHub per recuperare questi valori. 

4. Nella sezione **User Attributes** (Attributi utente) selezionare **User Identifier** (Identificatore utente) come user.mail.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. Nella sezione **Certificato di firma SAML** selezionare **Make new certificate active** (Rendi attivo il nuovo certificato) e fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. Nella finestra popup **Rollover certificate** (Certificato di rollover) fare clic su **OK**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. Nella sezione **GitHub Configuration** (Configurazione GitHub) fare clic su **Configure GitHub** (Configura GitHub) per aprire la finestra **Configura accesso**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. In un'altra finestra del Web browser accedere al sito aziendale di GitHub come amministratore.

12. Passare a **Settings** (Impostazioni) e fare clic su **Security** (Sicurezza).

    ![Impostazioni](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. Selezionare la casella **Enable SAML authentication** (Abilita autenticazione SAML) mostrando i campi di configurazione dell'accesso Single Sign-On. Usare quindi il valore URL di Single Sign-On per aggiornare l'URL di Single Sign-On nella configurazione di Azure AD.

    ![Impostazioni](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. Configurare i campi seguenti:

    a. **URL di accesso**: immettere il **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) nella sezione **Configura GitHub** in Azure AD

    b. **Autorità di certificazione**: immettere l'**ID entità SAML** dalla sezione **Configura GitHub** in Azure AD

    c. **Public Certificate** (Certificato pubblico): aprire il certificato scaricato da Azure AD in blocco note e copiare il contenuto incluso tra "BEGIN CERTIFICATE" ed "END CERTIFICATE".

    ![Impostazioni](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. Fare clic su **Test SAML configuration** (Test configurazione SAML) per confermare l'assenza di errori di convalida o errori durante l'accesso SSO.

    ![Impostazioni](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. Fare clic su **Save**

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 


### <a name="creating-a-github-test-user"></a>Creazione di un utente test GitHub

Per consentire agli utenti di Azure AD di accedere a GitHub, è necessario eseguirne il provisioning in GitHub.  
Nel caso di GitHub, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di GitHub come amministratore.

2. Fare clic su **Persone**.

    ![Persone](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "Persone")

3. Fare clic su **Invite member** (Invita membro).

    ![Invitare utenti](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "Invitare utenti")

4. Nella finestra di dialogo **Invite member** (Invita membro) seguire questa procedura:

    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    ![Invitare persone](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "Invitare persone")
    
    b. Fare clic su **Send Invitation** (Invia invito).

    ![Invitare persone](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "Invitare persone")

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso di Single Sign-On di Azure concedendole l'accesso a GitHub.

![Assegna utente][200] 

**Per assegnare Britta Simon a GitHub, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **GitHub.com**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro GitHub nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione GitHub. L'accesso avverrà con un account aziendale, ma è necessario accedere con l'account personale.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
