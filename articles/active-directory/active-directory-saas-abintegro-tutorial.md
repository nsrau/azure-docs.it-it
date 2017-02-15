---
title: 'Esercitazione: Integrazione di Azure Active Directory con Abintegro | Documentazione Microsoft'
description: Informazioni su come usare Abintegro con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 99287e1f-4189-494a-97c8-e1c03d047fd3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 64a7ff2552d2ccc97b1ec90cf524aed6b62ecb82


---
# <a name="tutorial-azure-active-directory-integration-with-abintegro"></a>Esercitazione: Integrazione di Azure Active Directory con Abintegro
Questa esercitazione descrive l'integrazione di Azure e Abintegro.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione Abintegro abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati ad Abintegro potranno accedere all'applicazione tramite il sito aziendale di Abintegro (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Abintegro
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scenario")

## <a name="enabling-the-application-integration-for-abintegro"></a>Abilitazione dell'integrazione dell'applicazione per Abintegro
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Abintegro.

### <a name="to-enable-the-application-integration-for-abintegro-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Abintegro, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-abintegro-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-abintegro-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-abintegro-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **abintegro**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-abintegro-tutorial/IC790077.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Abintegro** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad Abintegro tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Abintegro** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configure Single SignOn")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Abintegro** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configure Single SignOn")
3. Nella casella di testo **URL di accesso a Abintegro** della pagina **Configura URL app** digitare l'URL utilizzato dagli utenti per accedere ad Abintegro, ad esempio `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`, e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Abintegro** fare clic su **Scarica metadati** e quindi salvare il file di metadati nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configure Single SignOn")
5. Inviare il file di metadati al team di supporto di Abintegro.
   
   > [!NOTE]
   > La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Abintegro. Al termine della configurazione, viene ricevuta una notifica.
   > 
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in Abintegro.  
Quando un utente assegnato tenta di accedere ad Abintegro usando il pannello di accesso, Abintegro verifica se l'utente esiste.  
Se l'account utente non è presente, Abintegro lo crea automaticamente.

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-abintegro-perform-the-following-steps"></a>Per assegnare gli utenti ad Abintegro, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Abintegro** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-abintegro-tutorial/IC790084.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-abintegro-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


