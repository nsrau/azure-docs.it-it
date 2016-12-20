---
title: 'Esercitazione: Integrazione di Azure Active Directory con TeamSeer | Microsoft Docs'
description: Informazioni su come usare TeamSeer con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d978744bfecddc43c0bf5e803f1fa6bc281ee79b


---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Esercitazione: Integrazione di Azure Active Directory con TeamSeer
In questa esercitazione verrà illustrata l'integrazione di Azure e TeamSeer  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant TeamSeer

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a TeamSeer potranno accedere all'applicazione tramite il sito aziendale di TeamSeer (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per TeamSeer
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scenario")

## <a name="enabling-the-application-integration-for-teamseer"></a>Abilitazione dell'integrazione dell'applicazione per TeamSeer
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per TeamSeer.

### <a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per TeamSeer, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **TeamSeer**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **TeamSeer**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a TeamSeer tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **TeamSeer** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a TeamSeer** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configure Single Sign-On")
3. Nella casella di testo **URL di accesso TeamSeer** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*http://www.teamseer.com/companyid*", quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in TeamSeer** per scaricare il file del certificato, fare clic su **Scarica certificato**, quindi salvarlo nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di TeamSeer come amministratore.
6. Andare ad **Amministratore risorse umane**.
   
   ![Amministratore risorse umane](./media/active-directory-saas-teamseer-tutorial/IC789634.png "HR Admin")
7. Fare clic su **Configura**.
   
   ![Configura](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Setup")
8. Fare clic su **Configura dettagli del provider SAML**.
   
   ![Impostazioni SAML](./media/active-directory-saas-teamseer-tutorial/IC789636.png "SAML Settings")
9. Nella sezione dei dettagli del provider SAML, eseguire la procedura seguente:
   
   ![Impostazioni SAML](./media/active-directory-saas-teamseer-tutorial/IC789637.png "SAML Settings")
   
   1. Nella finestra di dialogo **Configura accesso Single Sign-On in TeamSeer** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **URL**.
   2. Creare un file **con codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   3. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificate pubblico IDP** .
10. Per completare la configurazione del provider SAML eseguire la procedura seguente:
    
    ![Impostazioni SAML](./media/active-directory-saas-teamseer-tutorial/IC789638.png "SAML Settings")
    
    1. In **Test indirizzi e-mail**, digitare l’indirizzo e-mail dell’utente test.
    2. Nella casella di testo **Autorità di certificazione** digitare l'URL del provider di servizi.
    3. Fare clic su **Save**.
11. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a TeamSeer, è necessario eseguirne il provisioning in TeamSeer.  
Nel caso di TeamSeer, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **TeamSeer** come amministratore.
2. Eseguire la procedura seguente:
   
   ![Amministratore risorse umane](./media/active-directory-saas-teamseer-tutorial/IC789640.png "HR Admin")
   
   1. Accedere a **Amministratore risorse umane \> Utenti**.
   2. Fare clic su **Esegui procedura guidata nuovo utente**.
3. Nella sezione **Dettagli utente** eseguire la procedura seguente:
   
   ![Dettagli utente](./media/active-directory-saas-teamseer-tutorial/IC789641.png "User Details")
   
   1. Digitare il **Nome**, **Cognome**, **Nome utente (indirizzo e-mail)** di un account AAD valido di cui si desidera eseguire il provisioning nelle caselle correlate.
   2. Fare clic su **Next**.
4. Seguire le istruzioni visualizzate per l'aggiunta di un nuovo utente e fare clic su **Fine**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TeamSeer per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>Per assegnare gli utenti a TeamSeer, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **TeamSeer** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


