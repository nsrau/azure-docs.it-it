---
title: 'Esercitazione: Integrazione di Azure Active Directory con Canvas LMS | Documentazione Microsoft'
description: Informazioni su come usare Canvas LMS con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0031446f8b5202f6627ed02e90c4789f6d434234


---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Esercitazione: Integrazione di Azure Active Directory con Canvas LMS
Questa esercitazione descrive l'integrazione di Azure e Canvas.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Canvas

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Canvas potranno accedere all'applicazione tramite il sito aziendale di Canvas (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Canvas
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scenario")

## <a name="enabling-the-application-integration-for-canvas"></a>Abilitazione dell'integrazione dell'applicazione per Canvas
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Canvas.

### <a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Canvas, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Canvas**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Canvas** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Canvas")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Canvas tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per Canvas richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Canvas** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Canvas** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configure Single Sign-On")
3. Nella casella di testo **URL accesso Canvas** della pagina **Configura URL app** digitare l'URL usando il modello seguente `https://<tenant-name>.instructure.com` e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Canvas** fare clic su **Scarica certificato** per scaricare il file di certificato e quindi salvarlo localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Canvas come amministratore.
6. Passare a **Courses (Corsi) \> Managed Accounts (Account gestiti) \> Microsoft**.
   
   ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
7. Nel riquadro di spostamento sinistro selezionare **Authentication** (Autenticazione) e quindi fare clic su **Add New SAML Config** (Aggiungi nuova configurazione SAML).
   
   ![Autenticazione](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentication")
8. Nella pagina Current Integration seguire questa procedura:
   
   ![Current Integration](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Current Integration")
   
   1. Nella finestra di dialogo **Configura accesso Single Sign-On in Canvas** del portale di Azure classico copiare il valore di **ID entità** e quindi incollarlo nella casella di testo **IdP Entity ID** (ID entità IdP).
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in Canvas** del portale di Azure classico copiare il valore di **URL di accesso remoto** e quindi incollarlo nella casella di testo **Log On URL** (URL di accesso).
   3. Nella finestra di dialogo **Configura accesso Single Sign-On in Canvas** copiare il valore di **URL di accesso remoto** e quindi incollarlo nella casella di testo **Log Out URL** (URL di disconnessione).
   4. Nella finestra di dialogo **Configura accesso Single Sign-On in Canvas** copiare il valore di **Modifica URL password** e quindi incollarlo nella casella di testo **Change Password Link** (Cambia collegamento password).
   5. Copiare il valore **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Impronta digitale del certificato**.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. Nell'elenco **Login Attribute** (Attributo accesso) selezionare **NameID**.
   7. Nell'elenco **Identifier Format** (Formato identificatore) selezionare **emailAddress**.
   8. Fare clic su **Save authentication settings**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Canvas, è necessario eseguirne il provisioning in Canvas.  
Nel caso di Canvas, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:
1. Accedere al tenant **Canvas** .
2. Passare a **Courses (Corsi) \> Managed Accounts (Account gestiti) \> Microsoft**.
   
   ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
3. Fare clic su **Users**.
   
   ![Users](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Users")
4. Fare clic su **Add new user**.
   
   ![Users](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Users")
5. Nella finestra di dialogo Add a New User seguire questa procedura:
   
   ![Add User](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Add User")
   
   1. Nella casella di testo **Full Name** digitare il nome dell'utente.
   2. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente.
   3. Nella casella di testo **Login** digitare l'indirizzo di posta elettronica di Azure AD dell'utente.
   4. Selezionare **Email the user about this account creation**.
   5. Fare clic su **Add User**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Canvas per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Per assegnare gli utenti a Canvas, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Canvas **fare clic su **Assegna utenti**.
   
   ![Assegnazione degli utenti](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Assigning users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


