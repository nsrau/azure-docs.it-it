---
title: 'Esercitazione: Integrazione di Azure Active Directory con IdeaScale | Documentazione Microsoft'
description: Informazioni su come usare IdeaScale con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2695483b7dad4418bf24f21ebe808dd145fb9503
ms.openlocfilehash: 6362e5b6232a3a42fdbad3cf0e5a36a2e4b178b8
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Esercitazione: Integrazione di Azure Active Directory con IdeaScale
Questa esercitazione descrive l'integrazione di Azure e IdeaScale.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di IdeaScale abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a IdeaScale potranno eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

*  Abilitazione dell'integrazione dell'applicazione per IdeaScale
*  Configurazione dell'accesso Single Sign-On
*  Configurazione del provisioning utente
*  Assegnazione degli utenti

![Scenario](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scenario")

## <a name="enable-the-application-integration-for-ideascale"></a>Abilitare l'integrazione dell'applicazione per IdeaScale
Questa sezione descrive come abilitare l'integrazione dell'applicazione per IdeaScale.

**Per abilitare l'integrazione dell'applicazione per IdeaScale, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **IdeaScale**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **IdeaScale** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a IdeaScale tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  

La configurazione dell'accesso Single Sign-On per IdeaScale richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **IdeaScale** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a IdeaScale** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di accesso IdeaScale** della pagina **Configure App URL** (Configura URL app) digitare l'URL usato dagli utenti per accedere all'applicazione IdeaScale, ad esempio "*https://company.IdeaScale.com*", e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in IdeaScale** fare clic su **Scarica metadati** per scaricare il file di metadati e salvarlo nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di IdeaScale come amministratore.
6. Passare a **Impostazioni Community**.
   
   ![Impostazioni Community](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Impostazioni Community")
7. Passare a **Security (Sicurezza) \> Single Signon Settings (Impostazioni di Single Sign-O)**.
   
   ![Impostazioni di Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Impostazioni di Single Sign-On")
8. In **Single-Signon Type** (Tipo di accesso Single Sign-On) selezionare **SAML 2.0**.
   
   ![Single Signon Type](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single Signon Type")
9. Nella finestra di dialogo **Impostazioni di Single Sign-O** seguire questa procedura:
   
   ![Impostazioni di Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Impostazioni di Single Sign-On")
   
   1. Nella finestra di dialogo **Configura accesso Single Sign-On in IdeaScale** del portale di Azure classico copiare il valore di **ID entità** e incollarlo nella casella di testo **SAML IdP Entity ID** (ID entità IdP SAML).
   2. Copiare il contenuto del file dei metadati scaricato e incollarlo nella casella di testo **SAML IdP Metadata** .
   3. Nella finestra di dialogo **Configura accesso Single Sign-On in IdeaScale** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Logout Success URL** (URL disconnessione riuscita).
   4. Fare clic su **Salva modifiche**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurare l'accesso Single Sign-On")
    
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a IdeaScale, è necessario eseguirne il provisioning in IdeaScale. Nel caso di IdeaScale, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **IdeaScale** come amministratore.
2. Passare a **Impostazioni Community**.
   
   ![Impostazioni Community](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Impostazioni Community")
3. Passare a **Basic Settings (Impostazioni di base) \> Member Management (Gestione membri)**.
4. Fare clic su **Aggiungi membro**.
   
   ![Member Management](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Member Management")
5. Nella sezione Add New Member seguire questa procedura:
   
   ![Add New Member](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Add New Member")
   
   1. Nella casella di testo **Indirizzi email** digitare l'indirizzo di posta elettronica di un account di AAD valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Salva modifiche**. 
   
    >[!NOTE]
    >Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
    >  

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da IdeaScale per eseguire il provisioning degli account utente di AAD.
>  

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a IdeaScale, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **IdeaScale** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


