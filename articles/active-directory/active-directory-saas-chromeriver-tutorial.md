---
title: 'Esercitazione: Integrazione di Azure Active Directory con Chromeriver | Documentazione Microsoft'
description: Informazioni su come usare Chromeriver con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 445c5600-e340-4724-a9cb-3cfaf5770b70
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 90dcbc7744677703bf37469953a8ea2713765b40
ms.openlocfilehash: 8345391d6bb84115284a990b302d764805ef209a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-chromeriver"></a>Esercitazione: Integrazione di Azure Active Directory con Chromeriver
Questa esercitazione descrive l'integrazione di Azure e Chromeriver.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Chromeriver abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Chromeriver potranno accedere all'applicazione tramite il sito aziendale di Chromeriver (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Chromeriver
* Configurazione dell'accesso Single Sign-On (SSO)
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Scenario")

## <a name="enable-the-application-integration-for-chromeriver"></a>Abilitare l'integrazione dell'applicazione per Chromeriver
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Chromeriver.

**Per abilitare l'integrazione dell'applicazione per Chromeriver, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
      ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Chromeriver**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Chromeriver** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Chromeriver tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Chromeriver** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On **.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Chromeriver** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:
   
   ![Configurare le impostazioni dell'app](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Configurare le impostazioni dell'app")
   
   1. Nella casella di testo **URL di risposta** digitare l'URL **AssertionConsumerService** di Chromeriver, ad esempio *https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*.  
   
     >[!NOTE]
     >È possibile ottenere questo valore dal team di supporto di Chromeriver.
     >  
   2. Fare clic su **Avanti**
4. Nella pagina **Configura accesso Single Sign-On in Chromeriver** fare clic su **Scarica metadati** per scaricare i metadati e salvare il file di metadati nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Configurare l'accesso Single Sign-On")
5. Inviare il file di metadati scaricato al team di supporto di Chromeriver.
   
 >[!NOTE]
 >Il team di supporto di Chromeriver si occuperà dell'effettiva configurazione dell'accesso Single Sign-On. Una volta completata l'abilitazione dell'accesso Single Sign-On per la sottoscrizione, si riceverà una notifica.
 >

6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Chromeriver, è necessario eseguirne il provisioning in Chromeriver.  

* Nel caso di Chromeriver, gli account utente devono essere creati dal team di supporto di Chromeriver.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Chromeriver per eseguire il provisioning degli account utente di Azure Active Directory. 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Chromeriver seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Chromeriver **fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


