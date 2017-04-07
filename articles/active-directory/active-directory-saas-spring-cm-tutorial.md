---
title: 'Esercitazione: Integrazione di Azure Active Directory con SpringCM | Documentazione Microsoft'
description: Informazioni su come usare SpringCM con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 5600adfbc20fb499bdd206e966a9730f49a03e40
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Esercitazione: Integrazione di Azure Active Directory con SpringCM
Questa esercitazione illustra come configurare l'accesso Single Sign-On (SSO) tra Azure Active Directory e SpringCM.

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di SpringCM abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure Active Directory assegnati a SpringCM saranno in grado di eseguire l'accesso SSO tramite il pannello di accesso di AAD.

1. Abilitazione dell'integrazione dell'applicazione per SpringCM
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scenario")

## <a name="enabling-the-application-integration-for-springcm"></a>Abilitazione dell'integrazione dell'applicazione per SpringCM
Questa sezione descrive come abilitare l'integrazione dell'applicazione per SpringCM.

**Per abilitare l'integrazione dell'applicazione per SpringCM, eseguire la procedura seguente:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **SpringCM**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **SpringCM**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a SpringCM tramite il loro  account in Azure Active Directory utilizzando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SpringCM** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SpringCM** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configura URL app**, nella casella di testo **URL di accesso a SpringCM**, digitare l'URL usato dagli utenti per accedere all'applicazione SpringCM, quindi fare clic su **Avanti**. 
   
    L'URL dell'app è l'URL del tenant di SpringCM, ad esempio: *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurare l'URL dell'app")

4. Nella pagina **Configura accesso Single Sign-On in SpringCM** per scaricare il file del certificato, fare clic su **Download certificato** e quindi salvare il certificato localmente nel computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurare l'accesso Single Sign-On")

5. In un'altra finestra del browser Web accedere al sito aziendale di **SpringCM** come amministratore.

6. Nel menu nella parte superiore fare clic su **Vai a**, scegliere **Preferenze**, quindi nella scheda **Preferenze account** fare clic su **SAML SSO**.
   
    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7. Nella sezione Configurazione provider di identità, eseguire la procedura seguente:
   
    ![Configurazione del provider di identità](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Configurazione del provider di identità")   
  1. Per caricare il certificato di Azure Active Directory scaricato, fare clic su **Seleziona certificato autorità di certificazione** o su **Cambia certificato autorità di certificazione**.
  2. Nella finestra di dialogo **Configura accesso Single Sign-On in SpringCM** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e quindi incollarlo nella casella di testo **Issuer** (Autorità di certificazione).
  3. Nella pagina **Configura accesso Single Sign-On in SpringCM** del portale di Azure classico, copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **Service Provider (SP) Initiated Endpoint** (Endpoint avviato da provider di servizi).
  4. Per **SAML Enabled** selezionare **Abilita**.
  5. Fare clic su **Save**.

8. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configurare l'accesso Single Sign-On")

## <a name="configure-user-provisioning"></a>Configura provisioning utenti
Per consentire agli utenti di Azure Active Directory di accedere a SpringCM, è necessario eseguirne il provisioning in SpringCM.  

Nel caso di SpringCM, il provisioning è un'attività manuale.

>[!NOTE]
>Per altre informazioni, vedere [Creare e modificare un utente SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user). 
> 

**Per eseguire il provisioning di un account utente in SpringCM, eseguire la procedura seguente:**

1. Accedere al sito aziendale di **SpringCM** come amministratore.

2. Fare clic su **GOTO** e scegliere **Rubrica**.
   
    ![Crea utente](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Crea utente")

3. Fare clic su **Create User**.

4. Selezionare un **Ruolo utente**.

5. Selezionare **Invia messaggio di attivazione**.

6. Digitare il nome, il cognome, il titolo e l'indirizzo di posta elettronica di un account utente di Azure Active Directory valido di cui si desidera effettuare il provisioning nelle caselle di testo correlate.

7. Aggiungere l'utente a un **Gruppo di protezione**.

8. Fare clic su **Save**.

  >[!NOTE]
  >È possibile usare qualsiasi altro strumento di creazione di account utente SpringCM o API fornita da SpringCM per eseguire il provisioning degli account utente di Azure AD.  
  > 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a SpringCM, eseguire la procedura seguente:**

1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **SpringCM**, fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


