---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile | Microsoft Docs'
description: Informazioni su come usare Citrix ShareFile con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 62f9c25a4c1f34cad266c9948cdf82320f5e7011
ms.openlocfilehash: 0c46640d258a18ecbd3b0f8683412544272c63e0
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile
Questa esercitazione descrive l'integrazione di Azure e Citrix ShareFile.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Citrix ShareFile

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Citrix ShareFile potranno accedere all'applicazione tramite il sito aziendale di Citrix ShareFile (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Citrix ShareFile
* Configurazione dell'accesso Single Sign-On (SSO)
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenario")

## <a name="enable-the-application-integration-for-citrix-sharefile"></a>Abilitare l'integrazione dell'applicazione per Citrix ShareFile
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Citrix ShareFile.

**Per abilitare l'integrazione dell'applicazione per Citrix ShareFile, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
=   ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Citrix ShareFile**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Citrix ShareFile** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Citrix ShareFile tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Citrix ShareFile** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Abilitare l'accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Abilitare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Citrix ShareFile** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di accesso Citrix ShareFile** della pagina **Configura URL** app digitare l'URL usando il modello seguente `https://<tenant-name>.shareFile.com` e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Citrix ShareFile** fare clic su **Scarica certificato** per scaricare il file di certificato e quindi salvarlo nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "Configura accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di **Citrix ShareFile** come amministratore.
6. Nel barra degli strumenti in alto fare clic su **Admin**.
7. Nel riquadro di spostamento sinistro selezionare **Configure Single Sign-On**.
   
   ![Account amministratore](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Account amministratore")
8. In **Basic Settings** della finestra di dialogo **Single Sign-On/ SAML 2.0 Configuration** seguire questa procedura:
   
   ![Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Single Sign-On")
   1. Fare clic su **Enable SAML**.
   2. Nella finestra di dialogo **Configure single sign-on at Citrix ShareFile** (Configura accesso Single Sign-On in Citrix ShareFile) del portale di Azure classico copiare il valore di **ID entità** e incollarlo nella casella di testo **Your IDP Issuer/ Entity ID** (Autorità di certificazione IDP/ID entità).
   3. Nella finestra di dialogo **Configure single sign-on at Citrix ShareFile** (Configura accesso Single Sign-On in Citrix ShareFile) del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Login URL** (URL di accesso).
   4. Nella finestra di dialogo **Configure single sign-on at Citrix ShareFile** (Configura accesso Single Sign-On in Citrix ShareFile) del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Logout URL** (URL di disconnessione).
   5. Fare clic su **Cambia** accanto al campo **Certificato X.509** e quindi caricare il certificato scaricato dal portale di Azure classico. 
   
      ![Impostazioni di base](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Impostazioni di base")
9. Fare clic su **Save** nel portale di gestione di Citrix ShareFile.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configurare l'accesso Single Sign-On")
    
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Citrix ShareFile, è necessario eseguirne il provisioning in Citrix ShareFile.  

* Nel caso di Citrix ShareFile, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Citrix ShareFile** .
2. Fare clic su **Manage Users \> Manage Users Home \> + Create Employee** (Gestisci utenti > Gestisci home utenti > + Crea dipendente).
   
   ![Crea dipendente](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Crea dipendente")
3. Nelle caselle di testo **Email**, **First name** e **Last name** digitare rispettivamente l'indirizzo di posta elettronica, il nome e il cognome relativi a un account Azure AD valido di cui si vuole eseguire il provisioning.
   
   ![Informazioni di base](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Informazioni di base")
4. Fare clic su **Aggiungi utente**.
   >[!NOTE]
   >Il titolare dell'account AAD riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. 
   > 

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Citrix ShareFile per eseguire il provisioning degli account utente di Azure AD.
>  

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Citrix ShareFile, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Citrix ShareFile** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


