---
title: 'Esercitazione: Integrazione di Azure Active Directory con Brightspace by Desire2Learn | Documentazione Microsoft'
description: Informazioni su come usare Brightspace by Desire2Learn con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b68e1f772071678cef5380bbcb7c2b057748a6f3


---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Esercitazione: Integrazione di Azure Active Directory con Brightspace by Desire2Learn
Questa esercitazione descrive l'integrazione di Azure e Brightspace by Desire2Learn.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Brightspace by Desire2Learn abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Brightspace by Desire2Learn potranno accedere all'applicazione tramite il sito aziendale di Brightspace by Desire2Learn (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Brightspace by Desire2Learn
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scenario")

## <a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>Abilitazione dell'integrazione dell'applicazione per Brightspace by Desire2Learn
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Brightspace by Desire2Learn.

### <a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Brightspace by Desire2Learn, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Brightspace by Desire2Learn**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Apllication Gallery")
7. Nel riquadro dei risultati selezionare **Brightspace by Desire2Learn** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Brightspace by Desire2Learn tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Brightspace by Desire2Learn** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Brightspace by Desire2Learn** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configure Single Sign-On")
3. Nella pagina **Configura URL app** seguire questa procedura:
   
   ![Configura URL app](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configure App URL")
   
   1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere a **Brightspace by Desire2Learn**, ad esempio *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*.
   2. Fare clic su **Avanti**
4. Nella pagina **Configura accesso Single Sign-On in Brightspace by Desire2Learn** fare clic su **Scarica metadati** per scaricare i metadati e quindi salvarli nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configure Single Sign-On")
5. Inviare il file di metadati scaricato al team di supporto di Brightspace by Desire2Learn.
   
   > [!NOTE]
   > Il team di supporto di Brightspace by Desire2Learn si occuperà dell'effettiva configurazione dell'accesso Single Sign-On.
   > Una volta completata l'abilitazione dell'accesso Single Sign-On per la sottoscrizione, si riceverà una notifica.
   > 
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Brightspace by Desire2Learn, è necessario eseguirne il provisioning in Brightspace by Desire2Learn.  
Nel caso di Brightspace by Desire2Learn, gli account utente devono essere creati dal team di supporto di Brightspace by Desire2Learn.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Brightspace by Desire2Learn per eseguire il provisioning degli account utente di Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>Per assegnare gli utenti a Brightspace by Desire2Learn, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Brightspace by Desire2Learn** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


