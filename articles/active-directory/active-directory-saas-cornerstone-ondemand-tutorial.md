---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cornerstone OnDemand | Microsoft Docs'
description: Informazioni su come usare Cornerstone OnDemand con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7ab0ffdb9619a488915a7627ac7f50cfaad7019b


---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Esercitazione: Integrazione di Azure Active Directory con Cornerstone OnDemand
Questa esercitazione descrive l'integrazione di Azure e Cornerstone OnDemand.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Cornerstone OnDemand

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Cornerstone OnDemand potranno accedere all'applicazione tramite il sito aziendale di Cornerstone OnDemand (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Cornerstone OnDemand
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scenario")

## <a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>Abilitazione dell'integrazione dell'applicazione per Cornerstone OnDemand
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Cornerstone OnDemand.

### <a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Cornerstone OnDemand, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **cornerstone ondemand**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Cornerstone OnDemand** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![cornerstone ondemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Cornerstone OnDemand tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Cornerstone OnDemand** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Abilitazione dell'accesso Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Enable Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Cornerstone OnDemand** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD Single Sign-On")
3. Nella pagina **Configura URL app** nella casella di testo **Cornerstone OnDemand Sign In URL** (URL di accesso a Cornerstone OnDemand) digitare l'URL usando il modello "*http://company.csod.com*" e fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Cornerstone OnDemand** fare clic su **Download certificato** e infine salvare il file di certificato localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configure Single Sign-On")
5. Inviare quanto segue al team di supporto di Cornerstone OnDemand:
   
   1. Certificato scaricato
   2. Il valore **URL accesso remoto**
   3. Il valore **URL disconnessione remota**
   
   > [!NOTE]
   > L'accesso Single Sign-On deve essere configurato dal team di supporto di Cornerstone OnDemand.
   > Il team di supporto invierà una notifica al termine della configurazione.
   > 
   > 
6. Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Cornerstone OnDemand, è necessario eseguirne il provisioning in Cornerstone OnDemand.  
Nel caso di Cornerstone OnDemand, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Inviare le informazioni, ad esempio nome e indirizzo di posta elettronica, relative all'utente di Azure AD di cui si vuole eseguire il provisioning al team di supporto di Cornerstone OnDemand.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Cornerstone OnDemand per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Per assegnare gli utenti a Cornerstone OnDemand, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Cornerstone OnDemand** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Assegna utenti](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Assign Users")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


