---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mozy Enterprise | Documentazione Microsoft'
description: Informazioni su come utilizzare Mozy Enterprise con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6034434c289936089afbb0fa5bd803363cde882


---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con Mozy Enterprise
In questa esercitazione viene illustrata l'integrazione di Azure e Mozy Enterprise.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Mozy Enterprise

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Mozy Enterprise saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Mozy Enterprise (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Mozy Enterprise
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Scenario")

## <a name="enabling-the-application-integration-for-mozy-enterprise"></a>Abilitazione dell'integrazione dell'applicazione per Mozy Enterprise
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Mozy Enterprise.

### <a name="to-enable-the-application-integration-for-mozy-enterprise-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Mozy Enterprise eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **mozy enterprise**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Mozy Enterprise** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![mozy enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Mozy Enterprise tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di caricare un file di certificato codificato Base 64 sul tenant Mozy Enterprise.  
Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Mozy Enterprise** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On **.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Mozy Enterprise** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configure single sign-on")
3. Nella casella di testo **URL di accesso a Mozy Enterprise** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<nome-tenant\>.Mozyenterprise.com*" e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configure app URL")
4. Nella pagina **Configura accesso Single Sign-On in Mozy Enterprise** fare clic su **Scarica certificato** per scaricare il certificato, quindi salvare il file di certificato nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configure single sign-on")
5. In un'altra finestra del Web browser accedere al sito aziendale di Mozy Enterprise come amministratore.
6. Nella sezione **Configuration** fare clic su **Authentication Policy**.
   
   ![Authentication Policy](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Authentication policy")
7. Nella sezione **Authentication Policy** seguire questa procedura:
   
   ![Authentication Policy](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Authentication policy")
   
   1. Selezionare **Directory Service** come **Provider**.
   2. Selezionare **Use LDAP Push**.
   3. Fare clic sulla scheda **SAML Authentication** .
   4. Nella pagina **Configura accesso Single Sign-On in Mozy Enterprise** del portale di Azure classico copiare il valore di **URL richiesta di autenticazione** e incollarlo nella casella di testo **Authentication URL** (URL di autenticazione).
   5. Nella pagina **Configura accesso Single Sign-On in Mozy Enterprise** del portale di Azure classico copiare il valore di **ID provider di identità** e incollarlo nella casella di testo **SAML Endpoint** (Endpoint SAML).
   6. Creare un file **con codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   7. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **SAML Certificate** .
   8. Selezionare **Abilita SSO per consentire agli amministratori di accedere con le proprie credenziali di rete**.
   9. Fare clic su **Salva modifiche**.
8. Nella pagina **Configura Single Sign-On in Mozy Enterprise** del portale di Azure classico selezionare la conferma della configurazione dell'accesso e quindi fare clic su **Completa**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Mozy Enterprise, è necessario eseguirne il provisioning in Mozy Enterprise.  
Nel caso di Mozy Enterprise, il provisioning è un’attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al tenant **Mozy Enterprise** .
2. Fare clic su **Users** (Utenti) e quindi fare clic su **Add New User** (Aggiungi nuovo utente).
   
   ![Utenti](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Users")
   
   > [!NOTE]
   > L'opzione **Add New User** (Aggiungi nuovo utente) viene visualizzata solo se **Mozy** è selezionato come provider in **Authentication policy** (Criteri di autenticazione). Se è configurata l'autenticazione SAML gli utenti vengono aggiunti automaticamente al primo accesso tramite Single Sign-On.
   > 
   > 
3. Nella finestra di dialogo Nuovo utente eseguire la procedura seguente:
   
   ![Aggiungi utenti](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Add Users")
   
   1. Dall’elenco **Choose a group** selezionare un gruppo.
   2. Dall’elenco **What tupe of user** selezionare un tipo.
   3. Nella casella di testo **Nome utente** digitare il nome dell'utente di Azure AD.
   4. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente di Azure AD.
   5. Selezionare **Send user instruction email**.
   6. Fare clic su **Add User(s)**.
   
   > [!NOTE]
   > Dopo aver creato l'utente, verrà inviato un messaggio di posta elettronica all'utente di Azure AD con un collegamento da selezionare per confermare l'account e attivarlo.
   > 
   > 

> [!NOTE]
> È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Mozy Enterprise o le API fornite da Mozy Enterprise per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-mozy-enterprise-perform-the-following-steps"></a>Per assegnare gli utenti a Mozy Enterprise, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione di **Mozy Enterprise **fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


