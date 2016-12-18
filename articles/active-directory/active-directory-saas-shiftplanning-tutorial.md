---
title: 'Esercitazione: Integrazione di Azure Active Directory con ShiftPlanning | Microsoft Docs'
description: Informazioni su come usare ShiftPlanning con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 403f297e7f0bfdde8ade3f35c401bcefb79ea2f5


---
# <a name="tutorial-azure-active-directory-integration-with-shiftplanning"></a>Esercitazione: Integrazione di Azure Active Directory con ShiftPlanning
In questa esercitazione viene illustrata l'integrazione di Azure e ShiftPlanning.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di ShiftPlanning abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a ShiftPlanning saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di ShiftPlanning (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per ShiftPlanning
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scenario")

## <a name="enabling-the-application-integration-for-shiftplanning"></a>Abilitazione dell'integrazione dell'applicazione per ShiftPlanning
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per ShiftPlanning.

### <a name="to-enable-the-application-integration-for-shiftplanning-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per ShiftPlanning, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **ShiftPlanning**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **ShiftPlanning**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a ScreenSteps tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **ShiftPlanning** nel portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a ShiftPlanning** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configure Single Sign-On")
3. Nella casella di testo **URL accesso ShiftPlanning** della pagina **Configura URL app** digitare l'URL usando il modello "*https://company.shiftplanning.com/includes/saml/*" e fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in ShiftPlanning** fare clic su **Scarica certificato** per scaricare il certificato e salvare il file di certificato sul computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di **ShiftPlanning** come amministratore.
6. Nel menu in alto fare clic su **Admin**.
   
   ![Amministratore](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")
7. In **Integrazione** fare clic su **Single Sign-On**.
   
   ![Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")
8. Nella sezione **Single Sign-On** , eseguire la procedura seguente:
   
   ![Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")
   
   1. Selezionare **SAML abilitato**.
   2. Selezionare **Consenti accesso tramite password**
   3. Nella finestra di dialogo **Configura accesso Single Sign-On in ShiftPlanning** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **SAML Issuer URL** (URL autorità di certificazione SAML).
   4. Nella finestra di dialogo **Configure single sign-on at ShiftPlanning** (Configura accesso Single Sign-On in ShiftPlanning) del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Remote Logout URL** (URL disconnessione remota).
   5. Creare un file con **codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato X.509**
   7. Fare clic su **Salva impostazioni**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a ShiftPlanning, è necessario eseguirne il provisioning in ShiftPlanning.  
Nel caso di ShiftPlanning, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **ShiftPlanning** come amministratore.
2. Fare clic su **Admin**.
   
   ![Admin](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")
3. Fare clic su **Personale**.
   
   ![Personale](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Staff")
4. In **Actions** (Azioni) fare clic su **Add Employee** (Aggiungi dipendente).
   
   ![Aggiungi dipendenti](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Add Employees")
5. Nella sezione **Aggiungi dipendenti** , eseguire la procedura seguente:
   
   ![Salva dipendenti](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Save Employees")
   
   1. Digitare il **nome**, il **cognome** e l'**indirizzo di posta elettronica** di un account di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
   2. Fare clic su **Salva dipendenti**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da ShiftPlanning per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-shiftplanning-perform-the-following-steps"></a>Per assegnare gli utenti a ShiftPlanning, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **ShiftPlanning ** fare clic su **Assign users** (Assegna utenti).
   
   ![Assegna utenti](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


