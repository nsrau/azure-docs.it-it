---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kintone | Microsoft Docs'
description: Informazioni su come usare Kintone con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7a25e030e8ade95db2ac9cd58fe4d94bbb7775ba


---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Esercitazione: Integrazione di Azure Active Directory con Kintone
Questa esercitazione descrive l'integrazione di Azure e Kintone.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Kintone abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Kintone potranno accedere all'applicazione tramite il sito aziendale di Kintone (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Kintone
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenario")

## <a name="enabling-the-application-integration-for-kintone"></a>Abilitazione dell'integrazione dell'applicazione per Kintone
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Kintone.

### <a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Kintone, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications](./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungi applicazione](./media/active-directory-saas-kintone-tutorial/IC749321.png "Add application")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-kintone-tutorial/IC749322.png "Add an application from gallerry")

6. Nella **casella di ricerca** digitare **Kintone**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-kintone-tutorial/IC785867.png "Application Gallery")

7. Nel riquadro dei risultati selezionare **Kintone** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Kintone tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Kintone** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configure Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Kintone** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configure Single Sign-On")

3. Nella casella di testo **URL di accesso Kintone** della pagina **Configure App URL** (Configura URL app) digitare l'URL usando il modello "*https://company.kintone.com*" e quindi fare clic su **Avanti**.
   
    ![Configura URL app](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configure App URL")

4. Nella pagina **Configura accesso Single Sign-On in Kintone** fare clic su **Scarica certificato** per scaricare il file di certificato e quindi salvarlo nel computer.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configure Single Sign-On")

5. In un'altra finestra del Web browser accedere al sito aziendale di **Kintone** come amministratore.

6. Fare clic su **Impostazioni**.
   
    ![Impostazioni](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

7. Fare clic su **Amministrazione utenti e di sistema**.
   
    ![Amministrazione utenti e di sistema](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration")

8. In **Amministrazione di sistema \> Sicurezza** fare clic su **Accesso**.
   
    ![Login](./media/active-directory-saas-kintone-tutorial/IC785881.png "Login")

9. Fare clic su **Abilita autenticazione SAML**.
   
    ![Autenticazione SAML](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML Authentication")

10. Nella sezione SAML Authentication seguire questa procedura:
    
    ![SAML Authentication](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML Authentication")
    
    1. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Kintone** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Login URL** (URL di accesso).
   
    2. Nella finestra di dialogo **Configura accesso Single Sign-On in Kintone** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Logout URL** (URL disconnessione).
    
    3. Per caricare il certificato scaricato, fare clic su **Browse** .
    
    4. Fare clic su **Salva**.

11. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configure Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Kintone, è necessario eseguirne il provisioning in Kintone.  
Nel caso di Kintone, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **Kintone** come amministratore.

2. Fare clic su **Impostazioni**.
   
    ![Impostazioni](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

3. Fare clic su **Amministrazione utenti e di sistema**.
   
    ![User & System Administration](./media/active-directory-saas-kintone-tutorial/IC785880.png "User & System Administration")

4. In **Amministrazione Utente** fare clic su **Reparti e Utenti**.
   
    ![Department & Users](./media/active-directory-saas-kintone-tutorial/IC785888.png "Department & Users")

5. Fare clic su **Nuovo utente**.
   
    ![New Users](./media/active-directory-saas-kintone-tutorial/IC785889.png "New Users")

6. Nella sezione **Nuovo utente** seguire questa procedura:
   
    ![New Users](./media/active-directory-saas-kintone-tutorial/IC785890.png "New Users")
   
    1. Nelle caselle di testo **Nome visualizzato**, **Nome di accesso**, **Nuova Password**, **Conferma Password** e **indirizzo e-mail** digitare il nome visualizzato, il nome di accesso, la nuova password, la conferma password e l'indirizzo di posta elettronica oltre a tutti gli altri dettagli di un account AAD valido di cui si vuole eseguire il provisioning.
 
    2. Fare clic su **Salva**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Kintone per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Per assegnare gli utenti a Kintone, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.

2. Nella **pagina di integrazione dell'applicazione Kintone** fare clic su **Assegna utenti**.
   
    ![Assegna utenti](./media/active-directory-saas-kintone-tutorial/IC785891.png "Assign Users")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-kintone-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


