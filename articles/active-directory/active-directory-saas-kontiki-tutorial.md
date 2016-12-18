---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kontiki | Documentazione Microsoft'
description: Informazioni su come usare Kontiki con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 38b7f9772d5cf4e7b7861ef57804436de2a2f442


---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Esercitazione: Integrazione di Azure Active Directory con Kontiki
Questa esercitazione descrive l'integrazione di Azure e Kontiki.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Kontiki abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Kontiki potranno accedere all'applicazione tramite il sito aziendale di Kontiki (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Kontiki
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Scenario")

## <a name="enabling-the-application-integration-for-kontiki"></a>Abilitazione dell'integrazione dell'applicazione per Kontiki
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Kontiki.

### <a name="to-enable-the-application-integration-for-kontiki-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Kontiki, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Kontiki**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Kontiki** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Kontiki tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Kontiki** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configure Single SignOn")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Kontiki** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configure Single SignOn")
3. Nella casella di testo **URL di accesso Kontiki** della pagina **Configure App URL** (Configura URL app) digitare l'URL usato dagli utenti per accedere a Kontiki, ad esempio "*https://company.mc.eval.kontiki.com/*", e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Kontiki** fare clic su **Scarica metadati** per scaricare il file di metadati e salvarlo nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configure Single SignOn")
5. Inviare il file di metadati al team di supporto di Kontiki.
   
   > [!NOTE]
   > La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Kontiki. Al termine della configurazione, viene ricevuta una notifica.
   > 
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Non è necessaria alcuna attività di configurazione per il provisioning degli utenti in Kontiki.  
Quando un utente assegnato prova ad accedere a Kontiki usando il pannello di accesso, Kontiki verifica se l'utente esiste.  
Se l'account utente non è presente, Kontiki lo crea automaticamente.

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-kontiki-perform-the-following-steps"></a>Per assegnare gli utenti a Kontiki, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Kontiki** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


