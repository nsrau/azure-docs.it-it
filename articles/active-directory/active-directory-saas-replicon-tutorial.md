---
title: 'Esercitazione: Integrazione di Azure Active Directory con Replicon | Documentazione Microsoft'
description: Informazioni su come usare Replicon con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2aeeceb61191962b62892b8409218684f76c6fa8
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017

---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Esercitazione: Integrazione di Azure Active Directory con Replicon
In questa esercitazione viene illustrata l'integrazione di Azure e Replicon. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Replicon

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Replicon saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Replicon (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Replicon
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-replicon-tutorial/IC777798.png "Scenario")

## <a name="enable-the-application-integration-for-replicon"></a>Abilitare l'integrazione dell'applicazione per Replicon
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Replicon.

**Per abilitare l'integrazione dell'applicazione per Replicon, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-replicon-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-replicon-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-replicon-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Replicon**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-replicon-tutorial/IC777799.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Replicon**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Replicon tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Replicon** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-replicon-tutorial/IC777801.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Replicon** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-replicon-tutorial/IC777802.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** seguire questa procedura:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-replicon-tutorial/IC777803.png "Configurare l'URL dell'app")
  1. Nella casella di testo dell'**URL di accesso a Replicon** digitare l'URL del tenant Replicon (ad esempio: *https://na2.replicon.com/company/saml2/sp-sso/post*).
  2. Nella casella di testo **Replicon Reply URL** (URL di risposta Replicon) digitare l'URL **AssertionConsumerService** di Replicon, ad esempio *https://global.replicon.com/!/saml2/company/sso/post*.  
      
     >[!NOTE]
     >È possibile ottenere l'URL dai metadati Replicon in: **https://global.replicon.com/!/saml2/\<YourCompanyKey\>**.
     > 
     > 
 
  3. Fare clic su **Avanti**.

4. Nella pagina **Configura accesso Single Sign-On in Replicon** fare clic su **Scarica metadati** per scaricare il file di metadati e salvare i metadati nel computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-replicon-tutorial/IC777804.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Replicon come amministratore.

6. Per configurare SAML 2.0, eseguire la procedura seguente:
   
    ![Abilita autenticazione SAML](./media/active-directory-saas-replicon-tutorial/IC777805.png "autenticazione SAML abilitare")
  
  1. Per visualizzare la finestra di dialogo **EnableSAMLAuthentication2** aggiungere quanto segue all'URL, dopo la chiave dell'azienda: **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**  
    * Di seguito è illustrato lo schema dell'URL completo:  
   **https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
   2. Fare clic su **+** per espandere la sezione **v20Configuration**.
   3. Fare clic su **+** per espandere la sezione **metaDataConfiguration**.
   4. Fare clic su **Scegli file** per selezionare il file XML dei metadati del provider di identità, quindi scegliere **Invia**.

7. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-replicon-tutorial/IC778418.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Replicon, è necessario eseguirne il provisioning in Replicon.  

Nel caso di Replicon, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. In una finestra del Web browser accedere al sito aziendale di Replicon come amministratore.
2. Passare ad **Amministrazione \> Utenti**.
   
    ![Utenti](./media/active-directory-saas-replicon-tutorial/IC777806.png "Utenti")
3. Fare clic su **+Aggiungi utente**.
   
    ![Aggiungere un utente](./media/active-directory-saas-replicon-tutorial/IC777807.png "Aggiungere un utente")
4. Nella sezione **Profilo utente** , eseguire la procedura seguente:
   
    ![Profilo utente](./media/active-directory-saas-replicon-tutorial/IC777808.png "Profilo utente")
   
  1. Nella casella di testo **Nome accesso** , digitare l’indirizzo di posta elettronica dell'utente di Azure AD di cui si desidera eseguire il provisioning.
  2. In **Tipo di autenticazione** selezionare **SSO**.
  3. Nella casella di testo **Reparto** , digitare il reparto dell'utente.
  4. In **Tipo di dipendente** selezionare **Amministratore**.
  5. Fare clic su **Salva profilo utente**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Replicon per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Replicon, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **Replicon** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-replicon-tutorial/IC777809.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-replicon-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


