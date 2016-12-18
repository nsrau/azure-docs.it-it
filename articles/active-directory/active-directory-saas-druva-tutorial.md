---
title: 'Esercitazione: Integrazione di Azure Active Directory con Druva | Documentazione Microsoft'
description: Informazioni su come usare Druva con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51e6da24517479c7fe47e811cc2355ccdeb961b3


---
# <a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Esercitazione: Integrazione di Azure Active Directory con Druva
Questa esercitazione descrive l'integrazione di Azure e Druva.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Druva abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Druva potranno accedere all'applicazione tramite il sito aziendale di Druva (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Druva
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-druva-tutorial/IC795084.png "Scenario")

## <a name="enabling-the-application-integration-for-druva"></a>Abilitazione dell'integrazione dell'applicazione per Druva
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Druva.

### <a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Druva, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-druva-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-druva-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-druva-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Druva**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-druva-tutorial/IC795085.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Druva** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Druva tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Per eseguire questa procedura, è necessario creare un certificato con codifica Base 64.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

L'applicazione Druva prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**.  
La schermata seguente illustra un esempio relativo a questa operazione.

![Attributi token SAML](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML Token Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Druva** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795027.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Druva** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795088.png "Configure Single Sign-On")
3. Nella casella di testo **Druva Sign On URL** (URL di accesso Druva) della pagina **Configura URL app** digitare l'URL utilizzato dagli utenti per accedere all'applicazione Druva, ad esempio "*https://cloud.druva.com/home/*", e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-druva-tutorial/IC795089.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Druva** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795090.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Druva come amministratore.
6. Passare a **Manage \> Settings**.
   
   ![Impostazioni](./media/active-directory-saas-druva-tutorial/IC795091.png "Settings")
7. Nella finestra di dialogo Single Sign-On Settings seguire questa procedura:
   
   ![Single Sign-On Settings](./media/active-directory-saas-druva-tutorial/IC795092.png "Singl Sign-On Settings")
   
   1. Nella finestra di dialogo **Configura accesso Single Sign-On in Druva** del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **ID Provider Login URL** (URL di accesso provider di identità).
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in Druva** del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **ID Provider Logout URL** (URL di disconnessione provider di identità).
   3. Creare un file **con codifica Base 64** dal certificato scaricato.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **ID Provider Certificate**
   5. Per aprire la pagina **Settings** fare clic su **Save**.
8. Nella pagina **Settings** fare clic su **Generate SSO Token**.
   
   ![Impostazioni](./media/active-directory-saas-druva-tutorial/IC795093.png "Settings")
9. Nella finestra di dialogo **Single Sign-on Authentication Token** eseguire la procedura seguente:
   
   ![SSO Token](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO Token")
   
   1. Fare clic su **Copy**.
   2. Fare clic su **Close**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795095.png "Configure Single Sign-On")
11. Nel menu in alto fare clic su **Attributi** to open the **SAML Token Attributi** .
    
    ![Attributi](./media/active-directory-saas-druva-tutorial/IC795096.png "Attributes")
12. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:
    
    | Nome attributo | Valore attributo |
    | --- | --- |
    | insync\_auth\_token |<*valore negli Appunti*> |
    
    1. Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
    2. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
    3. Nella casella di testo **Valore attributo** , digitare il valore dell'attributo indicato per la riga.
    4. Fare clic su **Complete**.
13. Fare clic su **Applica modifiche**.
    
    ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Druva, è necessario eseguirne il provisioning in Druva.  
Nel caso di Druva, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **Druva** come amministratore.
2. Passare a **Gestisci \> utenti**.
   
   ![Gestire gli utenti](./media/active-directory-saas-druva-tutorial/IC795097.png "Manage Users")
3. Fare clic su **Create New**.
   
   ![Manage Users](./media/active-directory-saas-druva-tutorial/IC795098.png "Manage Users")
4. Nella finestra di dialogo Create New User seguire questa procedura:
   
   ![Create New User](./media/active-directory-saas-druva-tutorial/IC795099.png "Create NewUser")
   
   1. Digitare l'indirizzo di posta elettronica e il nome di un account utente Azure Active Directory valido di cui si vuole effettuare il provisioning nelle caselle di testo corrispondenti.
   2. Fare clic su **Create User**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Druva per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-druva-perform-the-following-steps"></a>Per assegnare gli utenti a Druva, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Druva** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-druva-tutorial/IC795100.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-druva-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


