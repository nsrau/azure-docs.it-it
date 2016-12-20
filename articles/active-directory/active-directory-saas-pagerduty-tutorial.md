---
title: 'Esercitazione: integrazione di Azure Active Directory con Pagerduty | Documentazione Microsoft'
description: Informazioni su come utilizzare Pagerduty con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b473d7edb531f4fdab9c2ea8bde969e37558072


---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Esercitazione: integrazione di Azure Active Directory con Pagerduty
In questa esercitazione viene illustrata l'integrazione di Azure e Pagerduty.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Pagerduty

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Pagerduty saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Pagerduty (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Pagerduty
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenario")

## <a name="enabling-the-application-integration-for-pagerduty"></a>Abilitazione dell'integrazione dell'applicazione per Pagerduty
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Pagerduty.

### <a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Pagerduty eseguire la procedura seguente:
1. Nel portale di gestione di Azure fare clic su **Active Directory**nel pannello di navigazione sinistro.
   
   ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Pagerduty**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Application gallery")
7. Nel riquadro dei risultati selezionare **Pagerduty**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Pagerduty tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Pagerduty** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Pagerduty** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configure single sign-on")
3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL accesso Pagerduty** nel formato "*https://\<tenant-name\>.Pagerduty.com*" e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configure app url")
4. Nella pagina **Configura accesso Single Sign-On in Pagerduty** fare clic su **Scarica certificato**, quindi salvare il file di certificato nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configure single sign-on")
5. In un'altra finestra del Web browser accedere al sito aziendale di Pagerduty come amministratore.
6. Nel menu in alto fare clic su **Impostazioni account**.
   
   ![Impostazioni account](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Account Settings")
7. Fare clic su **Single Sign-On**.
   
   ![Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Single sign-on")
8. Nella pagina Attiva Single Sign-on (SSO) eseguire la procedura seguente:
   
   ![Attiva Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Enable single sign-on")
   
   1. Creare un file **con codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   2. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato X.509**
   3. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Pagerduty** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Login URL** (URL di accesso).
   4. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Pagerduty** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Logout URL** (URL di disconnessione).
   5. Selezionare **Attiva Single Sign-on**.
   6. Fare clic su **Salva modifiche**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Pagerduty, è necessario eseguirne il provisioning in Pagerduty.  
Nel caso di Pagerduty, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al tenant **Pagerduty** .
2. Scegliere **Utenti**dal menu in alto.
3. Fare clic su **Aggiungi utenti**.
   
   ![Aggiungi utenti](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Add Users")
4. Nella finestra di dialogo **Invite your team** (Invita il team) digitare **nome e cognome** e **indirizzo di posta elettronica** dell'utente di Azure AD di cui eseguire il provisioning, fare clic su **Add** (Aggiungi) e su **Send Invites** (Invia inviti).
   
   ![Invita il team](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invite your team")
   
   > [!NOTE]
   > Tutti gli utenti aggiunti riceveranno un invito per creare un account PagerDuty.
   > 
   > 

> [!NOTE]
> È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Pagerduty o le API fornite da Pagerduty per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Per assegnare gli utenti a Pagerduty, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Pagerduty** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


