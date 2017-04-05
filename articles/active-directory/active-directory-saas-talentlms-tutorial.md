---
title: 'Esercitazione: Integrazione di Azure Active Directory con TalentLMS | Documentazione Microsoft'
description: Informazioni su come usare TalentLMS con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: a9de3004a1968f514227f0ba5dfde0f562a2b392
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Esercitazione: Integrazione di Azure Active Directory con TalentLMS
In questa esercitazione viene descritta l'integrazione di Azure e TalentLMS.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant TalentLMS

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a TalentLMS potranno accedere all'applicazione tramite il sito aziendale di TalentLMS (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per TalentLMS
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Scenario")

## <a name="enable-the-application-integration-for-talentlms"></a>Abilitare l'integrazione dell'applicazione per TalentLMS
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per TalentLMS.

**Per abilitare l'integrazione dell'applicazione per TalentLMS, eseguire la procedura seguente:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **TalentLMS**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **TalentLMS**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a TalentLMS con il relativo account in Azure AD usando la federazione basata sul protocollo SAML.

La configurazione dell'accesso SSO per TalentLMS richiede di recuperare un valore di identificazione personale da un certificato.  

Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **TalentLMS** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a TalentLMS** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL accesso TalentLMS** nel formato "*https://\<nome-tenant\>.TalentLMSapp.com*", quindi fare clic su **Avanti**.
   
    ![URL di accesso](./media/active-directory-saas-talentlms-tutorial/IC777294.png "URL di accesso")

4. Nella pagina **Configura accesso Single Sign-On in TalentLMS** fare clic su **Scarica certificato** per scaricare il file di certificato e quindi salvarlo localmente come **c:\\TalentLMS.cer**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configurare l'accesso Single Sign-On")

5. In un'altra finestra del Web browser accedere al sito aziendale di TalentLMS come amministratore.

6. Nella sezione **Account & Settings** (Account e impostazioni) fare clic sulla scheda **Users** (Utenti).
   
    ![Impostazioni e account](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Impostazioni e account")

7. Fare clic su **Single Sign-On (SSO)**.

8. Nella sezione Single Sign-On seguire questa procedura:
   
    ![Single Sign-On](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Single Sign-On")   
  1. Dall'elenco **SSO integration type** (Tipo integrazione SSO) selezionare **SAML 2.0**.
  2. Nella finestra di dialogo **Configura accesso Single Sign-On in TalentLMS** del portale di Azure classico copiare il valore di **ID provider di identità** e quindi incollarlo nella casella di testo **Identity provider (IdP)** (Provider di identità - IdP).
  3. Copiare il valore **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Impronta digitale del certificato**.
      
     >[!TIP]
     >Per informazioni dettagliate, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI). 
     >    

  4. Nella pagina finestra di dialogo **Configura accesso Single Sign-On in TalentLMS** del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Remote sign-in URL** (URL accesso remoto). 
  5. Nella pagina finestra di dialogo **Configura accesso Single Sign-On in TalentLMS** del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Remote sign-out URL** (URL disconnessione remota).
  6. Specificare le informazioni seguenti: 
    * Nella casella di testo **TargetedID** (ID destinazione) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    * Nella casella di testo **First Name** (Nome) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    * Nella casella di testo **Last Name** (Cognome) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    * Nella casella di testo **Email** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
  7. Fare clic su **Save**.

9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configurare l'accesso Single Sign-On")

## <a name="configure-user-provisioning"></a>Configura provisioning utenti
Per consentire agli utenti di Azure AD di accedere a TalentLMS, è necessario eseguirne il provisioning in TalentLMS.  

* Nel caso di TalentLMS, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **TalentLMS** .

2. Fare clic su **Users** (Utenti), quindi fare clic su **Add User** (Aggiungi utente).

3. Nella pagina della finestra di dialogo **Add User** seguire questa procedura:
   
    ![Aggiungere un utente](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Aggiungere un utente")   
  1. Digitare i valori di attributo dell'account utente di Azure AD nelle caselle di testo seguenti: **First name** (Nome), **Last name** (Cognome), **Email address** (Indirizzo di posta elettronica).
  2. Fare clic su **Aggiungi utente**.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di TalentLMS o le API fornite da TalentLMS per effettuare il provisioning degli account utente di AAD.
>  

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a TalentLMS eseguire la procedura seguente:**

1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **TalentLMS** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


