---
title: 'Esercitazione: Integrazione di Azure Active Directory con IBM Kenexa Survey Enterprise | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e IBM Kenexa Survey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 929ae8b780c0e18074832adfefb9a7674097fd08
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con IBM Kenexa Survey Enterprise

Questa esercitazione descrive come integrare IBM Kenexa Survey Enterprise con Azure Active Directory (Azure AD).

L'integrazione di IBM Kenexa Survey Enterprise con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a IBM Kenexa Survey Enterprise.
- È possibile abilitare gli utenti per l'accesso automatico a IBM Kenexa Survey Enterprise mediante Single Sign-On (SSO) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service, software come un servizio) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con IBM Kenexa Survey Enterprise, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione IBM Kenexa Survey Enterprise abilitata per l'accesso SSO

> [!NOTE]
> Per testare i passaggi di questa esercitazione non è consigliabile usare un ambiente di produzione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene testato l'accesso SSO di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

* Aggiunta di IBM Kenexa Survey Enterprise dalla raccolta
* Configurazione e test dell'accesso SSO di Azure AD

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Aggiungere IBM Kenexa Survey Enterprise dalla raccolta
Per configurare l'integrazione di IBM Kenexa Survey Enterprise in Azure AD, aggiungere IBM Kenexa Survey Enterprise dalla raccolta al proprio elenco di app SaaS gestite.

Per aggiungere IBM Kenexa Survey Enterprise dalla raccolta, eseguire queste operazioni:

1. Nel [portale di Azure](https://portal.azure.com) fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere un'applicazione fare clic sul pulsante **Nuova applicazione**.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **IBM Kenexa Survey Enterprise**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. Nell'elenco risultati selezionare **IBM Kenexa Survey Enterprise** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![IBM Kenexa Survey Enterprise nell'elenco risultati](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso SSO di Azure AD con IBM Kenexa Survey Enterprise con un utente test di nome "Britta Simon".

Affinché l'accesso SSO funzioni correttamente, Azure AD deve identificare l'utente di IBM Kenexa Survey Enterprise che corrisponde a un utente di Azure AD. In altre parole, Azure AD deve stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in IBM Kenexa Survey Enterprise.

Per stabilire la relazione di collegamento, assegnare il valore del **nome utente** in IBM Kenexa Survey Enterprise come valore di **Username** (Nome utente) in Azure AD.

Per configurare e testare l'accesso SSO di Azure AD con IBM Kenexa Survey Enterprise completare i blocchi predefiniti nelle prossime due sezioni.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso SSO di Azure AD nel portale di Azure e viene configurato l'accesso SSO nell'applicazione IBM Kenexa Survey Enterprise seguendo questa procedura:

1. Nella pagina di integrazione dell'applicazione **IBM Kenexa Survey Enterprise** del portale di Azure fare clic su **Single Sign-On**.

    ![IBM Kenexa Survey Enterprise Configurazione collegamento Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** nella casella **Modalità** per abilitare l'accesso SSO.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. Nella sezione **IBM Kenexa Survey Enterprise Domain and URLs** (URL e dominio IBM Kenexa Survey Enterprise) seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di IBM Kenexa Survey Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL usando il modello seguente: `https://surveys.kenexa.com/<companycode>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > I valori precedenti non sono valori reali. Aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere i valori effettivi, contattare il [team di supporto di IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

4. In **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato (Base64)](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    L'applicazione IBM Kenexa Survey Enterprise prevede la ricezione di asserzioni SAML (Security Assertions Markup Language) in un formato specifico. È necessario quindi aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Il valore dell'attestazione dell'identificatore utente nella risposta deve corrispondere all'ID di SSO configurato nel sistema Kenexa. Per eseguire il mapping dell'identificatore utente appropriato all'interno dell'organizzazione come IDP (Internet Datagram Protocol) SSO Internet collaborare con il [team di supporto di IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). 

    Per impostazione predefinita, Azure AD imposta l'identificatore utente come valore del nome dell'entità utente (UPN). È possibile modificare questo valore dalla scheda **Attributo** come illustrato nella schermata seguente. L'integrazione funziona solo dopo aver eseguito correttamente il mapping.
    
    ![Finestra di dialogo Attributi utente](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. Fare clic su **Save**.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. Per aprire la finestra **Configura accesso** in **IBM Kenexa Survey Enterprise Configuration** (Configurazione di IBM Kenexa Survey Enterprise) fare clic su **Configure IBM Kenexa Survey Enterprise** (Configura IBM Kenexa Survey Enterprise). 
 
    ![Collegamento Configure IBM Kenexa sondaggio Enterprise (Configura IBM Kenexa Survey Enterprise)](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. Copiare i valori **Sign-Out URL** (URL disconnessione), **SAML Entity ID** (ID di entità SAML) e **SAML single sign-on Service URL** (URL del servizio Single Sign-On SAML) dalla sezione **Riferimento rapido**.

8. Nella finestra **Configura accesso** in **Riferimento rapido** copiare i valori **Sign-Out URL** (URL disconnessione), **SAML Entity ID** (ID di entità SAML) e **SAML single sign-on Service URL** (URL del servizio Single Sign-On SAML).

9. Per configurare l'accesso SSO sul lato **IBM Kenexa Survey Enterprise** inviare i valori del file **Certificato (Base64)**, di **Sign-Out URL** (URL di disconnessione), di **SAML Entity ID** (ID di entità SAML) e di **SAML single sign-on Service URL** (URL del servizio Single Sign-On SAML) al [team di supporto di IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> È possibile consultare un riepilogo delle istruzioni all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** alla fine. Per altre informazioni sulla funzione di documentazione incorporata vedere [Azure AD embedded documentation](https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
In questa sezione si crea un utente di test Britta Simon nel portale di Azure seguendo questa procedura:

![Creare un utente test di Azure AD][100]

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.
    
    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.
 
    ![Pulsante Aggiungi](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo Utente](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Creare un utente test di IBM Kenexa Survey Enterprise

In questa sezione viene creato un utente chiamato Britta Simon in IBM Kenexa Survey Enterprise. 

Per creare gli utenti nel sistema IBM Kenexa Survey Enterprise ed eseguire il mapping del relativo ID SSO, è possibile collaborare con il [team di supporto di IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Per questo valore di ID SSO dovrà anche essere eseguito il mapping al valore Identificatore utente ottenuto da Azure AD. È possibile modificare questa impostazione predefinita nella scheda **Attributo**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita l'utente Britta Simon per l'uso dell'accesso SSO di Azure concedendole l'accesso a IBM Kenexa Survey Enterprise.

![Assegnare il ruolo utente][200] 

Per assegnare Britta Simon a IBM Kenexa Survey Enterprise, seguire questa procedura:

1. Nel portale di Azure aprire la visualizzazione **Applicazioni**, passare alla visualizzazione **Directory**, selezionare **Applicazioni aziendali** e quindi fare clic su **Tutte le applicazioni**.

    ![Collegamenti "Applicazioni aziendali" e "Tutte le applicazioni"][201] 

2. Nell'elenco **Applicazioni** selezionare **IBM Kenexa Survey Enterprise**.

    ![Collegamento IBM Kenexa Survey Enterprise nell'elenco Applicazioni](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][202] 

4. Fare clic sul pulsante **Aggiungi** e quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso SSO di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro **IBM Kenexa Survey Enterprise** nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione IBM Kenexa Survey Enterprise.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 
