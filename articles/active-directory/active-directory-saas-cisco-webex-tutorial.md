---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cisco Webex | Documentazione Microsoft'
description: Informazioni su come usare Cisco Webex con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ada8ffbbdc5565a517f5a06cb640a36aaf8879b4
ms.openlocfilehash: b44b1a5b3e988a51db3325ec8a181651fa84e768
ms.contentlocale: it-it
ms.lasthandoff: 02/17/2017

---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Esercitazione: Integrazione di Azure Active Directory con Cisco Webex
Questa esercitazione descrive l'integrazione di Azure e Cisco Webex.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Cisco Webex

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Cisco Webex potranno accedere all'applicazione tramite il sito aziendale di Cisco Webex (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Cisco Webex
* Configurazione dell'accesso Single Sign-On (SSO)
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scenario")

## <a name="enable-the-application-integration-for-cisco-webex"></a>Abilitare l'integrazione dell'applicazione per Cisco Webex
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Cisco Webex.

**Per abilitare l'integrazione dell'applicazione per Cisco Webex, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Cisco Webex**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Cisco Webex** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Cisco Webex tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  

Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64. Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Cisco Webex** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Cisco Webex** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** seguire questa procedura e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configurare l'URL dell'app")   
   1. Nella casella di testo **URL di accesso** digitare l'URL del tenant di Cisco Webex, ad esempio *http://contoso.webex.com*.
   2. Nella casella di testo **URL di risposta Cisco Webex** digitare l'**URL AssertionConsumerService di Cisco Webex**, ad esempio *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*.
4. Nella pagina **Configura accesso Single Sign-On in Cisco Webex** fare clic su **Scarica certificato** e quindi salvare il file del certificato nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Cisco Webex come amministratore.
6. Nel menu in alto fare clic su **Site Administration**.
   
   ![Site Administration](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Site Administration")
7. Nella sezione **Manage Site** (Gestisci sito) fare clic su **SSO Configuration** (Configurazione SSO).
   
   ![SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO Configuration")
8. Nella sezione Federated Web SSO Configuration seguire questa procedura:
   
   ![Federated SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Federated SSO Configuration")  
   1. Nell'elenco **Federation Protocol** (Protocollo federazione) selezionare **SAML 2.0**.
   2. Creare un file **con codifica Base 64** dal certificato scaricato.  
    >[!TIP]
    >Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
    >  
   3. Aprire il certificato con codifica Base 64 nel Blocco note e quindi copiarne il contenuto.
   4. Fare clic su **Import SAML Metadata**e quindi incollare il certificato con codifica Base 64.
   5. Nella finestra di dialogo **Configura accesso Single Sign-On in Cisco Webex** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Issuer for SAML (IdP ID)** (Autorità di certificazione per SAML - ID IdP).
   6. Nella finestra di dialogo **Configura accesso Single Sign-On in Cisco Webex** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Customer SSO Service Login URL** (URL di accesso al servizio clienti SSO).
   7. Nell'elenco **NameID Format** (Formato NameID) selezionare **Email address** (Indirizzo di posta elettronica).
   8. Nella casella di testo **AuthnContextClassRef** digitare **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.
   9. Nella finestra di dialogo **Configura accesso Single Sign-On in Cisco Webex** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Customer SSO Service Logout URL** (URL di disconnessione dal servizio clienti SSO).
   10. Fare clic su **Aggiorna**.
9. Nella finestra di dialogo **Configura accesso Single Sign-On in Cisco Webex** del portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Cisco Webex, è necessario eseguirne il provisioning in Cisco Webex.  

* Nel caso di Cisco Webex, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Cisco Webex** .
2. Passare a **Manage Users (Gestisci utenti) \> Add User (Aggiungi utente)**.
   
   ![Aggiungere utenti](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Aggiungere utenti")
3. Nella sezione Add User seguire questa procedura:
   
   ![Aggiungere un utente](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Aggiungere un utente")   
   1. Per **Account Type** (Tipo di account), scegliere **Host**.
   2. Digitare i dati di un utente di Azure AD esistente nelle caselle di testo seguenti: **First name, Last name** (Nome, Cognome), **User name** (Nome utente), **Email** (Indirizzo di posta elettronica), **Password**, **Confirm Password** (Conferma password).
   3. Fare clic su **Aggiungi**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Cisco Webex per eseguire il provisioning degli account utente di Azure AD. 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Cisco Webex, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Cisco Webex** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


