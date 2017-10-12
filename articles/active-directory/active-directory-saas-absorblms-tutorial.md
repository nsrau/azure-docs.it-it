---
title: 'Esercitazione: Integrazione di Azure Active Directory con Absorb LMS | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Absorb LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 549e353bb41f1f31bc0441294d0a8066779402e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Esercitazione: Integrazione di Azure Active Directory con Absorb LMS

Questa esercitazione descrive come integrare Absorb LMS con Azure Active Directory (Azure AD).

L'integrazione di Absorb LMS con Azure AD offre i vantaggi seguenti:

- In Azure AD è possibile controllare chi può accedere ad Absorb LMS.
- È possibile abilitare gli utenti per l'accesso automatico ad Absorb LMS, tramite l'accesso Single Sign-On, con i propri account di Azure AD.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS, Software as a Service, con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Absorb LMS, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Absorb LMS abilitata per l'accesso Single Sign-On

> [!NOTE]
> È consigliabile non usare un ambiente di produzione per questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

* Aggiunta di Absorb LMS dalla raccolta
* Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-absorb-lms-from-the-gallery"></a>Aggiungere Absorb LMS dalla raccolta
Per configurare l'integrazione di Absorb LMS in Azure AD, aggiungere Absorb LMS dalla raccolta al proprio elenco di app SaaS gestite.

Per aggiungere Absorb LMS dalla raccolta, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali][2]
    
3. Per aggiungere un'applicazione selezionare il pulsante **Nuova applicazione**.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Absorb LMS**, selezionare **Absorb LMS** nel pannello dei risultati e quindi selezionare il pulsante **Aggiungi**.

    ![Absorb LMS nell'elenco risultati](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Absorb LMS in base un utente test di nome Britta Simon.

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di Absorb LMS in Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente corrispondente in Absorb LMS.

La relazione di collegamento viene stabilita assegnando il valore di *nome utente* in Azure AD come valore di *Username* (Nome utente) in Absorb LMS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Absorb LMS, completare i blocchi nelle cinque sezioni successive.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Absorb LMS.

Per configurare l'accesso Single Sign-On di Azure AD con Absorb LMS, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Absorb LMS** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** nella casella **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. Nella sezione **Absorb LMS Domain and URLs** (URL e dominio di Absorb LMS) seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. Nella casella **Identificatore** digitare un URL che usi la sintassi seguente: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. Nella casella **URL di risposta** digitare un URL che usi la sintassi seguente: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Questi URL non sono i valori reali. Aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Absorb LMS](https://www.absorblms.com/support). 

4. Nella sezione **Certificato di firma SAML** selezionare **XML metadati** nella colonna **Download** e quindi salvare il file dei metadati nel computer.

    ![Collegamento al download del certificato di firma](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Selezionare **Salva**.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. Nella sezione **Absorb LMS Configuration** (Configurazione di Absorb LMS) selezionare **Configure Absorb LMS** (Configura Absorb LMS) per aprire la finestra **Configura accesso** e copiare il valore di **Sign-Out URL** (URL di disconnessione) nella **sezione Riferimento rapido.**

    ![Riquadro della configurazione di Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. In una nuova finestra del Web browser accedere al sito aziendale di Absorb LMS come amministratore.

8. Selezionare il pulsante **Account** in alto a destra. 

    ![Pulsante Account](./media/active-directory-saas-absorblms-tutorial/1.png)

9. Nel riquadro Account selezionare **Portal Settings** (Impostazioni del portale).

    ![Collegamento Impostazioni del portale](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. Selezionare la scheda **Utenti** ,

    ![La scheda Utenti](./media/active-directory-saas-absorblms-tutorial/3.png)

11. Nella pagina di configurazione Single Sign-On eseguire la procedura seguente:

    ![Pagina di configurazione Single Sign-on](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. Nella casella **Mode** (Modalità) selezionare **Identity Provider Initiated** (Avviato dal provider di identità).

    b. Nel blocco note, aprire il certificato scaricato dal portale di Azure. Rimuovere i tag **-----BEGIN CERTIFICATE-----** ed **-----END CERTIFICATE-----**. Quindi, nella casella **Key** (Chiave) incollare il contenuto rimanente.
    
    c. Nella casella **Id Property** (ID proprietà) selezionare l'attributo configurato come identificatore dell'utente in Azure AD. Ad esempio, se in Azure AD è stato selezionato *userPrincipalName*, selezionare **Username**.

    d. Nella casella **Login URL** (URL di accesso) incollare l'**URL di accesso utente** dalla pagina **Proprietà** dell'applicazione del portale di Azure.

    e. In **Logout URL** (URL disconnessione) incollare il valore dell'**URL di disconnessione** copiato dalla finestra **Configura accesso** del portale di Azure.

12. Spostare **Only Allow SSO Login** (Consenti solo accesso SSO) su **On** (Attivo).

    ![Abilitare Only Allow SSO Login](./media/active-directory-saas-absorblms-tutorial/5.png)

13. Selezionare **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** selezionare la scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Per altre informazioni, vedere la [documentazione incorporata su Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test di nome Britta Simon nel portale di Azure.

![Creare un utente test di Azure AD][100]

Per creare un utente di test in Azure AD, seguire questa procedura:

1. Nel portale di Azure fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Per visualizzare l'elenco di utenti, selezionare **Utenti e gruppi** > **Tutti gli utenti**.
    
    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo selezionare **Aggiungi**.
 
    ![Pulsante Aggiungi](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo Utente](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'indirizzo di posta elettronica di Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore nella casella **Password**.

    d. Selezionare **Crea**.

### <a name="create-an-absorb-lms-test-user"></a>Creare un utente di test di Absorb LMS

Affinché gli utenti di Azure AD possano accedere ad Absorb LMS devono essere configurati in Absorb LMS.  

In Absorb LMS la configurazione è un'attività manuale.

Per configurare un account utente, effettuare le operazioni seguenti:

1. Accedere al sito aziendale di Absorb LMS come amministratore.

2. Nel riquadro a sinistra selezionare **Users** (Utenti).

    ![Collegamento utenti di Absorb LMS](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. Nel riquadro **Users** (Utenti) selezionare **Users** (Utenti).

    ![Collegamento Utenti](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. Nell'elenco a discesa **Add New** (Aggiungi nuovo) selezionare **User** (Utente).

    ![Elenco a discesa Aggiungi nuovo](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. Nella pagina **Add User** (Aggiungi utente) eseguire la procedura seguente:

    ![Pagina Aggiungi utente](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. Nella casella di testo **First Name** (Nome) digitare il nome, ad esempio **Britta**.

    b. Nella casella di testo **Last Name** (Cognome) digitare il cognome, ad esempio **Simon**.
    
    c. Nella casella di testo **Username** (Nome utente) digitare il nome completo, ad esempio **Britta Simon**.

    d. Nella casella **Password** digitare la password di Britta Simon.

    e. Nella casella **Confirm Password** (Conferma la password) ridigitare la password.
    
    f. Impostare il pulsante **Is Active** (È attivo) su **Active** (Attivo).  

6. Selezionare **Salva**.
 
### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione l'utente Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Absorb LMS.

![Assegnare il ruolo utente][200]

Per assegnare Britta Simon ad Absorb LMS, seguire questa procedura:

1. Nel portale di Azure aprire la visualizzazione dell'applicazione, passare alla visualizzazione della directory, selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Collegamento "Tutte le applicazioni"][201] 

2. Nell'elenco **Applicazioni** selezionare **Absorb LMS**.

    ![Collegamento di Absorb LMS nell'elenco delle applicazioni](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][202] 

4. Fare clic su **Aggiungi** e quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro **Absorb LMS** nel pannello di accesso, si accede automaticamente all'applicazione Absorb LMS. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png

