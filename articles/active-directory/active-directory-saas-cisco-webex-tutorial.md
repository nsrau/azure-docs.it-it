---
title: ': Esercitazione Integrazione di Azure Active Directory con Cisco Webex | Documenti Microsoft'
description: Informazioni su come configurare single sign-on tra Azure Active Directory e Cisco Webex.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>: Esercitazione Integrazione di Azure Active Directory con Cisco Webex

In questa esercitazione informazioni su come integrare Cisco Webex con Azure Active Directory (Azure AD).

Integrazione di Cisco Webex con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso a Cisco Webex.
- È possibile consentire agli utenti di ottenere accesso viene eseguito automaticamente a Cisco Webex con gli account di Azure AD.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre dettagli sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cisco Webex, è necessario quanto segue:

- Sottoscrizione di Azure AD
- Una sottoscrizione Cisco Webex single sign-in-abilitato

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
Per configurare l'integrazione di Cisco Webex in Azure AD, è necessario aggiungere all'elenco di App SaaS gestite Cisco Webex dalla raccolta.

**Per aggiungere Cisco Webex dalla raccolta, eseguire le operazioni seguenti:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **Cisco Webex**. 

5. Selezionare **Cisco Webex** dal Pannello di risultati. Selezionare quindi il **Aggiungi** pulsante per aggiungere l'applicazione.

    ![Cisco Webex nell'elenco dei risultati](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con Cisco Webex, in base a un utente di test denominato "Britta Simon".

Per single sign-on a funzionare, Azure AD deve conoscere l'identità dell'utente corrispondente in Cisco Webex a un utente in Azure AD. In altre parole, è necessario stabilire un collegamento tra un utente di Azure AD e un utente correlato in Cisco Webex.

In Cisco Webex, assegnare il valore **Username** lo stesso valore come **nome utente** in Azure AD. Questo passaggio stabilisce il collegamento tra i due utenti. 

Per configurare e testare Azure AD single sign-on con Cisco Webex, completare i seguenti blocchi predefiniti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente test Cisco Webex](#create-a-cisco-webex-test-user) disporre di un equivalente di Britta Simon in Cisco Webex collegato per la rappresentazione di Azure AD dell'utente.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale di Azure e configurare l'accesso single sign-on nell'applicazione Cisco Webex.

**Per configurare Azure single sign-on AD con Cisco Webex, eseguire le operazioni seguenti:**

1. Nel portale di Azure, sul **Cisco Webex** pagina di integrazione dell'applicazione, seleziona **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Per abilitare single sign-on, nel **Single sign-on** della finestra di dialogo di **modalità** elenco a discesa, seleziona **basato su SAML Sign-on**.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. Nel **Cisco Webex dominio e gli URL** sezione, eseguire le operazioni seguenti:

    ![URL e Cisco Webex dominio single sign-on, informazioni](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. Nella casella **URL di accesso** digitare un URL usando il modello seguente: `https://<subdomain>.webex.com`

    b. Nel **identificatore** casella, digitare l'URL `http://www.webex.com`.

    c. Nella casella **URL di risposta** digitare un URL nel formato seguente: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'URL di risposta effettivo e URL sign-on. Contatto [team di supporto Client di Cisco Webex](https://www.webex.co.in/support/support-overview.html) per ottenere questi valori. 

5. Nel **certificato di firma SAML** selezionare **Metadata XML**e quindi salvare il file di metadati nel computer in uso.

    ![Collegamento di download del certificato](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Selezionare **Salva**.

    ![Configurare l'accesso single sign-On pulsante Salva](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. Nel **Cisco Webex configurazione** selezionare **configurare Cisco Webex** per aprire la **Configura sign-on** finestra. Copiare **URL di accesso**, **ID di entità SAML** e **URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configure Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. In una finestra del web browser, accedere al sito della società Cisco Webex come amministratore.

8. Nel menu in alto, selezionare **amministrazione sito**.

    ![Site Administration](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Site Administration")

9. Nel **Manage Site** selezionare **configurazione di SSO**.
   
    ![SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO Configuration")

10. Nel **configurazione di SSO Web federata** sezione, eseguire le operazioni seguenti:
   
    ![Federated SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "Federated SSO Configuration")  

    a. Nel **protocollo Federation** elenco, selezionare **SAML 2.0**.

    b. Per **profilo SSO**selezionare **SP Initiated**.

    c. Aprire il certificato scaricato nel blocco note e quindi copiare il contenuto.

    d. Selezionare **Import SAML Metadata**, quindi incollare il contenuto copiato del certificato.

    e. Nel **Issuer for SAML (IdP ID)** incollare il valore della **ID entità SAML** copiato dal portale di Azure.

    f. Nel **Customer SSO Service Login URL** incollare **SAML Single Sign-On Service URL**, che è stato copiato dal portale di Azure.

    g. Nell'elenco **NameID Format** (Formato NameID) selezionare **Email address** (Indirizzo di posta elettronica).

    h. Nel **AuthnContextClassRef** digitare **urn: oasis: nomi: tc: SAML:2.0:ac:classes:Password**.

    i. Nel **Customer SSO Service Logout URL** incollare **Sign-Out URL**, che è stato copiato dal portale di Azure.
   
    j. Selezionare **aggiornamento**.

> [!TIP]
> Un riepilogo di queste istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto questa app dal **Active Directory** > **applicazioni aziendali** sezione, selezionare il **Single Sign-On** scheda e quindi accedere incorporato documentazione tramite il **configurazione** sezione nella parte inferiore. Per altre informazioni sulla funzione di documentazione incorporata, vedere la [documentazione incorporata di Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

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
 
### <a name="create-a-cisco-webex-test-user"></a>Creare un utente test Cisco Webex

Per consentire agli utenti di Azure AD accedere a Cisco Webex, è necessario eseguirne il provisioning in Cisco Webex. Nel caso di Cisco Webex, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, eseguire le operazioni seguenti:**

1. Accedi al **Cisco Webex** tenant.

2. Passare a **Gestisci utenti** > **aggiungere utente**.
   
    ![Aggiungere utenti](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "Aggiungere utenti")

3. Nel **Aggiungi utente** sezione, eseguire le operazioni seguenti:
   
    ![Aggiungere un utente](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "Aggiungere un utente")   

    a. Per **tipo di Account**selezionare **Host**.

    b. Nel **nome** , digitare il nome dell'utente (in questo caso, **Laura**).

    c. Nel **cognome** , digitare il cognome dell'utente (in questo caso, **Simon**).

    d. Nel **Username** , digitare il messaggio di posta elettronica dell'utente (in questo caso,  **Brittasimon@contoso.com** ).

    e. Nel **posta elettronica** , digitare l'indirizzo di posta elettronica dell'utente (in questo caso,  **Brittasimon@contoso.com** ).

    f. Nella casella **Password** digitare la password dell'utente.

    g. Nel **conferma** Password casella, digitare nuovamente la password dell'utente.

    h. Selezionare **Aggiungi**.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione account utente di Cisco Webex o le API fornite da Cisco Webex, eseguire il provisioning degli account utente di Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è consentire all'utente di Britta Simon per utilizzare single sign-on Azure concedendo l'accesso a Cisco Webex.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Cisco Webex, eseguire le operazioni seguenti:**

1. Nel portale di Azure aprire la visualizzazione applicazioni. Quindi, passare alla visualizzazione directory e quindi a **applicazioni aziendali**.  

2. Selezionare **Tutte le applicazioni**.

    ![Assegnare utenti][201] 

3. Nell'elenco delle applicazioni, selezionare **Cisco Webex**.

    ![Il collegamento di Cisco Webex nell'elenco delle applicazioni](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **utenti e gruppi** nel **Aggiungi** la finestra di dialogo.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Selezionare il pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Cisco Webex nel Pannello di accesso, l'utente automaticamente ottenere connesso all'applicazione di Cisco Webex.

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

