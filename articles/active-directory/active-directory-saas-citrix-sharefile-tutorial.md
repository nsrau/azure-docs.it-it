---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile | Microsoft Docs'
description: Informazioni su come usare Citrix ShareFile con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 30f5ed9a5e9371193ef7663cdd8896793ca6359b


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile
Questa esercitazione descrive l'integrazione di Azure e Citrix ShareFile.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Citrix ShareFile

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Citrix ShareFile potranno accedere all'applicazione tramite il sito aziendale di Citrix ShareFile (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Citrix ShareFile
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenario")

## <a name="enabling-the-application-integration-for-citrix-sharefile"></a>Abilitazione dell'integrazione dell'applicazione per Citrix ShareFile
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Citrix ShareFile.

### <a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Citrix ShareFile, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Citrix ShareFile**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Application gallery")
7. Nel riquadro dei risultati selezionare **Citrix ShareFile** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Citrix ShareFile tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Citrix ShareFile** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Abilita Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Enable single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Citrix ShareFile** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configure Single Sign-On")
3. Nella casella di testo **URL di accesso Citrix ShareFile** della pagina **Configura URL** app digitare l'URL usando il modello seguente `https://<tenant-name>.shareFile.com` e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Citrix ShareFile** fare clic su **Scarica certificato** per scaricare il file di certificato e quindi salvarlo nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di **Citrix ShareFile** come amministratore.
6. Nel barra degli strumenti in alto fare clic su **Admin**.
7. Nel riquadro di spostamento sinistro selezionare **Configure Single Sign-On**.
   
   ![Account Administration](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Account Administration")
8. In **Basic Settings** della finestra di dialogo **Single Sign-On/ SAML 2.0 Configuration** seguire questa procedura:
   
   ![Single sign-on](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Single sign-on")
   
   1. Fare clic su **Enable SAML**.
   2. Nella finestra di dialogo **Configure single sign-on at Citrix ShareFile** (Configura accesso Single Sign-On in Citrix ShareFile) del portale di Azure classico copiare il valore di **ID entità** e incollarlo nella casella di testo **Your IDP Issuer/ Entity ID** (Autorità di certificazione IDP/ID entità).
   3. Nella finestra di dialogo **Configure single sign-on at Citrix ShareFile** (Configura accesso Single Sign-On in Citrix ShareFile) del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Login URL** (URL di accesso).
   4. Nella finestra di dialogo **Configure single sign-on at Citrix ShareFile** (Configura accesso Single Sign-On in Citrix ShareFile) del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Logout URL** (URL di disconnessione).
   5. Fare clic su **Cambia** accanto al campo **Certificato X.509** e quindi caricare il certificato scaricato dal portale di Azure classico.
      ![Single Sign-On/ SAML 2.0 Configuration](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Basic Settings")
9. Fare clic su **Save** nel portale di gestione di Citrix ShareFile.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Citrix ShareFile, è necessario eseguirne il provisioning in Citrix ShareFile.  
Nel caso di Citrix ShareFile, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:
1. Accedere al tenant di **Citrix ShareFile** .
2. Fare clic su **Manage Users \> Manage Users Home \> + Create Employee** (Gestisci utenti > Gestisci home utenti > + Crea dipendente).
   
   ![Create Employee](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Create Employee")
3. Nelle caselle di testo **Email**, **First name** e **Last name** digitare rispettivamente l'indirizzo di posta elettronica, il nome e il cognome relativi a un account Azure AD valido di cui si vuole eseguire il provisioning.
   
   ![Basic Information](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Basic Information")
4. Fare clic su **Add User**.
   
   > [!NOTE]
   > Il titolare dell'account AAD riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
   > 
   > 

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Citrix ShareFile per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>Per assegnare gli utenti a Citrix ShareFile, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Citrix ShareFile** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


