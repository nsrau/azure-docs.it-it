---
title: 'Esercitazione: Integrazione di Azure Active Directory con Bime | Documentazione Microsoft'
description: Informazioni su come usare Bime con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 19adbe15694a6413842b23c27851ce582bfd727e


---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Esercitazione: Integrazione di Azure Active Directory con Bime
Questa esercitazione descrive l'integrazione di Azure e Bime.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Bime

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Bime potranno accedere all'applicazione tramite il sito aziendale di Bime (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Bime
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-bime-tutorial/IC775552.png "Scenario")

## <a name="enabling-the-application-integration-for-bime"></a>Abilitazione dell'integrazione dell'applicazione per Bime
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Bime.

### <a name="to-enable-the-application-integration-for-bime-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Bime, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-bime-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-bime-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-bime-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Bime**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-bime-tutorial/IC775553.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Bime** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Bime](./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Bime tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per Bime richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Bime** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC771709.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Bime** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC775555.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL accesso Bime** della pagina **Configura URL app** digitare l'URL nel formato "*https://\<nome-tenant\>.Bimeapp.com*" e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-bime-tutorial/IC775556.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Bime** fare clic su **Scarica certificato** per scaricare il file di certificato e quindi salvarlo localmente come **c:\\Bime.cer**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC775557.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Bime come amministratore.
6. Nella barra degli strumenti fare clic su **Admin** e quindi su **Account**.
   
   ![Amministratore](./media/active-directory-saas-bime-tutorial/IC775558.png "Amministratore")
7. Nella pagina di configurazione dell’account, eseguire la procedura seguente:
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC775559.png "Configurare l'accesso Single Sign-On")
   
   1. Selezionare **Enable SAML authentication**.
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in Bime** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Remote Login URL** (URL accesso remoto).
   3. Copiare il valore **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Impronta digitale del certificato**.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)
      > 
      > 
   4. Fare clic su **Salva**.
8. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-bime-tutorial/IC775560.png "Configurare l'accesso Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Bime, è necessario eseguirne il provisioning in Bime.  
Nel caso di Bime, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al tenant di **Bime** .
2. Nella barra degli strumenti fare clic su **Admin** e quindi su **Users** (Utenti).
   
   ![Amministratore](./media/active-directory-saas-bime-tutorial/IC775561.png "Amministratore")
3. In **Users List** (Elenco utenti) fare clic su **"+"** per aggiungere un nuovo utente.
   
   ![Utenti](./media/active-directory-saas-bime-tutorial/IC775562.png "Utenti")
4. Nella finestra di dialogo **Dettagli utente** seguire questa procedura:
   
   ![Dettagli utente](./media/active-directory-saas-bime-tutorial/IC775563.png "Dettagli utente")
   
   1. Nelle caselle di testo First Name, Last Name, Login, Email digitare nome, cognome, accesso e indirizzo di posta elettronica di un account AAD valido di cui si vuole eseguire il provisioning.
   2. Fare clic su Save.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Bime per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-bime-perform-the-following-steps"></a>Per assegnare gli utenti a Bime, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Bime ** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-bime-tutorial/IC775564.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-bime-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


