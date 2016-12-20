---
title: 'Esercitazione: Integrazione di Azure Active Directory con e-Builder | Documentazione Microsoft'
description: Informazioni su come usare e-Builder con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d5068007-5a54-40ac-9cb8-2ceca89fd8ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c08989aa8dc966fc731be7a53c1eb0f1d57b5053


---
# <a name="tutorial-azure-active-directory-integration-with-e-builder"></a>Esercitazione: Integrazione di Azure Active Directory con e-Builder
Questa esercitazione descrive l'integrazione di Azure ed e-Builder.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di e-Builder

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a e-Builder potranno accedere all'applicazione tramite il sito aziendale di e-Builder (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per e-Builder
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-e-builder-tutorial/IC777378.png "Scenario")

## <a name="enabling-the-application-integration-for-e-builder"></a>Abilitazione dell'integrazione dell'applicazione per e-Builder
Questa sezione descrive come abilitare l'integrazione dell'applicazione per e-Builder.

### <a name="to-enable-the-application-integration-for-e-builder-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per e-Builder, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-e-builder-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-e-builder-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-e-builder-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **e-Builder**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-e-builder-tutorial/IC777379.png "Application gallery")
7. Nel riquadro dei risultati selezionare **e-Builder** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![e-Builder](./media/active-directory-saas-e-builder-tutorial/IC777380.png "e-Builder")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a e-Builder con il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **EmpCenter** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configure single sign-on")
2. Nella pagina **How would you like users to sign on to e-Builder** (Stabilire come si desidera che gli utenti accedano a e-Builder) selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configure single sign-on")
3. Nella casella di testo **URL accesso e-Builder** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<tenant-name\>.e-Builder.com*" e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configure app URL")
4. Nella pagina **Configura accesso Single Sign-On in e-Builder** fare clic su **Scarica metadati** per scaricare i metadati e salvare il file di dati localmente come **c:\\e-BuilderMetaData.xml**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configure single sign-on")
5. Inoltrare il file dei metadati al team di supporto di e-Builder. La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto.
6. Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in e-Builder.  
Quando un utente assegnato prova ad accedere a e-Builder usando il pannello di accesso, e-Builder verifica se l'utente esiste.  
Se l'account utente non è presente, e-Builder lo crea automaticamente.

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-e-builder-perform-the-following-steps"></a>Per assegnare gli utenti a e-Builder, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **e-Builder** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-e-builder-tutorial/IC777386.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-e-builder-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


