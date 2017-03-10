---
title: 'Esercitazione: Integrazione di Azure Active Directory con SimpleNexus| Documentazione Microsoft'
description: Informazioni su come usare SimpleNexus con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89821a05-88e2-4579-b144-0123b2b9cb95
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/08/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 451369e21e7471180b6cd8c77d62b157d0bcddff
ms.openlocfilehash: a4478283ef6ab949cc73322db908347a626c12d8
ms.lasthandoff: 12/14/2016


---
# <a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Esercitazione: Integrazione di Azure Active Directory con SimpleNexus
Questa esercitazione illustra l'integrazione di Azure e SimpleNexus.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di SimpleNexus abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SimpleNexus potranno accedere all'applicazione tramite il sito aziendale di SimpleNexus (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per SimpleNexus
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Scenario")

## <a name="enabling-the-application-integration-for-simplenexus"></a>Abilitazione dell'integrazione dell'applicazione per SimpleNexus
Questa sezione descrive come abilitare l'integrazione dell'applicazione per SimpleNexus.

### <a name="to-enable-the-application-integration-for-simplenexus-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per SimpleNexus, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **simple nexus**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **SimpleNexus**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Simple Nexus](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Simple Nexus")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a SimpleNexus tramite il loro account in Azure AD utilizzando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **SimpleNexus** nel portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SimpleNexus** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configurare l'accesso Single Sign-On")

3. Nella casella di testo **URL accesso SimpleNexus** della pagina **Configura URL app** digitare l'URL nel formato "*https://simplenexus.com/CompanyName\_login*" e quindi fare clic su **Avanti**.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configurare l'URL dell'app")

4. Nella pagina **Configura accesso Single Sign-On in SimpleNexus** fare clic su **Scarica metadati** e inoltrare il file di metadati al team di supporto di SimpleNexus.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configurare l'accesso Single Sign-On")
   
    > [!NOTE]
    > L'accesso Single Sign-On deve essere abilitato dal team di supporto di SimpleNexus.
    > 
    > 

5. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configurare l'accesso Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere ad SimpleNexus, è necessario eseguirne il provisioning in SimpleNexus.  
Nel caso di SimpleNexus, il provisioning è un'attività manuale eseguita dall'amministratore tenant.

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione di account utente di SimpleNexus o API fornita da SimpleNexus per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-simplenexus-perform-the-following-steps"></a>Per assegnare gli utenti a SimpleNexus, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **SimpleNexus** fare clic su **Assign users** (Assegna utenti).
   
    ![Assegnare utenti](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


