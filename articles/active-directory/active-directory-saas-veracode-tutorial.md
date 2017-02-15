---
title: 'Esercitazione: Integrazione di Azure Active Directory con Veracode | Documentazione Microsoft'
description: Informazioni su come usare Veracode con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 731a0fd05b6ed0c49c0c06836e33cd99f7e3bce2


---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Esercitazione: Integrazione di Azure Active Directory con Veracode
Questa esercitazione descrive l'integrazione di Azure e Veracode. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Veracode abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Veracode potranno eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione Veracode
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-veracode-tutorial/IC802903.png "Scenario")

## <a name="enabling-the-application-integration-for-veracode"></a>Abilitazione dell'integrazione dell'applicazione Veracode
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Veracode.

### <a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Veracode, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-veracode-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-veracode-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Veracode**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-veracode-tutorial/IC802904.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Veracode** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Veracode tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
L'applicazione Veracode prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**.  
La schermata seguente illustra un esempio relativo a questa operazione.

![Attributi](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Veracode** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-veracode-tutorial/IC802907.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Veracode** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-veracode-tutorial/IC802908.png "Configure Single Sign-On")
3. Nella pagina **Configurare le impostazioni dell'app** fare clic su **Avanti**.
   
   ![Configurare le impostazioni dell'app](./media/active-directory-saas-veracode-tutorial/IC802909.png "Configure App Settings")
4. Nella pagina **Configura accesso Single Sign-On in Veracode** fare clic su **Scarica certificato** per scaricare il file del certificato e quindi salvarlo localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-veracode-tutorial/IC802910.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Veracode come amministratore.
6. Nel menu in alto fare clic su **Settings** (Impostazioni) e quindi su **Admin**.
   
   ![Amministrazione](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")
7. Fare clic sulla scheda **SAML** .
8. Nella sezione **Impostazioni SAML dell’organizzazione** seguire questa procedura:
   
   ![Amministrazione](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")
   
   1. Nella finestra di dialogo **Configura accesso Single Sign-On in Veracode** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e quindi incollarlo nella casella di testo **Issuer** (Autorità di certificazione)
   2. Per caricare il certificato scaricato, fare clic su **Scegli file**.
   3. Selezionare **Abilita la registrazione automatica**.
9. Nella sezione **Self Registration Settings** (Impostazioni di registrazione automatica) seguire questa procedura e quindi fare clic su **Save** (Salva):
   
   ![Amministrazione](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")
   
   1. In **New User Activation** (Attivazione nuovo utente) selezionare **No Activation Required** (Nessuna attivazione richiesta).
   2. In **User Data Updates** (Aggiornamenti dati utenti) selezionare **Preference Veracode User Data** (Dati utenti Veracode di preferenza).
   3. In **Dettagli sull’attributo SAML**selezionare le opzioni seguenti:
      * **User Roles**
      * **Policy Administrator**
      * **Reviewer**
      * **Security Lead**
      * **Executive**
      * **Submitter**
      * **Creator**
      * **All Scan Types**
      * **Team Memberships**
      * **Default Team**
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-veracode-tutorial/IC802914.png "Configure Single Sign-On")
11. Nel menu in alto fare clic su **Attributi** to open the **SAML Token Attributi** .
    
    ![Attributi](./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributes")
12. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:
    
    ![Attributi](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")
    
    | Nome attributo | Valore attributo |
    |:--- |:--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |
    
    1. Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
    2. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
    3. Nella casella di testo **Valore attributo** , selezionare il valore dell'attributo indicato per la riga.
    4. Fare clic su **Completa**.
13. Fare clic su **Applica modifiche**.

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Veracode, è necessario eseguirne il provisioning in Veracode.  
Nel caso di Veracode, il provisioning è un'attività automatica.  
Non è necessario eseguire alcuna operazione.

Se necessario, gli utenti vengono creati automaticamente durante il primo tentativo di accesso Single Sign-On.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Veracode per eseguire il provisioning degli account utente Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Per assegnare gli utenti a Veracode, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Veracode ** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-veracode-tutorial/IC802915.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-veracode-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


