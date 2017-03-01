---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kontiki | Documentazione Microsoft'
description: Informazioni su come usare Kontiki con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 230facec9f8f83e8a94eaaac5a1495195ff45cc7
ms.openlocfilehash: 3af125b186e3905572dd5d621c948a6639f9b023
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Esercitazione: Integrazione di Azure Active Directory con Kontiki
Questa esercitazione descrive l'integrazione di Azure e Kontiki.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Kontiki abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Kontiki potranno accedere all'applicazione tramite il sito aziendale di Kontiki (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Kontiki
* Configurazione dell'accesso Single Sign-On
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Scenario")

## <a name="enable-the-application-integration-for-kontiki"></a>Abilitare l'integrazione dell'applicazione per Kontiki
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Kontiki.

**Per abilitare l'integrazione dell'applicazione per Kontiki, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Kontiki**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Kontiki** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Kontiki tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

*Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Kontiki** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Kontiki** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di accesso Kontiki** della pagina **Configure App URL** (Configura URL app) digitare l'URL usato dagli utenti per accedere a Kontiki, ad esempio "*https://company.mc.eval.kontiki.com/*", e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Kontiki** fare clic su **Scarica metadati** per scaricare il file di metadati e salvarlo nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configurare l'accesso Single Sign-On")
5. Inviare il file di metadati al team di supporto di Kontiki.
   
   >[!NOTE]
   >La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Kontiki. Al termine della configurazione, viene ricevuta una notifica. 
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
  ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Non è necessaria alcuna attività di configurazione per il provisioning degli utenti in Kontiki. Quando un utente assegnato prova ad accedere a Kontiki usando il pannello di accesso, Kontiki verifica se l'utente esiste.  

>!NOTA] Se l'account utente non è presente, Kontiki lo crea automaticamente.
>

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Kontiki, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Kontiki** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


