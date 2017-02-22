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
ms.date: 01/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 991a7d112bc0cd65466be394dbb1aad9ca823681
ms.openlocfilehash: 726880186693756941538f767dfba40c54ac9fd9


---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con Mozy Enterprise
In questa esercitazione viene illustrata l'integrazione di Azure e Mozy Enterprise.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Mozy Enterprise

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Mozy Enterprise potranno eseguire l'accesso Single Sign-On (SSO) all'applicazione tramite il sito aziendale di Mozy Enterprise (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Mozy Enterprise
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "Scenario")

## <a name="enabling-the-application-integration-for-mozy-enterprise"></a>Abilitazione dell'integrazione dell'applicazione per Mozy Enterprise
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Mozy Enterprise.

**Per abilitare l'integrazione dell'applicazione per Mozy Enterprise, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **mozy enterprise**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Mozy Enterprise** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Mozy Enterprise tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  

Come parte di questa procedura, verrà richiesto di caricare un file di certificato codificato Base&64; sul tenant Mozy Enterprise. Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Mozy Enterprise** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On **.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Mozy Enterprise** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di accesso a Mozy Enterprise** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<nome-tenant\>.Mozyenterprise.com*" e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Mozy Enterprise** fare clic su **Scarica certificato** per scaricare il certificato, quindi salvare il file di certificato nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Mozy Enterprise come amministratore.
6. Nella sezione **Configuration** fare clic su **Authentication Policy**.
   
   ![Criteri di autenticazione](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "Criteri di autenticazione")
7. Nella sezione **Authentication Policy** seguire questa procedura:
   
   ![Criteri di autenticazione](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "Criteri di autenticazione")
   
   1. Selezionare **Directory Service** come **Provider**.
   2. Selezionare **Use LDAP Push**.
   3. Fare clic sulla scheda **SAML Authentication** .
   4. Nella pagina **Configura accesso Single Sign-On in Mozy Enterprise** del portale di Azure classico copiare il valore di **URL richiesta di autenticazione** e incollarlo nella casella di testo **Authentication URL** (URL di autenticazione).
   5. Nella pagina **Configura accesso Single Sign-On in Mozy Enterprise** del portale di Azure classico copiare il valore di **ID provider di identità** e incollarlo nella casella di testo **SAML Endpoint** (Endpoint SAML).
   6. Creare un file **con codifica Base&64;** dal certificato scaricato.  
   
      >[!TIP]
      >Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      >  
   7. Aprire il certificato con codifica Base&64; nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **SAML Certificate** .
   8. Selezionare **Abilita SSO per consentire agli amministratori di accedere con le proprie credenziali di rete**.
   9. Fare clic su **Salva modifiche**.
8. Nella pagina **Configura Single Sign-On in Mozy Enterprise** del portale di Azure classico selezionare la conferma della configurazione dell'accesso e quindi fare clic su **Completa**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "Configurare l'accesso Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Mozy Enterprise, è necessario eseguirne il provisioning in Mozy Enterprise.  
Nel caso di Mozy Enterprise, il provisioning è un’attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Mozy Enterprise** .
2. Fare clic su **Users** (Utenti) e quindi fare clic su **Add New User** (Aggiungi nuovo utente).
   
   ![Utenti](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "Utenti")
   
   > [!NOTE]
   > L'opzione **Add New User** (Aggiungi nuovo utente) viene visualizzata solo se **Mozy** è selezionato come provider in **Authentication policy** (Criteri di autenticazione). Se è configurata l'autenticazione SAML gli utenti vengono aggiunti automaticamente al primo accesso tramite Single Sign-On.
   > 
    
3. Nella finestra di dialogo Nuovo utente eseguire la procedura seguente:
   
   ![Aggiungere utenti](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "Aggiungere utenti")
   
  1. Dall’elenco **Choose a group** selezionare un gruppo.
  2. Dall’elenco **What tupe of user** selezionare un tipo.
  3. Nella casella di testo **Nome utente** digitare il nome dell'utente di Azure AD.
  4. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente di Azure AD.
  5. Selezionare **Send user instruction email**.
  6. Fare clic su **Add User(s)**.
   > [!NOTE]
   > Dopo aver creato l'utente, verrà inviato un messaggio di posta elettronica all'utente di Azure AD con un collegamento da selezionare per confermare l'account e attivarlo.


> [!NOTE]
> È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Mozy Enterprise o le API fornite da Mozy Enterprise per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Mozy Enterprise, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione di **Mozy Enterprise **fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO1-->


