---
title: 'Esercitazione: Integrazione di Azure Active Directory con Onit | Microsoft Docs'
description: Informazioni su come utilizzare Onit con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 80c727ba687e11f7407339b57bc1f6b0c091dd0f


---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Esercitazione: Integrazione di Azure Active Directory con Onit
In questa esercitazione viene illustrata l'integrazione di Azure e Onit  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Onit abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Onit saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Onit (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Onit
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-onit-tutorial/IC791166.png "Scenario")

## <a name="enabling-the-application-integration-for-onit"></a>Abilitazione dell'integrazione dell'applicazione per Onit
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Onit.

### <a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Onit eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-onit-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-onit-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-onit-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Onit**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-onit-tutorial/IC791167.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Onit**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Onit tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per Onit richiede di recuperare un valore di identificazione personale da un certificato.  
Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

L'applicazione Onit prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**.  
La schermata seguente illustra un esempio relativo a questa operazione.

![Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791168.png "Single Sign-On")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **Onit** del portale di Azure classico fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**.
   
   ![Attributi](./media/active-directory-saas-onit-tutorial/IC791169.png "Attributes")
2. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:

    |Nome attributo|Valore attributo|
    |---|---|
    |name|User.userprincipalname|
    |email|User.mail|

    1.  Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
    3.  Nell’elenco **Valore attributo** selezionare il valore dell'attributo indicato per quella riga.
    4.  Fare clic su **Complete**.

1. Fare clic su **Applica modifiche**.
2. Nel browser fare clic su **Indietro** per aprire nuovamente la finestra di dialogo **Avvio rapido**.
3. Fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791170.png "Configure Single Sign-On")
4. Nella pagina **Stabilire come si desidera che gli utenti accedano a Onit** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791171.png "Configure Single Sign-On")
5. Nella pagina **Configura URL app**, nella casella di testo **Onit Sign On URL** (URL di accesso a Onit) digitare l'URL usato dagli utenti per accedere all'applicazione Onit, ad esempio: "*https://ms-sso-test.onit.com*", quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-onit-tutorial/IC791172.png "Configure App URL")
6. Nella pagina **Configura accesso Single Sign-On in Onit** fare clic su **Scarica certificato** per scaricare il file del certificato, quindi salvarlo localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791173.png "Configure Single Sign-On")
7. In un'altra finestra del Web browser accedere al sito aziendale di Onit come amministratore.
8. Scegliere **Administration**dal menu disponibile nella parte superiore.
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")
9. Fare clic su **Edit Corporation**.
   
   ![Edit Corporation](./media/active-directory-saas-onit-tutorial/IC791175.png "Edit Corporation")
10. Fare clic sulla scheda **Security** .
    
    ![Modifica informazioni società](./media/active-directory-saas-onit-tutorial/IC791176.png "Edit Company Information")
11. Nella scheda **Sicurezza** eseguire la procedura seguente:
    
    ![Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791177.png "Single Sign-On")
    
    1. Come **strategia di autenticazione** selezionare **Single Sign-On e password**.
    2. Nella finestra di dialogo **Configure single sign-on at Onit** (Configura accesso Single Sign-On in Onit) del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Idp Target URL** (URL di destinazione IDP).
    3. Nella finestra di dialogo **Configure single sign-on at Onit** (Configura accesso Single Sign-On in Onit) del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Idp logout URL** (URL disconnessione IDP).
    4. Copiare il valore di **Identificazione personale** dal certificato esportato e incollarlo nella casella di testo relativa a **Idp Cert Fingerprint (SHA1)**.  
       
       > [!TIP]
       > Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)
       > 
       > 
    5. Come **SSO Type** selezionare **SAML**.
    6. Nella casella di testo relativa al **SSO login button text** digitare il testo desiderato per il pulsante.
    7. Selezionare **Login with SSO: Required for the following domains/users** (Accesso SSO: necessario per i seguenti domini/utenti), digitare l'indirizzo di posta elettronica di un utente test nella relativa casella di testo, quindi fare clic su **Update** (Aggiorna).
       ![Edit Corporation](./media/active-directory-saas-onit-tutorial/IC791178.png "Edit Corporation")
12. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-onit-tutorial/IC791179.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Onit, è necessario eseguirne il provisioning in Onit.  
Nel caso di Onit, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **Onit** come amministratore.
2. Fare clic su **Add User**.
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")
3. Nella pagina della finestra di dialogo **Aggiungi utente** eseguire la procedura seguente:
   
   ![Aggiunta di un utente](./media/active-directory-saas-onit-tutorial/IC791181.png "Add User")
   
   1. Digitare il **nome** e l'**indirizzo e-mail** di un account AAD valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
   2. Fare clic su **Create**(Crea).  
      
      > [!NOTE]
      > Il titolare dell'account riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
      > 
      > 

> [!NOTE]
> È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Onit o le API fornite da Onit per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-onit-perform-the-following-steps"></a>Per assegnare gli utenti a Onit, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Onit** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-onit-tutorial/IC791182.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-onit-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


