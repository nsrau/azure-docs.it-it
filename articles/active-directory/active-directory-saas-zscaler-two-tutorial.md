---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler Two| Microsoft Docs'
description: Informazioni su come usare Zscaler Two con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a8925d01def7b4e9b00584602fdd4ecbaecef57c


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler Two
Questa esercitazione descrive l'integrazione di Azure e Zscaler Two.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di ZScaler Two abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Zscaler Two potranno accedere all'applicazione tramite il sito aziendale di Zscaler Two (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Zscaler Two
2. Configurazione dell'accesso Single Sign-On
3. Configurazione delle impostazioni proxy
4. Configurazione del provisioning utente
5. Assegnazione degli utenti

![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800199.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-zscaler-two"></a>Abilitazione dell'integrazione dell'applicazione per Zscaler Two
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Zscaler Two.

### <a name="to-enable-the-application-integration-for-zscaler-two-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Zscaler Two, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-zscaler-two-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-zscaler-two-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-zscaler-two-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-zscaler-two-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Zscaler Two**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-zscaler-two-tutorial/IC800200.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Zscaler Two** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Zscaler Two](./media/active-directory-saas-zscaler-two-tutorial/IC800201.png "ZScaler Two")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Zscaler Two tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 sul proprio tenant Zscaler Two.  
Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **ZScaler Two** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800202.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Zscaler Two** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800203.png "Configure Single Sign-On")
3. Nella casella di testo **URL di accesso Zscaler Two** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Zscaler Two e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-zscaler-two-tutorial/IC800204.png "Configure App URL")
   
   > [!NOTE]
   > Se necessario, è possibile ottenere il valore effettivo per l'ambiente in uso dal team di supporto Zscaler Two.
   > 
   > 
4. Nella pagina **Configura accesso Single Sign-On in Zscaler Two** fare clic su **Scarica certificato** e quindi salvare il file di certificato sul computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800205.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Zscaler Two come amministratore.
6. Scegliere **Amministrazione**dal menu disponibile nella parte superiore.
   
   ![Amministrazione](./media/active-directory-saas-zscaler-two-tutorial/IC800206.png "Administration")
7. In **Manage Administrators & Roles** (Gestisci amministratori e ruoli) fare clic su **Manage Users & Authentication** (Gestisci utenti e autenticazione).
   
   ![Manage Users & Authentication](./media/active-directory-saas-zscaler-two-tutorial/IC800207.png "Manage Users & Authentication")
8. Nella sezione **Choose Authentication Options for your Organization** seguire questa procedura:
   
   ![Autenticazione](./media/active-directory-saas-zscaler-two-tutorial/IC800208.png "Authentication")
   
   1. Selezionare **Authenticate using SAML Single Sign-On**.
   2. Fare clic su **Configure SAML Single Sign-On Parameters**.
9. Nella pagina della finestra di dialogo **Configure SAML Single Sign-On Parameters** (Configura parametri Single Sign-On SAML) procedere come descritto di seguito e quindi fare clic su **Done** (Chiudi):
   
   ![Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800209.png "Single Sign-On")
   
   1. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in ZScaler Two** del portale di Azure classico copiare il valore **URL richiesta di autenticazione** e quindi incollarlo nella casella di testo **URL of the SAML Portal to which users are sent for authentication** (URL del portale di SAML a cui vengono indirizzati gli utenti per l'autenticazione).
   2. Nella casella di testo **Attribute containing Login Name** digitare **NameID**.
   3. Per caricare il certificato scaricato fare clic su **Zscaler pem**.
   4. Selezionare **Enable SAML Auto-Provisioning**.
10. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:
    
    ![Amministrazione](./media/active-directory-saas-zscaler-two-tutorial/IC800210.png "Administration")
    
    1. Fare clic su **Save**.
    2. Fare clic su **Attiva subito**.
11. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in ZScaler Two** del portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-two-tutorial/IC800211.png "Configure Single Sign-On")

## <a name="configuring-proxy-settings"></a>Configurazione delle impostazioni proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni proxy in Internet Explorer
1. Avviare **Internet Explorer**.
2. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.
   
   ![Opzioni Internet](./media/active-directory-saas-zscaler-two-tutorial/IC769492.png "Internet Options")
3. Fare clic sulla scheda **Connessioni** .
   
   ![Connessioni](./media/active-directory-saas-zscaler-two-tutorial/IC769493.png "Connections")
4. Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.
5. Nella sezione del server proxy seguire questa procedura:
   
   ![Server proxy](./media/active-directory-saas-zscaler-two-tutorial/IC769494.png "Proxy server")
   
   1. Selezionare Usa un server di proxy per la rete LAN.
   2. Nella casella di testo Indirizzo digitare **gateway.zscalerone.net**.
   3. Nella casella di testo Porta digitare **80**.
   4. Selezionare **Ignora server proxy per indirizzi locali**.
   5. Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)**.
6. Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Zscaler Two, è necessario eseguirne il provisioning in Zscaler Two.  
Nel caso di Zscaler Two, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al tenant **Zscaler** .
2. Fare clic su **Administration**.
   
   ![Amministrazione](./media/active-directory-saas-zscaler-two-tutorial/IC781035.png "Administration")
3. Fare clic su **User Management**.
   
   ![Add](./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Add")
4. Nella scheda **Users** (Utenti) fare clic su **Add** (Aggiungi).
   
   ![Add](./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Add")
5. Nella sezione Add User seguire questa procedura:
   
   ![Aggiunta di un utente](./media/active-directory-saas-zscaler-two-tutorial/IC781038.png "Add User")
   
   1. Digitare **UserID** (ID utente), **User Display Name** (Nome visualizzato utente), **Password**, **Confirm Password** (Conferma password) e quindi selezionare **Groups** (Gruppi) e **Department** (Reparto) di un account AAD valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Salva**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zscaler Two per eseguire il provisioning degli account utente Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-zscaler-two-perform-the-following-steps"></a>Per assegnare gli utenti a Zscaler Two, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Zscaler Two** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-zscaler-two-tutorial/IC800212.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-zscaler-two-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


