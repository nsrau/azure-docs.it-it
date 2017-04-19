---
title: 'Esercitazione: integrazione di Azure Active Directory con Pagerduty | Documentazione Microsoft'
description: Informazioni su come utilizzare Pagerduty con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a7fae61f9b2dc400cbbd7054c11ec622f8683ccb
ms.lasthandoff: 04/03/2017


---

# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Esercitazione: integrazione di Azure Active Directory con Pagerduty
In questa esercitazione viene illustrata l'integrazione di Azure e Pagerduty. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Pagerduty

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Pagerduty potranno eseguire l'accesso Single Sign-On (SSO) all'applicazione tramite il sito aziendale di Pagerduty (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Pagerduty
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenario")

## <a name="enabling-the-application-integration-for-pagerduty"></a>Abilitazione dell'integrazione dell'applicazione per Pagerduty
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Pagerduty.

**Per abilitare l'integrazione dell'applicazione per Pagerduty, seguire questa procedura:**

1. Nel portale di gestione di Azure fare clic su **Active Directory**nel pannello di navigazione sinistro.
   
   ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Pagerduty**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Pagerduty**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Pagerduty tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  

Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base 64.  

Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Pagerduty** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Pagerduty** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL accesso Pagerduty** nel formato "*https://\<tenant-name\>.Pagerduty.com*" e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Pagerduty** fare clic su **Scarica certificato**, quindi salvare il file di certificato nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Pagerduty come amministratore.
6. Nel menu in alto fare clic su **Impostazioni account**.
   
   ![Impostazioni account](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Impostazioni account")
7. Fare clic su **Single Sign-On**.
   
   ![Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Single Sign-On")
8. Nella pagina Attiva Single Sign-on (SSO) eseguire la procedura seguente:
   
   ![Abilitare l'accesso Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Abilitare l'accesso Single Sign-On")
   
   1. Creare un file con **codifica Base 64** dal certificato scaricato.  
      
      >[!TIP]
      >Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).
      >

  2. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato X.509**
  3. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Pagerduty** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **Login URL** (URL di accesso).
  4. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in Pagerduty** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Logout URL** (URL di disconnessione).
  5. Selezionare **Attiva Single Sign-on**.
  6. Fare clic su **Salva modifiche**.

9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configurare l'accesso Single Sign-On")
   

## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Pagerduty, è necessario eseguirne il provisioning in Pagerduty.  

* Nel caso di Pagerduty, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Pagerduty** .
2. Scegliere **Utenti**dal menu in alto.
3. Fare clic su **Aggiungi utenti**.
   
   ![Aggiungere utenti](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Aggiungere utenti")
4. Nella finestra di dialogo **Invite your team** (Invita il team) digitare **nome e cognome** e **indirizzo di posta elettronica** dell'utente di Azure AD di cui eseguire il provisioning, fare clic su **Add** (Aggiungi) e su **Send Invites** (Invia inviti).
   
   ![Invitare il team](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invitare il team")
   
   >[!NOTE]
   >Tutti gli utenti aggiunti riceveranno un invito per creare un account PagerDuty.
   > 
   > 

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Pagerduty o le API fornite da Pagerduty per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Pagerduty, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Pagerduty** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Sì")

Per testare le impostazioni di SSO, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

