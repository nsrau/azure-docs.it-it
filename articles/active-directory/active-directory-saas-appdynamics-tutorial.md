---
title: 'Esercitazione: Integrazione di Azure Active Directory con AppDynamics | Documentazione Microsoft'
description: Informazioni su come usare AppDynamics con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 13f4e780afc26dc0f1f59408a77f68a7e46308c7


---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Esercitazione: Integrazione di Azure Active Directory con AppDynamics
Questa esercitazione descrive l'integrazione di Azure e AppDynamics. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione AppDynamics abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati ad AppDynamics potranno accedere all'applicazione tramite il sito aziendale di AppDynamics (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per AppDynamics
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario")

## <a name="enabling-the-application-integration-for-appdynamics"></a>Abilitazione dell'integrazione dell'applicazione per AppDynamics
Questa sezione descrive come abilitare l'integrazione dell'applicazione per AppDynamics.

### <a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per AppDynamics, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **AppDynamics**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **AppDynamics** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione ad AppDynamics tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **AppDynamics** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configure Single SignOn")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a AppDynamics** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configure Single SignOn")
3. Nella pagina **Configura URL app** nella casella di testo **AppDynamics Sign On URL** (URL di accesso ad AppDynamics) digitare l'URL utilizzato dagli utenti per accedere ad AppDynamics ("*https://companyname.saas.appdynamics.com*") e fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in AppDynamics** fare clic su **Download certificato** e infine salvare il file di certificato nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configure Single SignOn")
5. In un'altra finestra del Web browser accedere al sito aziendale di AppDynamics come amministratore.
6. Nella barra degli strumenti in alto fare clic su **Settings** e quindi su **Administration**.
   
   ![Amministrazione](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")
7. Fare clic sulla scheda **Authentication Provider** .
   
   ![Authentication Provider](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")
8. Nella sezione **Authentication Provider** seguire questa procedura:
   
   ![SAML Configuration](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")
   
   1. Per **Authentication Provider** selezionare **SAML**.
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in AppDynamics** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Login URL** (URL di accesso).
   3. Nella finestra di dialogo **Configura accesso Single Sign-On in AppDynamics** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Logout URL** (URL di disconnessione).
   4. Creare un file **con codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   5. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato** .
   6. Fare clic su **Salva**.
      ![Salva](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere ad AppDynamics, è necessario eseguirne il provisioning in AppDynamics.  
Nel caso di AppDynamics, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di AppDynamics come amministratore.
2. Passare a **Users** e fare clic su **+** per aprire la finestra di dialogo **Create User**.
   
   ![Utenti](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")
3. Nella sezione **Crea utente** , eseguire la procedura seguente:
   
   ![Crea utente](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")
   
   1. Nelle caselle di testo **Username**, **Name**, **Email**, **New Password** e **Repeat New Password** digitare nome utente, nome, indirizzo di posta elettronica, nuova password e conferma nuova password di un account utente ADD valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Salva**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da AppDynamics per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Per assegnare gli utenti ad AppDynamics, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **AppDynamics** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


