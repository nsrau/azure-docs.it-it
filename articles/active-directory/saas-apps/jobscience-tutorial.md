---
title: 'Esercitazione: Integrazione di Azure Active Directory con Jobscience | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d2694aa4936090367cf881379f81911ae70f9d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870531"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Esercitazione: Integrazione di Azure Active Directory con Jobscience

Questa esercitazione descrive come integrare Jobscience con Azure Active Directory (Azure AD).

L'integrazione di Jobscience con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Jobscience
- È possibile abilitare gli utenti per l'accesso automatico a Jobscience (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione delle app SaaS con Azure AD, vedere [che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory.](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Jobscience, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Jobscience abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di valutazione di Azure AD, è possibile ottenere una versione di valutazione di un mese qui: Offerta di [valutazione](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Jobscience dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Aggiunta di Jobscience dalla raccolta
Per configurare l'integrazione di Jobscience in Azure AD, è necessario aggiungere Jobscience dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Jobscience dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Jobscience**.

    ![Creazione di un utente di test di Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Nel pannello dei risultati selezionare **Jobscience** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Jobscience in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di Jobscience corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Jobscience.

Per stabilire la relazione di collegamento, in Jobscience assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Jobscience, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** per consentire agli utenti di usare questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di Jobscience](#creating-a-jobscience-test-user)**: per avere una controparte di Britta Simon in Jobscience collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** - per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Jobscience.

**Per configurare l'accesso Single Sign-On di Azure AD con Jobscience, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Jobscience** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Modalità** come **accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Nella sezione **URL e dominio Jobscience** seguire questa procedura:

    ![Configure Single Sign-On](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Nella casella di testo **URL di accesso** digitare un URL utilizzando il modello seguente:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Ottenere questo valore dal [team di supporto clienti di Jobscience](http://www.jobscience.com/support) o dal profilo SSO che verrà creato come descritto più avanti nell'esercitazione. 
 
1. Nella sezione Certificato di **firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/jobscience-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Jobscience** fare clic su **Configura Jobscience** per visualizzare la finestra **Configura accesso**. Copiare **l'URL di disconnessione, l'ID entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configure Single Sign-On](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Accedere al sito aziendale di Jobscience come amministratore.

1. Passare a **Setup**.
   
   ![Configurazione](./media/jobscience-tutorial/IC784358.png "Installazione")

1. Nella sezione **Administer** (Amministra) del riquadro di spostamento sinistro fare clic su **Domain Management** (Gestione dominio) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale) per aprire la pagina **My Domain** (Dominio personale). 
   
   ![My Domain](./media/jobscience-tutorial/ic767825.png "My Domain")

1. Per verificare che il dominio sia stato configurato correttamente, assicurarsi che sia nel "**Passaggio 4 distribuito agli utenti**" ed esaminare le "**Impostazioni del dominio**personale ".

    ![Dominio distribuito all'utente](./media/jobscience-tutorial/ic784377.png "Dominio distribuito all'utente")

1. Nel sito della società Jobscience company fare clic su **Security Controls** e quindi su **Single Sign-On Settings**.
    
    ![Security Controls](./media/jobscience-tutorial/ic784364.png "Security Controls")

1. Nella sezione **Single Sign-On Settings** eseguire la procedura seguente:
    
    ![Single Sign-On Settings](./media/jobscience-tutorial/ic781026.png "Single Sign-On Settings")
    
    a. Selezionare **SAML Enabled**.

    b. Fare clic su **New**.

1. Nella pagina **SAML Single Sign-On Setting Edit** eseguire la procedura seguente:
    
    ![SAML Single Sign-On Setting](./media/jobscience-tutorial/ic784365.png "SAML Single Sign-On Setting")
    
    a. Nella casella di testo **Name** digitare un nome per la configurazione.

    b. Nella casella di testo **Issuer** (Autorità emittente) incollare il valore dell'**ID entità SAML** copiato dal portale di Azure.

    c. Nella casella di testo **Entity Id** (ID entità) digitare `https://salesforce-jobscience.com`

    d. Fare clic su **Browse** per caricare il certificato di Azure AD.

    e. In **SAML Identity Type**, selezionare **Assertion contains the Federation ID from the User object**.

    f. In **SAML Identity Location** selezionare **Identity is in the NameIdentfier element of the Subject statement**.

    g. Nella casella di testo **Identity Provider Login URL** (URL accesso provider di identità) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    h. Nella casella di testo **Identity Provider Logout URL** (URL disconnessione provider di identità) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    i. Fare clic su **Salva**.

1. Nella sezione **Administer** (Amministra) del riquadro di spostamento sinistro fare clic su **Domain Management** (Gestione dominio) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale) per aprire la pagina **My Domain** (Dominio personale). 
    
    ![My Domain](./media/jobscience-tutorial/ic767825.png "My Domain")

1. Nella sezione **Login Page Branding** (Personalizzazione pagina di accesso) della pagina **My Domain** (Dominio personale) fare clic su **Edit** (Modifica).
    
    ![Login Page Branding](./media/jobscience-tutorial/ic767826.png "Login Page Branding")

1. Nella sezione**Authentication Service** (Servizio autenticazione) della pagina **Login Page Branding** (Personalizzazione pagina di accesso) viene visualizzato il nome delle **impostazioni SAML SSO**. Selezionarlo e quindi fare clic su **Save**.
    
    ![Login Page Branding](./media/jobscience-tutorial/ic784366.png "Login Page Branding")

1. Per ottenere l'URL di accesso Single Sign-On inizializzato da provider di servizi, fare clic su **Single Sign-On Settings** nella sezione **Security Controls** del menu.

    ![Security Controls](./media/jobscience-tutorial/ic784368.png "Security Controls")
    
    Fare clic sul profilo SSO creato nel passaggio precedente. Questa pagina mostra l'URL di accesso Single Sign-On della società, ad esempio `https://companyname.my.salesforce.com?so=companyid`.    

> [!TIP]
> È ora possibile leggere una versione concisa di queste istruzioni all'interno del portale di [Azure](https://portal.azure.com)durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure AD
Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure**fare clic sull'icona **di Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente di test di Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente di test di Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente di test di Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente di test di Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e annotare il valore della **password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-jobscience-test-user"></a>Creazione di un utente test di Jobscience

Per consentire agli utenti di Azure AD di accedere a Jobscience, è necessario eseguirne il provisioning in Jobscience. Nel caso di Jobscience, il provisioning è un'attività manuale.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerta da Jobscience per eseguire il provisioning degli account utente di Azure Active Directory.
>  
        
**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito della società **Jobscience** come amministratore.

1. Passare a Setup (Installazione).
   
   ![Configurazione](./media/jobscience-tutorial/ic784358.png "Installazione")
1. Passare a **Manage Users (Gestisci utenti) \> Users (Utenti)**.
   
   ![Utenti](./media/jobscience-tutorial/ic784369.png "Utenti")
1. Fare clic su **New User**.
   
   ![Tutti gli utenti](./media/jobscience-tutorial/ic784370.png "All Users")
1. Nella finestra di dialogo **Edit user** eseguire la procedura seguente:
   
   ![User Edit](./media/jobscience-tutorial/ic784371.png "User Edit")
   
   a. Nella casella di testo **First Name** (Nome) digitare il nome dell'utente, ad esempio Britta.
   
   b. Nella casella **Last Name** (Cognome) digitare il cognome dell'utente, ad esempio Simon.
   
   c. Nella casella di testo **Alias** digitare un nome alias dell'utente, ad esempio brittas.

   d. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

   e. Nella casella di testo **User Name** (Nome utente) digitare il nome utente dell'utente, ad esempio Brittasimon@contoso.com.

   f. Nella casella di testo **Nick Name** (Nome alternativo) digitare un nome alternativo dell'utente, ad esempio Simon.

   g. Fare clic su **Salva**.

    
> [!NOTE]
> Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Jobscience.

![Assegna utente][200] 

**Per assegnare Britta Simon a Jobscience, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni, quindi passare alla visualizzazione directory e passare ad **Applicazioni aziendali,** quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **Jobscience**.

    ![Configure Single Sign-On](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Jobscience nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Jobscience.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

