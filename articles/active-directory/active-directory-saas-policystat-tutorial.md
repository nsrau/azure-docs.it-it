---
title: 'Esercitazione: Integrazione di Azure Active Directory con PolicyStat | Documentazione Microsoft'
description: Informazioni su come usare PolicyStat con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: 8290d90f22a80d5c585192b17cfa0aeba00aa891


---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Esercitazione: Integrazione di Azure Active Directory con PolicyStat
In questa esercitazione viene illustrata l'integrazione di Azure e PolicyStat.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant PolicyStat

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a PolicyStat saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di PolicyStat (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per PolicyStat
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-policystat-tutorial/IC808662.png "Scenario")

## <a name="enabling-the-application-integration-for-policystat"></a>Abilitazione dell'integrazione dell'applicazione per PolicyStat
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per PolicyStat.

**Per abilitare l'integrazione dell'applicazione per PolicyStat, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-policystat-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-policystat-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-policystat-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **PolicyStat**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-policystat-tutorial/IC808627.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **PolicyStat**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a PolicyStat tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.  

L'applicazione PolicyStat prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**.  

Lo screenshot seguente ne illustra un esempio.

![Attributi](./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributi")

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **PolicyStat** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a PolicyStat** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configurare le impostazioni dell'app** digitare nella casella di testo **URL di accesso** l'URL utilizzato dagli utenti per accedere all'applicazione PolicyStat (ad esempio, *"https://demo-azure.policystat.com"*), quindi fare clic su **Avanti**.
   
   ![Configurare le impostazioni dell'app](./media/active-directory-saas-policystat-tutorial/IC808631.png "Configurare le impostazioni dell'app")
4. Nella pagina **Configura accesso Single Sign-On in PolicyStat** fare clic su **Scarica metadati**, quindi salvare il file di metadati nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di PolicyStat come amministratore.
6. Scegliere la scheda **Admin**, quindi fare clic su **Configurazione di Single Sign-On** nel riquadro di spostamento a sinistra.
   
   ![Menu amministratore](./media/active-directory-saas-policystat-tutorial/IC808633.png "Menu amministratore")
7. Nella sezione **Configurazione** selezionare **Abilitare integrazione di Single Sign-On**.
   
   ![Configurazione Single Sign-on](./media/active-directory-saas-policystat-tutorial/IC808634.png "Configurazione Single Sign-on")
8. Fare clic su **Configura attributi** e nella sezione **Configura attributi** eseguire la procedura seguente:
   
   ![Configurazione Single Sign-on](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configurazione Single Sign-on")
   
   1. Nella casella di testo **Attributo nome utente** digitare **uid**.
   2. Nella casella di testo **Attributo nome** digitare **firstname**.
   3. Nella casella di testo **Attributo cognome** digitare **lastname**.
   4. Nella casella di testo **Attributo posta elettronica** digitare **emailaddress**.
   5. Fare clic su **Salva modifiche**.
9. Fare clic su **Metadati del provider di identità** quindi, nella sezione **Metadati del provider di identità**, eseguire la procedura seguente:
   
   ![Configurazione Single Sign-on](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configurazione Single Sign-on")
   
   1. Aprire il file di metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **Your Identity Provider Metadata** (Metadati provider di identità).
   2. Fare clic su **Salva modifiche**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurare l'accesso Single Sign-On")
  
11. Nel menu in alto fare clic su **Attributi** to open the **SAML Token Attributi** .
    
    ![Attributi](./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributi")
12. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:
    
    ![Attributi](./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributi")
    
   1. Fare clic su **Aggiungi attributo utente**.
   2. Nella casella di testo **Nome attributo** digitare **uid**.
   3. Nella casella di testo **Valore attributo** selezionare **ExtractMailPrefix()**.    
   4. Nell'elenco **Posta** selezionare **User.mail**.
   5. Fare clic su **Complete**.

##<a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a PolicyStat, è necessario eseguirne il provisioning in PolicyStat.  

PolicyStat supporta solo il provisioning JIT dell'utente. Non è pertanto è necessario aggiungere gli utenti manualmente a PolicyStat.  
Gli utenti verranno vengono aggiunti automaticamente al primo accesso tramite il Single Sign-On.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da PolicyStat per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a PolicyStat, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **PolicyStat** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-policystat-tutorial/IC808636.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-policystat-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO1-->


