---
title: 'Esercitazione: Integrazione di Azure Active Directory con Wikispaces | Microsoft Docs'
description: Informazioni su come usare Wikispaces con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 665b95aa-f7f5-4406-9e2a-6fc299a1599c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e7761d07beebbbc68db7d3e30a26153bd8f9655


---
# <a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Esercitazione: Integrazione di Azure Active Directory con Wikispaces
Questa esercitazione descrive l'integrazione di Azure e Wikispaces.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Wikispaces abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Wikispaces potranno accedere all'applicazione tramite il sito aziendale di Wikispaces (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione Wikispaces
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

## <a name="enabling-the-application-integration-for-wikispaces"></a>Abilitazione dell'integrazione dell'applicazione Wikispaces
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Wikispaces.

### <a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Wikispaces, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Wikispaces**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Wikispaces** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Wikispaces tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Wikispaces** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Wikispaces** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configure Single Sign-On")
3. Nella casella di testo **URL di accesso Wikispaces** della pagina **Configura URL app** digitare l'URL usando il modello "*http://company.wikispaces.net*" e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Wikispaces** fare clic su **Download metadati** e quindi salvare il file dei metadati nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configure Single Sign-On")
5. Inviare il file dei metadati al team di supporto di Wikispaces.
   
   > [!NOTE]
   > La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Wikispaces. Al termine della configurazione, viene ricevuta una notifica.
   > 
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Wikispaces, è necessario eseguirne il provisioning in Wikispaces.  
Nel caso di Wikispaces, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **Wikispaces** come amministratore.
2. Passare a **Membri**.
   
   ![Membri](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Members")
3. Fare clic su **Invita persone**.
   
   ![Invita persone](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invite People")
4. Nella sezione **Invita Persone** seguire la procedura seguente:
   
   ![Invita persone](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invite People")
   
   1. Nelle apposite caselle di testo immettere i valori di **Usernames o Email Address** di un account Azure AD valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Send**.  
      
      > [!NOTE]
      > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
      > 
      > 

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Wikispaces per eseguire il provisioning degli account utente Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>Per assegnare gli utenti a Wikispaces, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Wikispaces** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


