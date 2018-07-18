---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Sign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: 9a1a1f1a1866e5221775d583a9bafe86eef17131
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221224"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Sign

Questa esercitazione descrive come integrare Adobe Sign con Azure Active Directory (Azure AD).

L'integrazione di Adobe Sign con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Adobe Sign.
- È possibile abilitare gli utenti per l'accesso automatico a Adobe Sign (Single Sign-On) con i propri account Azure AD.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre dettagli sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Adobe Sign, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Adobe Sign abilitata per l'accesso Single Sign-On

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Adobe Sign dalla raccolta.
2. Configurazione e test dell'accesso Single Sign-On di Azure AD.

## <a name="add-adobe-sign-from-the-gallery"></a>Aggiungere Adobe Sign dalla raccolta
Per configurare l'integrazione di Adobe Sign in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Screenshot dell'icona di Azure Active Directory][1]

2. Esplorare **Applicazioni aziendali** > **All applications** (Tutte le applicazioni).

    ![Schermata dei menu di Azure AD, con evidenziate le applicazioni aziendali e tutte le applicazioni][2]
    
3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Schermata dell'opzione Nuova applicazione nella parte superiore della finestra di dialogo][3]

4. Nella casella di ricerca digitare **Adobe Sign**.

    ![Screenshot della casella di ricerca](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Nel riquadro dei risultati selezionare **Adobe Sign**, quindi selezionare **Aggiungi**.

    ![Schermata del riquadro dei risultati](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adobe Sign usando un utente test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Sign.

Per stabilire la relazione di collegamento, in Adobe Sign assegnare il valore di **nome utente** in Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adobe Sign, completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#creating-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di Adobe Sign](#creating-an-adobe-sign-test-user) per avere una controparte di Britta Simon in Adobe Sign collegata alla relativa rappresentazione in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assigning-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#testing-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Adobe Sign.

1. Nella pagina di integrazione dell'applicazione **Adobe Sign** del portale di Azure selezionare **Single Sign-On**.

    ![Schermata della pagina di integrazione dell'applicazione Adobe Sign con accesso Single Sign-On evidenziato][4]

2. Nella finestra di dialogo **Single Sign-On**, in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.

    ![Schermata della finestra di dialogo Single Sign-On, con la casella Modalità evidenziata](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Nella sezione **URL e dominio di Adobe Sign** seguire questa procedura:

    ![Schermata della sezione URL e dominio di Adobe Sign](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. Nella casella di testo **URL accesso** digitare un URL che usa il modello seguente: `https://<companyname>.echosign.com/`

    b. Nella casella di testo **Identificatore** digitare l'URL che usa il modello seguente:`https://<companyname>.echosign.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con URL di accesso e identificatore effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html).

4. Nella sezione **Certificato di firma SAML** selezionare **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Schermata della sezione Certificato di firma SAML](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Selezionare **Salva**.

    ![Screenshot del pulsante Salva](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. Nella sezione **Adobe Sign Configuration** (Configurazione di Adobe Sign) fare clic su **Configure Adobe Sign** (Configura Adobe Sign) per aprire la finestra **Configure sign-on** (Configura accesso). Copiare **URL di accesso**, **ID di entità SAML** e **URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Schermata della sezione di configurazione di Adobe Sign, con evidenziata l'opzione Configura Adobe Sign](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Prima della configurazione, contattare il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html) per inserire il proprio dominio nell'elenco elementi consentiti di Adobe Sign. Di seguito viene illustrato come aggiungere il dominio:

    a. Il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html) invia un token generato in modo casuale. Il token per il proprio dominio sarà simile al seguente: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Pubblicare il token di verifica in un record DNS TEXT e comunicarlo al [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > L'operazione può richiedere alcuni giorni. Eventuali ritardi nella propagazione dei record DNS indicano che un valore pubblicato nel DNS potrebbe non essere visibile per almeno un'ora. Si suppone che l'amministratore IT sappia come pubblicare questo token in un record DNS TEXT.
    
    c. Una volta avvisato il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html) tramite il ticket di supporto, dopo la pubblicazione del token, il dominio verrà convalidato e aggiunto al proprio account.
    
    d. Procedura da eseguire per pubblicare il token in un record DNS:

    * Accedere al proprio account di dominio.
    * Trovare la pagina per l'aggiornamento del record DNS. Questa pagina potrebbe essere denominata Gestione DNS, Gestione server dei nomi o Impostazioni avanzate.
    * Trovare i record TXT relativi al proprio dominio.
    * Aggiungere un record TXT con il valore completo del token fornito da Adobe.
    * Salvare le modifiche.

8. In un'altra finestra del Web browser accedere al sito aziendale di Adobe Sign come amministratore.

9. Nel menu SAML, selezionare **Impostazioni account** > **Impostazioni SAML**.
   
    ![Schermata della pagina delle impostazioni SAML di Adobe Sign](./media/adobe-echosign-tutorial/ic789520.png "Account")

10. Nella sezione **SAML Settings** (Impostazioni SAML), seguire questa procedura:
  
    ![Schermata delle impostazioni SAML](./media/adobe-echosign-tutorial/ic789521.png "Impostazioni SAML")
   
    a. Per **SAML Mode** (Modalità SAML), selezionare **SAML Mandatory** (SAML obbligatorio).
   
    b. Selezionare **Allow Echosign Account Administrators to log in using their Echosign Sign Credentials** (Consenti agli amministratori account di Echosign Sign di accedere con le credenziali di Echosign Sign).
   
    c. In **User Creation** (Creazione utente), selezionare **Automatically add users authenticated through SAML** (Aggiungi automaticamente gli utenti autenticati tramite SAML).

    d. Incollare il valore di **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure nella casella di testo **Entity ID/Issuer URL** (ID entità/URL autorità di certificazione).
    
    e. Incollare **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure nella casella di testo **Login URL/SSO Endpoint** (URL di accesso/Endpoint SSO).
   
    f. Incollare **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure nella casella di testo **Logout URL/SLO Endpoint** (URL di disconnessione/Endpoint SLO).

    g. Aprire il file del **certificato (Base64)** scaricato nel blocco note. Copiare il contenuto negli Appunti e quindi incollarlo nella casella di testo **IDP Certificate** (Certificato IDP).

    h. Selezionare **Save changes** (Salva modifiche).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
L'obiettivo di questa sezione consiste nel creare l'utente di test denominato Britta Simon nel portale di Azure.

![Schermata del nome dell'utente di test nel portale di Azure][100]

1. Nel **portale di Azure** fare clic sull'icona **Azure Active Directory** nel riquadro sinistro.

    ![Schermata dell'icona di Azure AD](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Per visualizzare l'elenco di utenti, passare a **Users and groups** (Utenti e gruppi) e selezionare **Tutti gli utenti**.
    
    ![Schermata dei menu di Azure AD, con evidenziati Utenti e gruppi e Tutti gli utenti](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Per aprire la finestra di dialogo **Utente**, fare clic su **Aggiungi**.
 
    ![Schermata della parte superiore della finestra di dialogo Tutti gli utenti con evidenziata l'opzione Aggiungi](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** seguire questa procedura:
 
    ![Schermata della finestra di dialogo Utente](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'indirizzo di posta elettronica di Britta Simon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Selezionare **Create**.
 
### <a name="create-an-adobe-sign-test-user"></a>Creare un utente di test di Adobe Sign

Per consentire agli utenti di Azure AD di accedere a Adobe Sign, è necessario eseguirne il provisioning in Adobe Sign. Si tratta di un processo manuale.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da Adobe Sign per eseguire il provisioning degli account utente di Azure AD. 

1. Accedere al sito aziendale di **Adobe Sign** come amministratore.

2. Nel menu in alto, selezionare **Account**. Quindi, nel riquadro sinistro, selezionare **Utenti e gruppi** > **Crea un nuovo utente**.
   
    ![Schermata del sito aziendale di Adobe Site, con evidenziate le opzioni relative all'account, agli utenti e ai gruppi, all'azienda e alla creazione di un nuovo utente](./media/adobe-echosign-tutorial/ic789524.png "Account")
   
3. Nella sezione **Create New User** seguire questa procedura:
   
    ![Schermata Crea nuovo utente](./media/adobe-echosign-tutorial/ic789525.png "Crea utente")
   
    a. Digitare l'**indirizzo di posta elettronica**, il **nome** e il **cognome** di un account utente di Azure AD valido di cui si vuole eseguire il provisioning.
   
    b. Selezionare **Crea utente**.

>[!NOTE]
>Il titolare dell'account di Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Adobe Sign.

![Schermata del portale di accesso Single Sign-On del portale di Azure][200] 

1. Nel portale di Azure aprire la visualizzazione applicazioni. Passare alla visualizzazione directory, accedere ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Schermata della vista delle applicazioni del portale di Azure, con evidenziate le applicazioni aziendali e tutte le applicazioni][201] 

2. Nell'elenco di applicazioni selezionare **Adobe Sign**.

    ![Schermata dell'elenco di applicazioni, con evidenziata Adobe Sign](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Schermata del menu con l'opzione Utenti e gruppi][202] 

4. Selezionare **Aggiungi**. Quindi nella sezione **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Schermata della pagina Utenti e gruppi e della sezione Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic su **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Quando si fa clic sul riquadro Adobe Sign nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adobe Sign. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/adobe-echosign-tutorial/tutorial_general_203.png
