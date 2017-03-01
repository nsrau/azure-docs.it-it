---
title: 'Esercitazione: Integrazione di Azure Active Directory con FreshService | Documentazione Microsoft'
description: Informazioni su come usare FreshService con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 64421f1b7424751332b76508ae65d009a3e1f48b
ms.openlocfilehash: df90d455f2c3bdbc59c38447fe65bf1b64979249
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Esercitazione: Integrazione di Azure Active Directory con FreshService
Questa esercitazione descrive l'integrazione di Azure e FreshService.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di FreshService abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a FreshService potranno eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per FreshService
* Configurazione dell'accesso Single Sign-On
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scenario")

## <a name="enable-the-application-integration-for-freshservice"></a>Abilitare l'integrazione dell'applicazione per FreshService
Questa sezione descrive come abilitare l'integrazione dell'applicazione per FreshService.

**Per abilitare l'integrazione dell'applicazione per FreshService, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **FreshService**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **FreshService** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Freshservice](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a FreshService tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. 

La configurazione dell'accesso Single Sign-On per FreshService richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **FreshService** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a FreshService** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di accesso FreshService** della pagina **Configure App URL** (Configura URL app) digitare l'URL usato dagli utenti per accedere all'applicazione FreshService, ad esempio **http://democompany.freshservice.com/**, e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in FreshService** fare clic su **Scarica certificato** e quindi salvare il file del certificato nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di FreshService come amministratore.
6. Nel menu in alto fare clic su **Admin**.
   
   ![Amministratore](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Amministratore")
7. Nel **portale dei clienti** fare clic su **Security** (Sicurezza).
   
   ![Sicurezza](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Sicurezza")
8. Nella sezione **Security** seguire questa procedura:
   
   ![Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign-On")
   
   1. Passare a **Single Sign-On**.
   2. Selezionare **SAML SSO**.
   3. Nella finestra di dialogo **Configura accesso Single Sign-On in FreshService** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **SAML Login URL** (URL accesso SAML).
   4. Nella finestra di dialogo **Configura accesso Single Sign-On in FreshService** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Logout URL** (URL di disconnessione).
   5. Copiare il valore di **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Security Certificate Fingerprint** (Impronta digitale del certificato sicurezza).
   
      >[!TIP]
      >Per informazioni dettagliate, vedere [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)
      >
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a FreshService, è necessario eseguirne il provisioning in FreshService. Nel caso di FreshService, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **FreshService** come amministratore.
2. Nel menu in alto fare clic su **Admin**.
   
   ![Amministratore](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Amministratore")
3. Nella sezione **User Management** (Gestione utenti) fare clic su **Requesters** (Richiedenti).
   
   ![Requesters](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Requesters")
4. Fare clic su **Nuovo Requester**.
   
   ![New Requesters](./media/active-directory-saas-freshservice-tutorial/IC790819.png "New Requesters")
5. Nella sezione **New Requester** seguire questa procedura:
   
   ![New Requester](./media/active-directory-saas-freshservice-tutorial/IC790820.png "New Requester")   
   1. Nelle caselle **First Name** e **Email** immettere il nome e l'indirizzo di posta elettronica di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Salva**.
   
    >[!NOTE]
    >Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
    >  

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da FreshService per eseguire il provisioning degli account utente di AAD.
>  

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a FreshService, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **FreshService** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


