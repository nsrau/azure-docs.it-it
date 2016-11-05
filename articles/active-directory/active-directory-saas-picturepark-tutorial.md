---
title: 'Esercitazione: Integrazione di Azure Active Directory con Picturepark | Microsoft Docs'
description: Informazioni su come usare Picturepark con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-picturepark"></a>Esercitazione: Integrazione di Azure Active Directory con Picturepark
In questa esercitazione viene illustrata l'integrazione di Azure e Picturepark.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Picturepark

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Picturepark saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Picturepark (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Picturepark
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scenario")

## <a name="enabling-the-application-integration-for-picturepark"></a>Abilitazione dell'integrazione dell'applicazione per Picturepark
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Picturepark.

### <a name="to-enable-the-application-integration-for-picturepark,-perform-the-following-steps:"></a>Per abilitare l'integrazione dell'applicazione per Picturepark, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Picturepark**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Picturepark**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Picturepark tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per Picturepark richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Picturepark** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Picturepark** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configure Single Sign-On")
3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **Picturepark Sign On URL** (URL di accesso a Picturepark) usando il modello "*http://company.picturepark.com*" e fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Picturepark** fare clic su **Scarica certificato** per scaricare il certificato e salvare il file del certificato localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Picturepark come amministratore.
6. Nella barra degli strumenti in alto fare clic su **Strumenti di amministrazione**, quindi fare clic su **Console di gestione**.
   
   ![Console di gestione](./media/active-directory-saas-picturepark-tutorial/IC795062.png "Management Console")
7. Fare clic su **Autenticazione**, quindi su **Provider di identità**.
   
   ![Autenticazione](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Authentication")
8. Nella sezione **Configurazione provider di identità** , eseguire questa procedura:
   
   ![Configurazione provider di identità](./media/active-directory-saas-picturepark-tutorial/IC795064.png "Identity provider configuration")
   
   1. Fare clic su **Aggiungi**.
   2. Digitare un nome per la configurazione.
   3. Selezionare **Imposta come predefinito**.
   4. Nella finestra di dialogo **Configure single sign-on at Picturepark** (Configura accesso Single Sign-On in Picturepark) del portale di Azure classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Issuer URI** (URI autorità di certificazione).
   5. Copiare il valore **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo relativa all'**identificazione personale dell'autorità di certificazione attendibile**.  
      
      > [!TIP]
      > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. Fare clic su **JoinDefaultUsersGroup**.
   7. Per impostare l'attributo **Emailaddress** nella casella di testo **Attestazione**, digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
      ![Configurazione](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuration")
   8. Fare clic su **Save**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Picturepark, è necessario eseguirne il provisioning in Picturepark.  
Nel caso di Picturepark, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts,-perform-the-following-steps:"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al tenant **Picturepark** .
2. Nella barra degli strumenti in alto fare clic su **Strumenti di amministrazione**, quindi su **Utenti**.
   
   ![Utenti](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Users")
3. Nella scheda **Panoramica utenti** fare clic su **Nuovo**.
   
   ![Gestione utente](./media/active-directory-saas-picturepark-tutorial/IC795068.png "User management")
4. Nella finestra di dialogo **Create User** seguire questa procedura:
   
   ![Crea utente](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Create User")
   
   1. Digitare l'**indirizzo e-mail**, la **password**, la **password di conferma**, il **nome**, il **cognome**, la **società**, il **paese**, il **CAP** e la **città** di un account utente di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
   2. Selezionare una **Language**.
   3. Fare clic su **Crea**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Picturepark per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-picturepark,-perform-the-following-steps:"></a>Per assegnare gli utenti a Picturepark, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Picturepark** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


