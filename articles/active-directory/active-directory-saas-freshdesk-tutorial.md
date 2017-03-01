---
title: 'Esercitazione: Integrazione di Azure Active Directory con Freshdesk | Documentazione Microsoft'
description: Informazioni su come usare Freshdesk con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31177994-7910-4a72-bd76-5fa6260242fb
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4778d46702a08ea6bae7e71e06e5b30d4fb697c3
ms.openlocfilehash: 927fa651dac67031bb26234023f23294497aea06
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Esercitazione: Integrazione di Azure Active Directory con Freshdesk
Questa esercitazione descrive l'integrazione di Azure e Freshdesk.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Freshdesk

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Freshdesk potranno accedere all'applicazione tramite il sito aziendale di Freshdesk (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Freshdesk
* Configurazione dell'accesso Single Sign-On (SSO)
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scenario")

## <a name="enable-the-application-integration-for-freshdesk"></a>Abilitare l'integrazione dell'applicazione per Freshdesk
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Freshdesk.

**Per abilitare l'integrazione dell'applicazione per Freshdesk, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Freshdesk**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Freshdesk** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Freshdesk tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  

La configurazione dell'accesso SSO per Freshdesk richiede di recuperare un valore di identificazione personale da un certificato. Se non si ha familiarità con questa procedura, vedere [Procedura: recuperare l'identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Freshdesk** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Freshdesk** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL accesso Freshdesk** della pagina **Configura URL app** digitare l'URL usando il modello seguente "*https://\<tenant-name\>.Freshdesk.com*" e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Freshdesk** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente come **c:\\Freshdesk.cer**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Freshdesk come amministratore.
6. Nel menu in alto fare clic su **Admin**.
   
   ![Amministratore](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Amministratore")
7. Nella scheda **General Settings** fare clic su **Security**.
   
   ![Sicurezza](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Sicurezza")
8. Nella sezione **Security** seguire questa procedura:
   
   ![Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign-On")
   
   1. Per **Single Sign On (SSO)** selezionare **On**.
   2. Selezionare **SAML SSO**.
   3. Nella finestra di dialogo **Configura accesso Single Sign-On in Freshdesk** del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **SAML Login URL** (URL accesso SAML).
   4. Nella finestra di dialogo **Configura accesso Single Sign-On in Freshdesk** del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Logout URL** (URL di disconnessione).
   5. Copiare il valore di **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo **Security Certificate Fingerprint** (Impronta digitale del certificato sicurezza).  
 
      >[!TIP]
      >Per informazioni dettagliate, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI). 
      > 
   6. Fare clic su **Save**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Freshdesk, è necessario eseguirne il provisioning in Freshdesk. Nel caso di Freshdesk, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Freshdesk** .
2. Nel menu in alto fare clic su **Admin**.
   
   ![Amministratore](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Amministratore")
3. Nella scheda **General Settings** fare clic su **Agents**.
   
   ![Agents](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")
4. Fare clic su **New Agent**.
   
   ![New Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")
5. Nella finestra di dialogo Agent Information seguire questa procedura:
   
   ![Agent Information](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")
   
   1. Nella casella di testo **Full Name** , digitare il nome dell'account Azure AD di cui si vuole eseguire il provisioning.
   2. Nella casella di testo **Email** , digitare l’indirizzo di posta elettronica dell'account Azure AD di cui si vuole eseguire il provisioning.
   3. Nella casella di testo **Title** , digitare il titolo dell'account Azure AD di cui si vuole eseguire il provisioning.
   4. Selezionare **Agents role** e quindi fare clic su **Assign**.
   5. Fare clic su **Save**.     
   
      >[!NOTE]
      >Il titolare dell'account AD riceverà un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account. 
      > 

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Freshdesk per eseguire il provisioning degli account utente di AAD. 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Freshdesk, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Freshdesk** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


