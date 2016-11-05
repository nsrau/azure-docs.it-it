---
title: 'Esercitazione: Integrazione di Azure Active Directory con RunMyProcess | Microsoft Docs'
description: Informazioni su come usare RunMyProcess con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-runmyprocess"></a>Esercitazione: Integrazione di Azure Active Directory con RunMyProcess
In questa esercitazione viene illustrata l'integrazione di Azure e RunMyProcess.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant RunMyProcess

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a RunMyProcess saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di RunMyProcess (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per RunMyProcess
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "Scenario")

## <a name="enabling-the-application-integration-for-runmyprocess"></a>Abilitazione dell'integrazione dell'applicazione per RunMyProcess
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per RunMyProcess.

### <a name="to-enable-the-application-integration-for-runmyprocess,-perform-the-following-steps:"></a>Per abilitare l'integrazione dell'applicazione per RunMyProcess, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **RunMyProcess**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **RunMyProcess**, quindi fare clic su **Complete** per aggiungere l'applicazione.
   
   ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a RunMyProcess tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **RunMyProcess** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a RunMyProcess** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Configure Single Sign-On")
3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **RunMyProcess Sign On URL** (URL di accesso a RunMyProcess) usando il modello "*http://company.runmyprocess.com*" e fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in RunMyProcess** fare clic su **Scarica certificato**, quindi salvare il file del certificato nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di RunMyProcess come amministratore.
6. Passare ad **Account \> Configurazione**.
   
   ![Account](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "Account")
7. Fare clic sulla scheda **Metodo di autenticazione** .
8. Nella sezione **Metodo di autenticazione** , eseguire la procedura seguente:
   
   ![SSO](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")
   
   1. In **Metodo** selezionare **SSO con Samlv2**.
   2. Nella finestra di dialogo **Configure single sign-on at RunMyProcess** (Configura accesso Single Sign-On in RunMyProcess) del portale di Azure classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **SSO redirect** (Reindirizzamento SSO).
   3. Nella finestra di dialogo **Configure single sign-on at RunMyProcess** (Configura accesso Single Sign-On in RunMyProcess) del portale di Azure classico copiare il valore di **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Logout redirect** (Reindirizzamento disconnessione).
   4. Nella casella di testo **Formato ID nome** digitare **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
   5. Creare un file con **codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato** .
   7. Fare clic su **Save**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a RunMyProcess, è necessario eseguirne il provisioning in RunMyProcess.  
Nel caso di RunMyProcess, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts,-perform-the-following-steps:"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **RunMyProcess** come amministratore.
2. Passare ad **Account \> Utenti**, quindi fare clic su **Nuovo utente**.
   
   ![Nuovo utente](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "New User")
3. Nella sezione **Impostazioni utente** , eseguire la procedura seguente:
   
   ![Profilo](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "Profile")
   
   1. Digitare il **nome** e l'**indirizzo di posta elettronica** di un account di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
   2. Selezionare una **lingua per l'IDE**, una **lingua** e un **profilo**.
   3. Selezionare **Inviami un messaggio di posta elettronica di creazione account**.
   4. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da RunMyProcess per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-runmyprocess,-perform-the-following-steps:"></a>Per assegnare gli utenti a RunMyProcess, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **RunMyProcess** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


