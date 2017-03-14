---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cherwell | Documentazione Microsoft'
description: Informazioni su come usare Cherwell con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad891f99-179e-4487-834d-35f3bc01c1ec
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 43acab94bfde65793c4ff944cb6db5dc5de47feb
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Esercitazione: Integrazione di Azure Active Directory con Cherwell
Questa esercitazione descrive l'integrazione di Azure e Cherwell. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Cherwell abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Cherwell potranno accedere all'applicazione tramite il sito aziendale di Cherwell (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Cherwell
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scenario")

## <a name="enable-the-application-integration-for-cherwell"></a>Abilitare l'integrazione dell'applicazione per Cherwell
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Cherwell.

**Per abilitare l'integrazione dell'applicazione per Cherwell, seguire questa**
 procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Cherwell**.
   
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")
7. Nel riquadro dei risultati selezionare **Cherwell** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Cherwell tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso SSO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Cherwell** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Cherwell** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** seguire questa procedura:
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurare l'URL dell'app")
  1. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere a **Cherwell**, ad esempio *https://\<nome società\>.cherwellondemand.com/cherwellclient*. 
  2.  Fare clic su **Avanti**.
4. Nella pagina **Configura accesso Single Sign-On in Cherwell** seguire questa procedura:
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurare l'accesso Single Sign-On")
  1.  Fare clic su **Download certificato**e quindi salvare il certificato nel computer locale.
  2.  Copiare il valore di **URL provider di identità**.
  3.  Copiare il valore di **URL servizio Single Sign-On**.
  4.  Fare clic su **Avanti**.
5. Inviare il certificato scaricato, il valore di **URL provider di identità** e il valore di **URL servizio Single Sign-On** al team di supporto di Cherwell.
   
   >[!NOTE]
   >Il team di supporto di Cherwell si occuperà dell'effettiva configurazione dell'accesso Single Sign-On. Una volta completata l'abilitazione dell'accesso Single Sign-On per la sottoscrizione, si riceverà una notifica.
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
  ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurare l'accesso Single Sign-On")

## <a name="configure-user-provisioning"></a>Configura provisioning utenti
Per consentire agli utenti di Azure AD di accedere a Cherwell, è necessario eseguirne il provisioning in Cherwell.

Nel caso di Cherwell, gli account utente devono essere creati dal team di supporto di Cherwell.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Cherwell per eseguire il provisioning degli account utente di Azure Active Directory.
>  

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Cherwell, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Cherwell** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


