---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zscaler One | Microsoft Docs'
description: Informazioni su come usare Zscaler One con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a73d4c057aeff7de712632ec3711f636e6aadf90


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler One
Questa esercitazione descrive l'integrazione di Azure e Zscaler One.  
 Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:  

* Sottoscrizione di Azure valida
* Sottoscrizione di ZScaler One abilitata per l'accesso Single Sign-On  

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Zscaler One potranno accedere all'applicazione tramite il sito aziendale di Zscaler One (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)  

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:  

1. Abilitazione dell'integrazione dell'applicazione per Zscaler One
2. Configurazione dell'accesso Single Sign-On
3. Configurazione delle impostazioni proxy
4. Configurazione del provisioning utente
5. Assegnazione degli utenti  

![Scenario](./media/active-directory-saas-zscaler-one-tutorial/IC800214.png "Scenario")  

## <a name="enabling-the-application-integration-for-zscaler-one"></a>Abilitazione dell'integrazione dell'applicazione per Zscaler One
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Zscaler One.  

### <a name="to-enable-the-application-integration-for-zscaler-one-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Zscaler One, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.  
   
   ![Active Directory](./media/active-directory-saas-zscaler-one-tutorial/IC700993.png "Active Directory")  
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.  
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.  
   
   ![Applications](./media/active-directory-saas-zscaler-one-tutorial/IC700994.png "Applications")  
4. Fare clic su **Add** nella parte inferiore della pagina.  
   
   ![Aggiungi applicazione](./media/active-directory-saas-zscaler-one-tutorial/IC749321.png "Add application")  
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.  
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-zscaler-one-tutorial/IC749322.png "Add an application from gallerry")  
6. Nella **casella di ricerca** digitare **ZScaler One**.  
   
   ![Raccolta di applicazioni](./media/active-directory-saas-zscaler-one-tutorial/IC800215.png "Application Gallery")  
7. Nel riquadro dei risultati selezionare **ZScaler One** e quindi fare clic su **Completa** per aggiungere l'applicazione.  
   
   ![Zscaler One](./media/active-directory-saas-zscaler-one-tutorial/IC800216.png "ZScaler One")  

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Zscaler One tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 sul proprio tenant Zscaler One.  
Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)  

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **ZScaler One** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.  
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-one-tutorial/IC800217.png "Configure Single Sign-On")  
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Zscaler One** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.  
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-one-tutorial/IC800218.png "Configure Single Sign-On")  
3. Nella casella di testo **URL di accesso Zscaler One** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Zscaler One e quindi fare clic su **Avanti**.  
   
   ![Configura URL app](./media/active-directory-saas-zscaler-one-tutorial/IC800219.png "Configure App URL")  
   
   > [!NOTE]
   > Se necessario, è possibile ottenere il valore effettivo per l'ambiente in uso dal team di supporto Zscaler One.  
   > 
   > 
4. Nella pagina **Configura accesso Single Sign-On in Zscaler One** fare clic su **Scarica certificato** e quindi salvare il file di certificato nel computer.  
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-one-tutorial/IC800220.png "Configure Single Sign-On")  
5. In un'altra finestra del Web browser accedere al sito aziendale di Zscaler One come amministratore.  
6. Scegliere **Amministrazione**dal menu disponibile nella parte superiore.  
   
   ![Amministrazione](./media/active-directory-saas-zscaler-one-tutorial/IC800206.png "Administration")  
7. In **Manage Administrators & Roles** (Gestisci amministratori e ruoli) fare clic su **Manage Users & Authentication** (Gestisci utenti e autenticazione).  
   
   ![Manage Users & Authentication](./media/active-directory-saas-zscaler-one-tutorial/IC800207.png "Manage Users & Authentication")  
8. Nella sezione **Choose Authentication Options for your Organization** seguire questa procedura:  
   
   ![Autenticazione](./media/active-directory-saas-zscaler-one-tutorial/IC800208.png "Authentication")  
   
   1. Selezionare **Authenticate using SAML Single Sign-On**.  
   2. Fare clic su **Configure SAML Single Sign-On Parameters**.  
9. Nella pagina della finestra di dialogo **Configure SAML Single Sign-On Parameters** (Configura parametri Single Sign-On SAML) procedere come descritto di seguito e quindi fare clic su **Done** (Chiudi):  
   
   ![Single Sign-On](./media/active-directory-saas-zscaler-one-tutorial/IC800209.png "Single Sign-On")  
   
   1. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in ZScaler One** del portale di Azure classico copiare il valore **URL richiesta di autenticazione** e quindi incollarlo nella casella di testo **URL of the SAML Portal to which users are sent for authentication** (URL del portale di SAML a cui vengono indirizzati gli utenti per l'autenticazione).  
   2. Nella casella di testo **Attribute containing Login Name** (Attributo contenente il nome di accesso) digitare **NameID** (ID nome).  
   3. Per caricare il certificato scaricato fare clic su **Zscaler pem**.  
   4. Selezionare **Enable SAML Auto-Provisioning**.  
10. Nella pagina della finestra di dialogo **Configure User Authentication** seguire questa procedura:  
    
    ![Amministrazione](./media/active-directory-saas-zscaler-one-tutorial/IC800210.png "Administration")  
    
    1. Fare clic su **Save**.  
    2. Fare clic su **Attiva subito**.  
11. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in ZScaler One** del portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa**.  
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-zscaler-one-tutorial/IC800221.png "Configure Single Sign-On")  

## <a name="configuring-proxy-settings"></a>Configurazione delle impostazioni proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni proxy in Internet Explorer
1. Avviare **Internet Explorer**.  
2. Selezionare **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet**.  
   
   ![Opzioni Internet](./media/active-directory-saas-zscaler-one-tutorial/IC769492.png "Internet Options")  
3. Fare clic sulla scheda **Connessioni** .  
   
   ![Connessioni](./media/active-directory-saas-zscaler-one-tutorial/IC769493.png "Connections")  
4. Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN**.  
5. Nella sezione del server proxy seguire questa procedura:  
   
   ![Server proxy](./media/active-directory-saas-zscaler-one-tutorial/IC769494.png "Proxy server")  
   
   1. Selezionare Usa un server di proxy per la rete LAN.  
   2. Nella casella di testo Indirizzo digitare **gateway.zscalerone.net**.  
   3. Nella casella di testo Porta digitare **80**.  
   4. Selezionare **Ignora server proxy per indirizzi locali**.  
   5. Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)**.  
6. Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet**.  

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Zscaler One, è necessario eseguirne il provisioning in Zscaler One.  
 Nel caso di Zscaler One, il provisioning è un'attività manuale.  

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al tenant **Zscaler One** .  
2. Fare clic su **Amministrazione**.  
   
   ![Amministrazione](./media/active-directory-saas-zscaler-one-tutorial/IC781035.png "Administration")  
3. Fare clic su **User Management**.  
   
   ![Add](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Add")  
4. Nella scheda **Users** (Utenti) fare clic su **Add** (Aggiungi).  
   
   ![Add](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Add")  
5. Nella sezione Add User seguire questa procedura:  
   
   ![Aggiunta di un utente](./media/active-directory-saas-zscaler-one-tutorial/IC781038.png "Add User")  
   
   1. Digitare **UserID** (ID utente), **User Display Name** (Nome visualizzato utente), **Password**, **Confirm Password** (Conferma password) e quindi selezionare **Groups** (Gruppi) e **Department** (Reparto) di un account Azure AD valido di cui si vuole eseguire il provisioning.  
   2. Fare clic su **Save**.  

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zscaler One per eseguire il provisioning degli account utente Azure AD.  
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.  

### <a name="to-assign-users-to-zscaler-one-perform-the-following-steps"></a>Per assegnare gli utenti a Zscaler One, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.  
2. Nella pagina di integrazione dell'applicazione **ZScaler One** fare clic su **Assegna utenti**.  
   
   ![Assegna utenti](./media/active-directory-saas-zscaler-one-tutorial/IC800222.png "Assign Users")  
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.  
   
   ![Sì](./media/active-directory-saas-zscaler-one-tutorial/IC767830.png "Yes")  

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).  




<!--HONumber=Nov16_HO3-->


