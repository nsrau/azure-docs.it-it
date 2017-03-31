---
title: 'Esercitazione: Integrazione di Azure Active Directory con Evernote | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory ed Evernote.
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
ms.date: 03/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: e4e2ca40ae270e7d3bfe4e828fcd571b5594ffc8
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Esercitazione: Integrazione di Azure Active Directory con Evernote

Questa esercitazione descrive come integrare Evernote con Azure Active Directory (Azure AD).

L'integrazione di Evernote con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Evernote.
- È possibile abilitare gli utenti per l'accesso automatico a Evernote (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Evernote, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Evernote abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Evernote dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-evernote-from-the-gallery"></a>Aggiunta di Evernote dalla raccolta
Per configurare l'integrazione di Evernote in Azure AD, è necessario aggiungere Evernote dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Evernote dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Evernote**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search01.png)

5. Nel pannello dei risultati selezionare **Evernote** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search_result01.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Evernote in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Evernote che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Evernote.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Evernote.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Evernote, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
4. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Evernote.

**Per configurare Single Sign-On di Azure AD con Evernote, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Evernote** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. Nella sezione **URL e dominio Evernote**, se si vuole configurare l'applicazione in **modalità avviata da IDP**, non è necessario eseguire alcun passaggio.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_idp.png)
    
4. Nella sezione **URL e dominio Evernote**, se si vuole configurare l'applicazione in **modalità avviata da IDP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sp_01.png)
    
    a. Fare clic sull'opzione **Mostra impostazioni URL avanzate**

    b. Nella casella di testo **URL di accesso**, digitare il seguente URL: `https://www.evernote.com/Login.action`.

5. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_cert.png)     

6. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_general_300.png)

7. Nella sezione **Certificato di firma SAML** selezionare **Make new certificate active** (Rendi attivo il nuovo certificato) e fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_makecertactive.png)

8. Nella finestra popup **Rollover certificate** (Certificato di rollover) fare clic su **OK**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

9. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

10. Nella sezione **Configurazione di Evernote** fare clic su **Configura Evernote** per aprire la finestra **Configura accesso**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configuresignon.png)

11. In un'altra finestra del Web browser accedere al sito aziendale di Evernote come amministratore.

12. Passare a **Admin Console** (Console di amministrazione)

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

13. Da **Admin Console** (Console di amministrazione), andare a **Security** (Sicurezza) e selezionare **Single Sign-On** (Single Sign-On).

    ![SSO-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

14. Configurare i valori seguenti:

    a.  **Enable SSO** (Abilita SSO): SSO è abilitato per impostazione predefinita. Fare clic su **Disable Single Sign-on** (Disabilita Single Sign-on) per rimuovere il requisito SSO.

    b. **SAML HTTP Request URL** (URL della richiesta HTTP SAML): immettere il **SAML Single sign-on Service URL** (URL servizio Single Sign-On SAML) dalla sezione **Configura Evernote** su Azure AD.

    c. **X.509 Certificate** (Certificato x.&509;): aprire il certificato scaricato da Azure AD in blocco note e copiare il contenuto incluso tra "BEGIN CERTIFICATE" ed "END CERTIFICATE".

    ![Certificate-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)

    Fare clic su **Save Changes** (Salva modifiche). 


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 



### <a name="creating-an-evernote-test-user"></a>Creazione di un utente test Evernote

Per consentire agli utenti di Azure AD di accedere a Evernote, è necessario eseguirne il provisioning in Evernote.  
Nel caso di Evernote, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Evernote come amministratore.

2. Fare clic su di **Admin Console** (Console di amministrazione).

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Da **Admin Console** (Console di amministrazione), andare a **Add users** (Aggiungi utenti).

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. **Add team members**(Aggiungi membri del team): nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'account utente e fare clic su **Invite** (Invita).

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. Dopo l'invio dell'invito, il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica per accettare l'invito.   


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Evernote.

![Assegna utente][200] 

**Per assegnare Britta Simon a Evernote, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Evernote**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Evernote nel pannello di accesso, si dovrebbe accedere all'applicazione Evernote. L'accesso avverrà con un account aziendale, ma è necessario accedere con l'account personale.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

