---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cisco Webex | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 42632dcf8997ec5e987ac8a6615aae24e903399a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Esercitazione: Integrazione di Azure Active Directory con Cisco Webex

Questa esercitazione descrive come integrare Cisco Webex con Azure Active Directory (Azure AD).

L'integrazione di Cisco Webex con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Cisco Webex.
- È possibile abilitare gli utenti per l'accesso automatico a Cisco Webex con gli account Azure AD personali.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre dettagli sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Cisco Webex, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Cisco Webex abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Cisco Webex dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-cisco-webex-from-the-gallery"></a>Aggiungere Cisco Webex dalla raccolta
Per configurare l'integrazione di Cisco Webex in Azure AD, è necessario aggiungere Cisco Webex dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Cisco Webex dalla raccolta, seguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Cisco Webex**. 

5. Selezionare **Cisco Webex** nel pannello dei risultati. Selezionare quindi il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Cisco Webex nell'elenco dei risultati](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Cisco Webex mediante un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Cisco Webex corrispondente a un utente di Azure AD. In altre parole, deve essere stabilito un collegamento tra un utente di Azure AD e un utente correlato in Cisco Webex.

In Cisco Webex assegnare al valore **Username** (Nome utente) lo stesso valore del **nome utente** in Azure AD. Questo passaggio stabilisce il collegamento tra i due utenti. 

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cisco Webex, completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di Cisco Webex](#create-a-cisco-webex-test-user) per avere una controparte di Britta Simon in Cisco Webex collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel nuovo portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Cisco Webex.

**Per configurare l'accesso Single Sign-On di Azure AD con Cisco Webex, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Cisco Webex** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Per abilitare l'accesso Single Sign-On, nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** nell'elenco a discesa **Modalità**.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. Nella sezione **URL e dominio Cisco Webex** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. Nella casella **URL di accesso** digitare un URL usando il modello seguente: `https://<subdomain>.webex.com`

    b. Nella casella **Identificatore** digitare l'URL `http://www.webex.com`.

    c. Nella casella **URL di risposta** digitare un URL nel formato seguente: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con URL di risposta e URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Cisco Webex](https://www.webex.co.in/support/support-overview.html). 

5. Nella sezione **Certificato di firma SAML** selezionare **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Selezionare **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. Nella sezione **Configurazione di Cisco Webex** selezionare **Configura Cisco Webex** per aprire la finestra **Configura accesso**. Copiare **URL di accesso**, **ID di entità SAML** e **URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configure Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Cisco Webex come amministratore.

8. Dal menu in alto selezionare **Site Administration** (Amministrazione sito).

    ![Site Administration](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Site Administration")

9. Nella sezione **Manage Site** (Gestisci sito) selezionare **SSO Configuration** (Configurazione SSO).
   
    ![SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO Configuration")

10. Nella sezione **Federated Web SSO Configuration** (Configurazione SSO Web federato) seguire questa procedura:
   
    ![Federated SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "Federated SSO Configuration")  

    a. Nell'elenco **Federation Protocol** (Protocollo federazione) selezionare **SAML 2.0**.

    b. Per **SSO profile** (Profilo SSO), selezionare **SP Initiated** (Avviato da SP).

    c. Aprire il certificato scaricato nel Blocco note e quindi copiare il contenuto.

    d. Selezionare **Import SAML Metadata** (Importa metadati SAML) e quindi incollare il contenuto copiato del certificato.

    e. Nella casella di testo **Issuer for SAML (IdP ID)** (Autorità di certificazione per SAML - ID IdP) incollare il valore dell'**ID entità SAML** copiato dal portale di Azure.

    f. Nella casella **Customer SSO Service Login URL** (URL di accesso al servizio clienti SSO) incollare l'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    g. Nell'elenco **NameID Format** (Formato NameID) selezionare **Email address** (Indirizzo di posta elettronica).

    h. Nella casella **AuthnContextClassRef** digitare **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.

    i. Nella casella **Customer SSO Service Logout URL** (URL di disconnessione dal servizio clienti SSO) incollare l'**URL di disconnessione** copiato dal portale di Azure.
   
    j. Selezionare **Aggiorna**.

> [!TIP]
> Un riepilogo di queste istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente selezionare la scheda **Single Sign-On** e quindi accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzione di documentazione incorporata, vedere la [documentazione incorporata di Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** selezionare **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-a-cisco-webex-test-user"></a>Creare un utente di test per Cisco Webex

Per consentire agli utenti di Azure AD di accedere a Cisco Webex, è necessario effettuarne il provisioning in Cisco Webex. Nel caso di Cisco Webex, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Cisco Webex**.

2. Passare a **Manage Users** (Gestisci utenti) > **Add User** (Aggiungi utente).
   
    ![Aggiungere utenti](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "Aggiungere utenti")

3. Nella sezione **Add user** (Aggiungi utente) seguire questa procedura:
   
    ![Aggiungere un utente](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "Aggiungere un utente")   

    a. Per **Account Type** (Tipo di account), scegliere **Host**.

    b. Nella casella **First Name** (Nome) digitare il nome dell'utente (in questo caso, **Britta**).

    c. Nella casella **Last Name** (Cognome) digitare il cognome dell'utente (in questo caso, **Simon**).

    d. Nella casella **Username** (Nome utente) digitare l'indirizzo di posta elettronica dell'utente (in questo caso, **Brittasimon@contoso.com**).

    e. Nella casella **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'utente (in questo caso, **Brittasimon@contoso.com**).

    f. Nella casella **Password** digitare la password dell'utente.

    g. Reimmettere la password dell'utente nella casella di testo **Confirm password** (Conferma password).

    h. Selezionare **Aggiungi**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Cisco Webex per effettuare il provisioning degli account utente di Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione l'utente Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Webex.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Cisco Webex, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione applicazioni. Passare poi alla visualizzazione directory e quindi ad **Applicazioni aziendali**.  

2. Selezionare **Tutte le applicazioni**.

    ![Assegnare utenti][201] 

3. Nell'elenco delle applicazioni selezionare **Cisco Webex**.

    ![Collegamento di Cisco Webex nell'elenco delle applicazioni](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Selezionare il pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cisco Webex nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cisco Webex.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png

