---
title: 'Esercitazione: Integrazione di Azure Active Directory con Greenhouse | Documentazione Microsoft'
description: Informazioni su come usare Greenhouse con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 78ec1766-4f79-4f16-9a66-d5584c4b6151
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ad2e712cc1bcb7aea82b2c56a8a4a0f79e2ce1e0
ms.openlocfilehash: cce21156d962c7678e2a32c0953586b786c1ad0f
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Esercitazione: Integrazione di Azure Active Directory con Greenhouse
Questa esercitazione descrive l'integrazione di Azure e Greenhouse.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione Greenhouse abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Greenhouse potranno accedere all'applicazione tramite il sito aziendale di Greenhouse (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Greenhouse
* Configurazione dell'accesso Single Sign-On (SSO)
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scenario")

## <a name="enable-the-application-integration-for-greenhouse"></a>Abilitare l'integrazione dell'applicazione per Greenhouse
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Greenhouse.

**Per abilitare l'integrazione dell'applicazione per Greenhouse, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **greenhouse**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Greenhouse** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Greenhouse tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Greenhouse** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Greenhouse** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di accesso** della pagina **Configure App URL** (Configura URL app) digitare l'URL usando il modello "*https://company.greenhouse.io*" e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Greenhouse** fare clic su **Scarica metadati** per scaricare il file di metadati e salvarlo nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configurare l'accesso Single Sign-On")
5. Inoltrare il file dei metadati al team di supporto di Greenhouse.

>[!NOTE]
>L’accesso Single Sign-On deve essere abilitato dal team di supporto di Greenhouse.
>

6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Greenhouse, è necessario eseguirne il provisioning in Greenhouse. Nel caso di Greenhouse, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Greenhouse** come amministratore.
2. Nel menu in alto fare clic su **Configure** (Configura) e quindi su **Users** (Utenti).
   
   ![Utenti](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Utenti")
3. Fare clic su **Nuovi utenti**.
   
   ![Nuovo utente](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "Nuovo utente")
4. Nella sezione **Aggiungi nuovo utente** eseguire la procedura seguente:
   
   ![Aggiungi nuovo utente](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Aggiungi nuovo utente")
   1. Nella casella di testo **Inserire email utente** digitare l'indirizzo di posta elettronica di un account di Azure Active Directory valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Save**.    
   
      >[!NOTE]
      >I titolari dell'account Azure Active Directory riceveranno un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
      >  

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Greenhouse per eseguire il provisioning degli account utente di AAD. 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Greenhouse, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Greenhouse** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


