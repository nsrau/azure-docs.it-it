---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cornerstone OnDemand | Microsoft Docs'
description: Informazioni su come usare Cornerstone OnDemand con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bbbae7d7a5e9b0fbfe07bd3f2aa61df77b56c5df
ms.openlocfilehash: 7fce07464b90c81da7a015246d5757ecc768792a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Esercitazione: Integrazione di Azure Active Directory con Cornerstone OnDemand
Questa esercitazione descrive l'integrazione di Azure e Cornerstone OnDemand.

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Cornerstone OnDemand

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Cornerstone OnDemand potranno accedere all'applicazione tramite il sito aziendale di Cornerstone OnDemand (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Cornerstone OnDemand
* Configurazione dell'accesso Single Sign-On (SSO)
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scenario")

## <a name="enable-the-application-integration-for-cornerstone-ondemand"></a>Abilitare l'integrazione dell'applicazione per Cornerstone OnDemand
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Cornerstone OnDemand.

**Per abilitare l'integrazione dell'applicazione per Cornerstone OnDemand, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **cornerstone ondemand**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Cornerstone OnDemand** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Cornerstone OnDemand tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso SSO, seguire questa procedura:**
1. Nella pagina di integrazione dell'applicazione **Cornerstone OnDemand** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Abilitare l'accesso Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Abilitare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Cornerstone OnDemand** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Accesso Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Accesso Single Sign-On di Microsoft Azure AD")
3. Nella pagina **Configura URL app** nella casella di testo **Cornerstone OnDemand Sign In URL** (URL di accesso a Cornerstone OnDemand) digitare l'URL usando il modello "*http://company.csod.com*" e fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Cornerstone OnDemand** fare clic su **Download certificato** e infine salvare il file di certificato localmente nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurare l'accesso Single Sign-On")
5. Inviare quanto segue al team di supporto di Cornerstone OnDemand:  
   1. Certificato scaricato
   2. Il valore **URL accesso remoto**
   3. Il valore **URL disconnessione remota**

   >[!NOTE]
   >L'accesso Single Sign-On deve essere configurato dal team di supporto di Cornerstone OnDemand. Il team di supporto invierà una notifica al termine della configurazione.
   > 
6. Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
  ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Cornerstone OnDemand, è necessario eseguirne il provisioning in Cornerstone OnDemand. Nel caso di Cornerstone OnDemand, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Inviare le informazioni, ad esempio nome e indirizzo di posta elettronica, relative all'utente di Azure AD di cui si vuole eseguire il provisioning al team di supporto di Cornerstone OnDemand.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Cornerstone OnDemand per eseguire il provisioning degli account utente di AAD. 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Cornerstone OnDemand, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Cornerstone OnDemand** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Assegnare utenti](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Assegnare utenti")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


