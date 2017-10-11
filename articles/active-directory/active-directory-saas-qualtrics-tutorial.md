---
title: 'Esercitazione: Integrazione di Azure Active Directory con Qualtrics | Documentazione Microsoft'
description: Informazioni su come usare Qualtrics con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 2fcde595a40dafda7549f5bccb582b57585b314e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Esercitazione: Integrazione di Azure Active Directory con Qualtrics
In questa esercitazione viene illustrata l'integrazione di Azure e Qualtrics.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Qualtrics abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Qualtrics saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Qualtrics (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Qualtrics
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Scenario")

## <a name="enabling-the-application-integration-for-qualtrics"></a>Abilitazione dell'integrazione dell'applicazione per Qualtrics
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Qualtrics.

**Per abilitare l'integrazione dell'applicazione per Qualtrics, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Qualtrics**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Qualtrics**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Qualtrics](./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Qualtrics tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Qualtrics** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Qualtrics** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **Qualtrics Sign On URL** (URL di accesso a Qualtrics) ad esempio "*https://ssotest2ut1.qualtrics.com*", quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Qualtrics** fare clic su **Scarica metadati**, quindi salvare il file di metadati nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Configurare l'accesso Single Sign-On")
5. Inviare il file di metadati al team di supporto di Qualtrics.
   
   >[!NOTE]
   >La configurazione dell'accesso SSO deve essere eseguita dal team di supporto di Qualtrics. Al termine della configurazione, viene ricevuta una notifica.
   > 
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in Qualtrics. Quando un utente assegnato tenta di accedere a Qualtrics usando il pannello di accesso, Qualtrics verifica se l'utente esiste.  

Se l’account utente non è disponibile, viene creato automaticamente da Qualtrics.

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Qualtrics, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Qualtrics** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Sì")

Per testare le impostazioni di SSO, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

