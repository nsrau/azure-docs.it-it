---
title: 'Esercitazione: Integrazione di Azure Active Directory con ITRP | Microsoft Docs'
description: Informazioni su come usare ITRP con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ab3e450b78e95ed6967d5cfdfb89d27257902e6d


---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Esercitazione: Integrazione di Azure Active Directory con ITRP
Questa esercitazione descrive l'integrazione di Azure e ITRP.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di ITRP

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a ITRP potranno accedere all'applicazione tramite il sito aziendale di ITRP (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione ITRP
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-itrp-tutorial/IC775551.png "Scenario")

## <a name="enabling-the-application-integration-for-itrp"></a>Abilitazione dell'integrazione dell'applicazione ITRP
Questa sezione descrive come abilitare l'integrazione dell'applicazione per ITRP.

### <a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per ITRP, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-itrp-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-itrp-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-itrp-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **ITRP**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-itrp-tutorial/IC775565.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **ITRP** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a ITRP tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per ITRP richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **ITRP** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC771709.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a ITRP** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC775567.png "Configure Single Sign-On")
3. Nella casella di testo **URL di accesso ITRP** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<nome-tenant\>.ITRP.com*" e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-itrp-tutorial/IC775568.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in ITRP** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente come **c:\\ITRP.cer**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC775569.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di ITRP come amministratore.
6. Nel barra degli strumenti in alto fare clic su **Impostazioni**.
   
   ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")
7. Nel riquadro di navigazione sinistro selezionare **Single Sign-On**.
   
   ![Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC775571.png "Single Sign-On")
8. Nella sezione di configurazione per Single Sign-On seguire questa procedura:
   
   ![Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC775572.png "Single Sign-On")
   
   ![Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC775573.png "Single Sign-On")
   
   1. Fare clic su **Abilita**.
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in ITRP** del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Remote Logout URL** (URL disconnessione remota).
   3. Nella finestra di dialogo **Configura accesso Single Sign-On in ITRP** del portale di Azure classico copiare il valore di **URL SSO SAML** e quindi incollarlo nella casella di testo **SAML SSO URL** (URL SSO SAML).
   4. Copiare il valore **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Impronta digitale del certificato**.
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)
      > 
      > 
   5. Fare clic su **Salva**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-itrp-tutorial/IC775574.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a ITRP, è necessario eseguirne il provisioning in ITRP.  
Nel caso di ITRP, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al tenant di **ITRP** .
2. Nel barra degli strumenti in alto fare clic su **Records**.
   
   ![Amministratore](./media/active-directory-saas-itrp-tutorial/IC775575.png "Admin")
3. Dal menu popup selezionare **Persone**.
   
   ![Persone](./media/active-directory-saas-itrp-tutorial/IC775587.png "People")
4. Fare clic su **Aggiungi nuova persona** ("+").
   
   ![Amministratore](./media/active-directory-saas-itrp-tutorial/IC775576.png "Admin")
5. Nella finestra di dialogo Add New Person seguire questa procedura:
   
   ![Utente](./media/active-directory-saas-itrp-tutorial/IC775577.png "User")
   
   1. Nelle caselle di testo **Name** ed **Email** digitare il nome e l'indirizzo di posta elettronica di un account AAD valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Salva**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da ITRP per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-itrp-perform-the-following-steps"></a>Per assegnare gli utenti a ITRP, seguire questa procedura:
1. Nel portale di Azure AD creare un account di test.
2. Nella pagina di integrazione dell'applicazione **ITRP **fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-itrp-tutorial/IC775588.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-itrp-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


