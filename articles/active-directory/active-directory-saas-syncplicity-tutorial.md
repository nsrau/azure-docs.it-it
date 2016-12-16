---
title: 'Esercitazione: Integrazione di Azure Active Directory con Syncplicity | Microsoft Docs'
description: Informazioni su come usare Syncplicity con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d8464b1ca5051b2bdac61f0e637ed1b2ff3df3ab


---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Esercitazione: Integrazione di Azure Active Directory con Syncplicity
L'obiettivo di questa esercitazione è di illustrare come impostare il single sign-on tra Azure Active Directory (Azure AD) e Syncplicity.

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Syncplicity

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Syncplicity saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Syncplicity (accesso avviato dal provider di servizi) o tramite il pannello di accesso ad Azure AD.

1. Abilitazione dell'integrazione dell'applicazione per Syncplicity
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Scenario")

## <a name="enabling-the-application-integration-for-syncplicity"></a>Abilitazione dell'integrazione dell'applicazione per Syncplicity
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Syncplicity.

### <a name="to-enable-the-application-integration-for-syncplicity-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Syncplicity, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Syncplicity**.
   
   ![Raccolta di applicazioni Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Syncplicity application gallery")
7. Nel riquadro dei risultati selezionare **Syncplicity** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Syncplicity tramite il relativo account in Azure Active Directory utilizzando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Syncplicity** del portale di Azure classico, fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura Accesso Single Sign On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Syncplicity** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Microsoft Azure AD Single Sign-On")
3. Nella casella di testo **URL di accesso a Syncplicity** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Syncplicity e fare clic su **Avanti**. 
   
   L'URL dell'app è l'URL del tenant Syncplicity (ad esempio, *http://company.Syncplicity.com*):
   
   ![Configura URL app](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configure app URL")
4. Nella pagina **Configura Single Sign-On per Syncplicity** fare clic su **Scarica certificato** per scaricare il certificato e quindi salvare il file del certificato localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Configure single sign-on")
5. Accedere al tenant **Syncplicity** .
6. Nel menu nella parte superiore fare clic su **admin** (amministrazione), selezionare **settings** (impostazioni) e quindi fare clic su **Custom domain and single sign-on** (Dominio personalizzato e Single Sign-On).
   
   ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")
7. Nella pagina finestra di dialogo **Single Sign-On (SSO)** , eseguire la procedura seguente:
   
   ![Single Sign-On \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Single Sign-On \\\(SSO\\\)")
   
   1. Nella casella di testo **Custom Domain** , digitare il nome del dominio.
   2. Selezionare **Enabled** (Abilitato) come **Single Sign-On Status** (Stato Single Sign-On).
   3. Nella pagina **Configura accesso Single Sign-On in Syncplicity** del portale di Azure classico copiare il valore **ID entità** e incollarlo nella casella di testo **Entity Id** (ID entità).
   4. Nella pagina **Configura accesso Single Sign-On in Syncplicity** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **Sign-in page URL** (URL pagina di accesso).
   5. Nella pagina **Configura accesso Single Sign-On in Syncplicity** del portale di Azure classico copiare il valore **URL disconnessione remota** e incollarlo nella casella di testo **Logout page URL** (URL pagina di disconnessione).
   6. In **Certificato Provider di identità** fare clic su **Scegli file**, quindi caricare il certificato scaricato dal portale di Azure classico.
   7. Fare clic su **Salva modifiche**.
8. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Conferma](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Confirmation")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per essere in grado di effettuare l’accesso, è necessario effettuare il provisioning degli utenti AAD all’applicazione Syncplicity. In questa sezione viene descritto come creare gli account utente AAD in Syncplicity.

### <a name="to-provision-a-user-account-to-syncplicity-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al tenant **Syncplicity** (ad esempio, *https://company.Syncplicity.com*).
2. Fare clic su **Admin** (Amministrazione) e selezionare **user accounts** (account utente).
3. Fare clic su **Add a user**.
   
   ![Gestire gli utenti](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Manage Users")
4. Digitare l'**indirizzo di posta elettronica** di un account AAD di cui si vuole effettuare il provisioning, selezionare **Utente** come **Ruolo** e quindi fare clic su **Avanti**.
   
   ![Informazioni account](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Account Information")
   
   > [!NOTE]
   > Il titolare dell’account AAD riceverà un messaggio di posta elettronica con un collegamento per confermare e attivare l'account.
   > 
   > 
5. Selezionare, nell’azienda, un gruppo di cui il nuovo utente dovrebbe diventare membro, quindi fare clic su **Avanti**.
   
   ![Appartenenza al gruppo](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Group Membership")
   
   > [!NOTE]
   > Se non sono elencati gruppi, fare clic su **Avanti**.
   > 
   > 
6. Selezionare le cartelle che si desidera inserire nel controllo di Syncplicity nel computer dell'utente, quindi fare clic su **Avanti**.
   
   ![Cartella Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Syncplicity Folders")

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione account utente o API fornita da Syncplicity per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-syncplicity-perform-the-following-steps"></a>Per assegnare gli utenti a Syncplicity, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Syncplicity** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


