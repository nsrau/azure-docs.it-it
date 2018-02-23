---
title: 'Esercitazione: Integrazione di Azure Active Directory con SCC LifeCycle | Microsoft Docs'
description: Informazioni su come usare SCC LifeCycle con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 943698311d10b479879fd92d2482d827650e2b0c
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Esercitazione: Integrazione di Azure Active Directory con SCC LifeCycle
In questa esercitazione viene illustrata l'integrazione di Azure e SCC LifeCycle.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di SCC LifeCycle abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SCC LifeCycle saranno in grado di eseguire l'accesso Single Sign-On all'applicazione tramite il sito aziendale di SCC LifeCycle (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per SCC LifeCycle
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scenario")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>Abilitare l'integrazione dell'applicazione per SCC LifeCycle
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per SCC LifeCycle.

**Per abilitare l'integrazione dell'applicazione per SCC LifeCycle, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **SCC LifeCycle**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **SCC LifeCycle**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a SCC LifeCycle tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SCC LifeCycle** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SCC LifeCycle** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** digitare nella casella di testo **URL di accesso** l'URL utilizzato dagli utenti per accedere all'applicazione SCC LifeCycle usando il modello "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" e fare clic su **Avanti**.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in SCC LifeCycle** fare clic su **Scarica metadati**, quindi salvare il file di metadati in locale nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurare l'accesso Single Sign-On")
5. Inoltrare il file di metadati al team di supporto di SCC LifeCycle.
   
   >[!NOTE]
   >L'accesso Single Sign-On deve essere abilitato dal team di supporto di SCC LifeCycle.
   > 
   > 

6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a SCC LifeCycle, è necessario eseguirne il provisioning in SCC LifeCycle. Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in SCC LifeCycle.

Quando un utente assegnato tenta di accedere a SCC LifeCycle, se necessario viene creato automaticamente un account di SCC LifeCycle.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da SCC LifeCycle per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario assegnare l'accesso all'applicazione agli utenti di Azure AD a cui si desidera consentire l'uso dell'applicazione.

**Per assegnare gli utenti a SCC LifeCycle, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **SCC LifeCycle** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Sì")

Per testare le impostazioni di SSO, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

