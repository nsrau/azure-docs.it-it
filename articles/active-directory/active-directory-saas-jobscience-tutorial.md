---
title: 'Esercitazione: Integrazione di Azure Active Directory con Jobscience | Documentazione Microsoft'
description: Informazioni su come usare Jobscience con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 53fc622aa659f157f82dd27c5d6d2d8c9d8cc94b
ms.openlocfilehash: c881e28adf09440e329ddcae366ca787c868502b
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Esercitazione: Integrazione di Azure Active Directory con Jobscience
Questa esercitazione descrive l'integrazione di Azure e Jobscience.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Jobscience abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Jobscience potranno accedere all'applicazione tramite il sito aziendale di Jobscience (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Jobscience
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scenario")

## <a name="enable-the-application-integration-for-jobscience"></a>Abilitare l'integrazione dell'applicazione per Jobscience
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Jobscience.

**Per abilitare l'integrazione dell'applicazione per Jobscience, seguire questa procedura:**
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **jobscience**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Jobscience** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Jobscience tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  

La configurazione dell'accesso Single Sign-On per Jobscience richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Accedere al sito aziendale di Jobscience come amministratore.
2. Passare a **Setup**.
   
   ![Installazione](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Installazione")
3. Nella sezione **Administer** (Amministra) del riquadro di spostamento sinistro fare clic su **Domain Management** (Gestione dominio) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale) per aprire la pagina **My Domain** (Dominio personale). 
   
   ![My Domain](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")
4. Per verificare che il dominio sia stato configurato correttamente, verificare che sia presente in "**Step 4 Deployed to Users**" (Passaggio 4 Distribuzione a utenti) e quindi verificare le selezioni in "**My Domain Settings**" (Impostazioni dominio personale).
   
   ![Doman Deployed to User](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Doman Deployed to User")
5. In una finestra diversa del Web browser accedere al portale di Azure classico.
6. Nella pagina di integrazione dell'applicazione **Jobscience** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configurare l'accesso Single Sign-On")
7. Nella pagina **Stabilire come si desidera che gli utenti accedano a Jobscience** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configurare l'accesso Single Sign-On")
8. Nella casella di testo **URL di accesso Jobscience** della pagina **Configure App URL** (Configura URL app) digitare l'URL usando il modello "*http://company.my.salesforce.com*" e fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configurare l'URL dell'app")
9. Nella pagina **Configura accesso Single Sign-On in Jobscience** fare clic su **Scarica certificato** e quindi salvare il file del certificato nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configurare l'accesso Single Sign-On")
10. Nel sito aziendale Jobscience fare clic su **Security Controls** (Controlli di sicurezza) e quindi fare clic su **Single Sign-On Settings** (Impostazioni Single Sign-On).
    
    ![Security Controls](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Security Controls")
11. Nella sezione **Single Sign-On Settings** , eseguire la procedura seguente:
    
    ![Impostazioni di Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Impostazioni di Single Sign-On")
    
    1. Selezionare **Abilitato SAML**.
    2. Fare clic su **New**.
12. Nella finestra di dialogo **SAML Single Sign-On Setting Edit** seguire questa procedura:
    
    ![SAML Single Sign-On Setting](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML Single Sign-On Setting")
    
    1. Nella casella di testo **Name** digitare un nome per la configurazione.
    2. Nella finestra di dialogo **Configura accesso Single Sign-On in Jobscience** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Issuer** (Autorità di certificazione).
    3. Nella casella di testo **ID entità** digitare **https://salesforce-jobscience.com**.
    4. Fare clic su **Browse** per caricare il certificato di Azure AD.
    5. In **SAML Identity Type** (Tipo di identità SAML) selezionare **Assertion contains the Federation ID from the User object** (L'asserzione contiene l'ID federazione dell'oggetto utente).
    6. In **SAML Identity Location** (Percorso identità SAML) selezionare **Identity is in the NameIdentfier element of the Subject statement** (L'identità è nell'elemento NameIdentfier dell'istruzione Subject).
    7. Nella finestra di dialogo **Configura accesso Single Sign-On in Jobscience** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Identity Provider Login URL** (URL di accesso provider di identità).
    8. Nella finestra di dialogo **Configura accesso Single Sign-On in Jobscience** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Identity Provider Logout URL** (URL di disconnessione provider di identità).
    9. Fare clic su **Save**.
13. Nella sezione **Administer** (Amministra) del riquadro di spostamento sinistro fare clic su **Domain Management** (Gestione dominio) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale) per aprire la pagina **My Domain** (Dominio personale). 
    
    ![My Domain](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")
14. Nella sezione **Login Page Branding** (Personalizzazione pagina di accesso) della pagina **My Domain** (Dominio personale) fare clic su **Edit** (Modifica).
    
    ![Login Page Branding](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Login Page Branding")
15. Nella sezione**Authentication Service** (Servizio autenticazione) della pagina **Login Page Branding** (Personalizzazione pagina di accesso) viene visualizzato il nome delle **impostazioni SAML SSO**. Selezionarlo e quindi fare clic su **Save**.
    
    ![Login Page Branding](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Login Page Branding")
16. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configurare l'accesso Single Sign-On")

Per ottenere l'URL di accesso Single Sign-On avviato dal provider di servizi, fare clic su **Single Sign-On Settings** (Impostazioni Single Sign-On) nella sezione di menu **Security Controls** (Controlli di sicurezza).

![Security Controls](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Security Controls")

Fare clic sul profilo SSO creato nel passaggio precedente. Questa pagina mostra l'URL di accesso Single Sign-On dell'azienda, ad esempio *https://companyname.my.salesforce.com?so=companyid*.

## <a name="configure-user-provisioning"></a>Configura provisioning utenti
Per consentire agli utenti di Azure AD di accedere a Jobscience, è necessario eseguirne il provisioning in Jobscience.  
Nel caso di Jobscience, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **Jobscience** come amministratore.
2. Passare a Setup (Installazione).
   
   ![Installazione](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Installazione")
3. Passare a **Manage Users (Gestisci utenti) \> Users (Utenti)**.
   
   ![Utenti](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Utenti")
4. Fare clic su **Nuovo utente**.
   
   ![Tutti gli utenti](./media/active-directory-saas-jobscience-tutorial/IC784370.png "Tutti gli utenti")
5. Nella finestra di dialogo **Edit User** seguire la procedura seguente:
   
   ![Modifica dell'utente](./media/active-directory-saas-jobscience-tutorial/IC784371.png "Modifica dell'utente")
   
   1. Immettere nelle caselle di testo correlate le proprietà relative a nome, cognome, alias, indirizzo di posta elettronica, nome utente e nome alternativo dell'utente di Azure AD di cui si vuole eseguire il provisioning.
   2. Fare clic su **Save**.
   
   >[!NOTE]
   >Il titolare dell'account AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.
   > 

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Jobscience per eseguire il provisioning degli account utente di AAD.
>  

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Jobscience, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Jobscience** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


