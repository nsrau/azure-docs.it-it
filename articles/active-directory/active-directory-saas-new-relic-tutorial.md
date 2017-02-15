---
title: 'Esercitazione: Integrazione di Azure Active Directory con New Relic | Documentazione Microsoft'
description: Informazioni su come utilizzare New Relic con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c7c7542454bab5e5c2fae88aeefa1a981ee165ff


---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Esercitazione: Integrazione di Azure Active Directory con New Relic
In questa esercitazione viene illustrato come configurare l’accesso Single Sign-On tra Azure Active Directory e New Relic.

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di New Relic abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure Active Directory assegnati a New Relic saranno in grado di eseguire l’accesso Single Sign-On tramite il pannello di accesso di AAD.

1. Abilitazione dell'integrazione dell'applicazione per New Relic
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scenario")

## <a name="enabling-the-application-integration-for-new-relic"></a>Abilitazione dell'integrazione dell'applicazione per New Relic
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per New Relic.

### <a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per New Relic, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-new-relic-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-new-relic-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **New Relic**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-new-relic-tutorial/IC797031.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **New Relic**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a New Relic tramite il relativo account in Azure Active Directory utilizzando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **New Relic** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a New Relic** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configure Single Sign-On")
3. Nella casella di testo **URL di accesso** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione New Relic, quindi fare clic su **Avanti**. 
   
   L'URL dell'app è l'URL del tenant New Relic, ad esempio: *https://rpm.newrelic.com*:
   
   ![Configura URL app](./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in New Relic** fare clic su **Scarica certificato** per scaricare il file del certificato, quindi salvarlo localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di **New Relic** come amministratore.
6. Nel menu in alto fare clic su **Impostazioni account**.
   
   ![Impostazioni account](./media/active-directory-saas-new-relic-tutorial/IC797036.png "Account Settings")
7. Fare clic sulla scheda **Security and authentication** (Sicurezza e autenticazione), quindi fare clic sulla scheda **Single sign on**.
   
   ![Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")
8. Nella pagina della finestra di dialogo SAML eseguire la procedura seguente:
   
   ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")
   
   1. Fare clic su **Choose File** per caricare il certificato di Azure Active Directory scaricato.
   2. Nella pagina **Configura accesso Single Sign-On in New Relic** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Remote login URL** (URL accesso remoto).
   3. Nella pagina **Configura accesso Single Sign-On in New Relic** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Logout landing URL** (URL di destinazione di disconnessione).
   4. Fare clic su **Save my changes**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure Active Directory di accedere a New Relic, è necessario eseguirne il provisioning in New Relic.  
Nel caso di New Relic, il provisioning è un’attività manuale.

### <a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente in New Relic, eseguire la procedura seguente:
1. Accedere al sito aziendale di **New Relic** come amministratore.
2. Nel menu in alto fare clic su **Impostazioni account**.
   
   ![Impostazioni account](./media/active-directory-saas-new-relic-tutorial/IC797040.png "Account Settings")
3. Nel riquadro **Account** a sinistra fare clic su **Summary** (Riepilogo), quindi su **Add user** (Aggiungi utente).
   
   ![Impostazioni account](./media/active-directory-saas-new-relic-tutorial/IC797041.png "Account Settings")
4. Nella finestra di dialogo **Active users** eseguire la procedura seguente:
   
   ![Active users](./media/active-directory-saas-new-relic-tutorial/IC797042.png "Active Users")
   
   1. Nella casella di testo **Posta elettronica** digitare l'indirizzo di posta elettronica di un utente valido di Azure Active Directory di cui si desidera eseguire il provisioning.
   2. Come **Role** (Ruolo) selezionare **User** (Utente).
   3. Fare clic su **Add this user**.

> [!NOTE]
> È possibile utilizzare qualsiasi altro strumento di creazione di account utente di New Relic o le API fornite da New Relic per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Per assegnare gli utenti a New Relic eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **New Relic** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-new-relic-tutorial/IC797043.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-new-relic-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


