---
title: 'Esercitazione: Integrazione di Azure Active Directory con Clarizen | Microsoft Docs'
description: Informazioni su come usare Clarizen con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con Clarizen
Questa esercitazione descrive l'integrazione di Azure e Clarizen. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Clarizen abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Clarizen potranno accedere all'applicazione tramite il sito aziendale di Clarizen (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Clarizen
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario")

## Abilitazione dell'integrazione dell'applicazione per Clarizen
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Clarizen.

### Per abilitare l'integrazione dell'applicazione per Clarizen, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiunta di un'applicazione](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Aggiunta di un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Clarizen**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Clarizen** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
   
   ## Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Clarizen tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Clarizen** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configura accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Clarizen** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configura accesso Single Sign-On")
3. Nella pagina **Configura accesso Single Sign-On in Clarizen** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configura accesso Single Sign-On")
4. In un'altra finestra del Web browser accedere al sito aziendale di **Clarizen** come amministratore (ad esempio: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).
5. Fare clic sul nome utente e quindi su **Impostazioni**.
   
   ![Impostazioni](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Impostazioni")
6. Fare clic su **Impostazioni globali** e quindi su **modifica** accanto a **Autenticazione federata**.
   
   ![Global Settings](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")
7. Nella finestra di dialogo **Autenticazione federata** seguire questa procedura:
   
   ![Federated Authentication](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")
   
   1. Per caricare il certificato scaricato, fare clic su **Carica**.
   2. Nella finestra di dialogo **Configure single sign-on at Clarizen** (Configura accesso Single Sign-On in Clarizen) del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e quindi incollarlo nella casella di testo **Sign-in URL** (URL di accesso).
   3. Nella finestra di dialogo **Configure single sign-on at Clarizen** (Configura accesso Single Sign-On in Clarizen) del portale di Azure classico copiare il valore di **URL servizio Single Sign-Out** e quindi incollarlo nella casella di testo **Sign-out URL** (URL di disconnessione).
   4. Selezionare **Utilizza POST**.
   5. Fare clic su **Save**.
8. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configura accesso Single Sign-On")
   
   ## Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Clarizen, è necessario eseguirne il provisioning in Clarizen. Nel caso di Clarizen, il provisioning è un'attività manuale.

### Per eseguire il provisioning di un account utente, seguire questa procedura:
1. Accedere al sito aziendale di **Clarizen** come amministratore.
2. Fare clic su **Persone**.
   
   ![People](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")
3. Fare clic su **Invita Utente**.
   
   ![Invite Users](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")
4. Nella finestra di dialogo Invite People seguire questa procedura:
   
   ![Invite People](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invite People")
   
   1. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Invita**.
   
   > [!NOTE]
   > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
   > 
   > 

## Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### Per assegnare gli utenti a Clarizen, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Clarizen** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assegna utenti")
3. Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->