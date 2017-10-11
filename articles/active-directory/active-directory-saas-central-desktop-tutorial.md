---
title: 'Esercitazione: Integrazione di Azure Active Directory con Central Desktop | Documentazione Microsoft'
description: Informazioni su come usare Central Desktop con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: fe32c1d68040ceb9d9de2ad6c4a6dc9ea93f5aef
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Esercitazione: Integrazione di Azure Active Directory con Central Desktop
Questa esercitazione descrive l'integrazione di Azure e Central Desktop. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Central Desktop abilitata per l'accesso Single Sign-On e tenant di Central Desktop

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Central Desktop
* Configurazione dell'accesso Single Sign-On (SSO)
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")

## <a name="enable-the-application-integration-for-central-desktop"></a>Abilitare l'integrazione dell'applicazione per Central Desktop
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Central Desktop.

**Per abilitare l'integrazione dell'applicazione per Central Desktop, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Central Desktop**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Central Desktop** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Central Desktop tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

Come parte di questa procedura, verrà richiesto di caricare un file di certificato con codifica Base 64 nel tenant di Central Desktop.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nel portale di Azure classico, nel **Central Desktop** pagina di integrazione dell'applicazione, fare clic su **configurare single sign-on** per aprire il * * configurare Single Sign-On * * finestra di dialogo.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Central Desktop** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** seguire questa procedura e quindi fare clic su **Avanti**: 
   
   1. Nella casella di testo **URL accesso Central Desktop** digitare l'URL del tenant di Central Desktop, ad esempio *http://contoso.centraldesktop.com*.
   2. Nella casella di testo URL di risposta Central Desktop digitare l'URL AssertionConsumerService di Central Desktop, ad esempio https://contoso.centraldesktop.com/saml2-assertion.php.
   
   >[!NOTE]
   >È possibile ottenere il valore dai metadati di Central Desktop, ad esempio *http://contoso.centraldesktop.com*.
   >  
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Central Desktop** fare clic su **Scarica certificato** per scaricare il file di certificato e quindi salvarlo nel computer.
   
  ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configurare l'accesso Single Sign-On")
5. Accedere al tenant di **Central Desktop** .
6. Passare a **Settings** (Impostazioni), fare clic su **Advanced** (Avanzate) e quindi su **Single Sign On**.
   
  ![Installazione - Avanzate](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Installazione - Avanzate")
7. Nella pagina **Single Sign-On Settings** seguire questa procedura:
   
  ![Single Sign On Settings](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")
   
  1. Selezionare **Enable SAML v2 Single Sign On**.
  2. Nella pagina **Configura accesso Single Sign-On in Central Desktop** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **URL SSO**.
  3. Nella pagina **Configura accesso Single Sign-On in Central Desktop** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **SSO Login URL** (URL di accesso SSO).
  4. Nella pagina **Configura accesso Single Sign-On in Central Desktop** del portale di Azure classico copiare il valore di **URL servizio Single Sign-Out** e incollarlo nella casella di testo **SSO Logout URL** (URL disconnessione SSO).
8. Nella sezione **Message Signature Verification Method** seguire questa procedura:
   
   ![Message Signature Verification Method](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")
   
  1. Selezionare **Certificate**.
  2. Nell'elenco **SSO Certificate** (Certificato SSO) selezionare **RSH SHA256**.
  3. Creare un file di testo dal certificato scaricato, copiare il contenuto del file di testo e quindi incollarlo nel campo **SSO Certificate** .  
     >[!TIP]
     >Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
      >  
   4. Selezionare **Display a link to your SAMLv2 login page**.
9. Fare clic su **Aggiorna**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configurare l'accesso Single Sign-On")
    
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di AAD di accedere, è necessario eseguirne il provisioning nell'applicazione Central Desktop. Questa sezione descrive come creare account utente di AAD in Central Desktop.

**Per eseguire il provisioning degli account utente in Central Desktop:**
1. Accedere al tenant di Central Desktop.
2. Passare a **People \> Internal Members** (Persone > Membri interni).
3. Fare clic su **Add Internal Members**.
   
  ![Persone](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Persone")
4. Nella casella di testo **Email Address of New Members** (Indirizzo posta elettronica nuovi membri) digitare un account AAD di cui si vuole eseguire il provisioning e quindi fare clic su **Next** (Avanti).
   
  ![Email Addresses of New Members](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")
5. Fare clic su **Add Internal member(s)**.
   
  ![Add Internal Member](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")
   
   >[!NOTE]
   >Gli utenti aggiunti riceveranno un messaggio di posta elettronica che include un collegamento di conferma su cui dovranno fare clic per attivare l'account.
   > 

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Central Desktop per eseguire il provisioning degli account utente di Azure AD.
>  

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Central Desktop, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Central Desktop** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

