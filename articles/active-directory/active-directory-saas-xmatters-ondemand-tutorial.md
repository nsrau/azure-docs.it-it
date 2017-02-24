---
title: 'Esercitazione: Integrazione di Azure Active Directory con xMatters OnDemand | Documentazione Microsoft'
description: Informazioni su come usare xMatters OnDemand con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 143e4a856a31a44a9ec909d07d5f24710a6e2803


---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Esercitazione: Integrazione di Azure Active Directory con xMatters OnDemand
Questa esercitazione descrive l'integrazione di Azure e xMatters OnDemand. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di xMatters OnDemand

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a xMatters OnDemand potranno accedere all'applicazione tramite il sito aziendale di xMatters OnDemand (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione xMatters OnDemand
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenario")

## <a name="enabling-the-application-integration-for-xmatters-ondemand"></a>Abilitazione dell'integrazione dell'applicazione xMatters OnDemand
Questa sezione descrive come abilitare l'integrazione dell'applicazione per xMatters OnDemand.

### <a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per xMatters OnDemand, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **xMatters OnDemand**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **XMatters OnDemand** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a XMatters OnDemand tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **XMatters OnDemand** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a XMatters OnDemand** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configura URL app** seguire questa procedura:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configurare l'URL dell'app")
   
    a. Nella casella di testo **URL accesso XMatters OnDemand** digitare l'URL nel formato seguente: `https://<tenant-name>.XMattersOnDemandapp.com`
   
    b. Fare clic su **Avanti**.

4. Nella pagina **Configura accesso Single Sign-On in XMatters OnDemand** fare clic su **Scarica certificato** e quindi salvare il file di certificato localmente come **c:\\XMatters OnDemand.cer**.
   
    > [!IMPORTANT]
    > Inoltrare il certificato al team di supporto xMatters. Per poter completare la configurazione di Single Sign-On, è necessario che il certificato venga caricato dal team di supporto xMatters.
    > 
    > 
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configurare l'accesso Single Sign-On")

5. In un'altra finestra del Web browser accedere al sito aziendale di XMatters OnDemand come amministratore.

6. Sulla barra degli strumenti in alto fare clic su **Admin** e quindi su **Company Details** (Dettagli società) sulla barra di spostamento a sinistra.
   
    ![Amministratore](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Amministratore")

7. Nella pagina **Configurazione SAML** seguire la procedura seguente:
   
    ![Configurazione SAML](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Configurazione SAML")
   
    a. Selezionare **Enable SAML**.
   
    b. Nella finestra di dialogo **Configura accesso Single Sign-On in XMatters OnDemand** del portale di Azure classico copiare il valore di **ID provider di identità** e quindi incollarlo nella casella di testo **Identity Provider ID** (ID provider di identità).
   
    c. Nella finestra di dialogo **Configura accesso Single Sign-On in XMatters OnDemand** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **Single Sign On URL** (URL servizio Single Sign-On).
   
    d. Nella finestra di dialogo **Configura accesso Single Sign-On in XMatters OnDemand** del portale di Azure classico copiare il valore di **URL servizio Single Sign-Out** e quindi incollarlo nella casella di testo **Single Logout URL** (URL punto di disconnessione singolo).
   
    e. Nella parte superiore della pagina Informazioni sull’azienda fare clic su **Salva modifiche**.
    
    ![Dettagli dell'azienda](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Dettagli dell'azienda")

8. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configurare l'accesso Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a XMatters OnDemand, è necessario eseguirne il provisioning in XMatters OnDemand.  
Nel caso di XMatters OnDemand, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al tenant di **XMatters OnDemand** .

2. Fare clic sulla scheda **Utenti** .

3. Fare clic su **Aggiungi utente**.
  
    ![Utenti](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Utenti")

4. Selezionare **Active**.

5. Nella sezione **Aggiungi un utente** eseguire la procedura seguente:
   
    ![Aggiungere un utente](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Aggiungere un utente")
   
    a. Nelle apposite caselle di testo immettere i valori di **ID utente**, **Nome**, **Cognome** e **Sito** di un account AAD valido di cui si vuole eseguire il provisioning.
    
    b. Fare clic su **Save**.


## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Per assegnare gli utenti a XMatters OnDemand, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **XMatters OnDemand ** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO2-->


