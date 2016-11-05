---
title: 'Esercitazione: Integrazione di Azure Active Directory con LogicMonitor | Microsoft Docs'
description: Informazioni su come utilizzare LogicMonitor con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/08/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con LogicMonitor
In questa esercitazione viene illustrata l'integrazione di Azure e LogicMonitor. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant LogicMonitor

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per LogicMonitor
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenario")

## Abilitazione dell'integrazione dell'applicazione per LogicMonitor
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per LogicMonitor.

### Per abilitare l'integrazione dell'applicazione per LogicMonitor eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiunta di un'applicazione](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Aggiunta di un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **logicmonitor**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **LogicMonitor**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
   
   ## Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a LogicMonitor tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.

### Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **LogicMonitor** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configura accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a LogicMonitor** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configura accesso Single Sign-On")
3. Nella pagina **Configura URL app**, nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere a LogicMonitor, ad esempio "*http://company.logicmonitor.com*"\, quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configura URL app")
4. Nella pagina **Configura accesso Single Sign-On in LogicMonitor** fare clic su **Scarica metadati**, quindi salvare il file nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configura accesso Single Sign-On")
5. Accedere al sito aziendale di **LogicMonitor** come amministratore.
6. Nel menu in alto fare clic su **Impostazioni**.
   
   ![Impostazioni](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Impostazioni")
7. Nella barra di spostamento sul lato sinistro fare clic su **Single Sign-On**
   
   ![Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")
8. Nella sezione **Impostazioni Single Sign-on (SSO)** eseguire questa procedura:
   
   ![Impostazioni Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Impostazioni Single Sign-On")
   
   1. Selezionare **Attiva Single Sign-On**.
   2. Come **Assegnazione dei ruoli predefiniti** selezionare **readonly**.
   3. Aprire il file di metadati scaricato in Blocco note, quindi incollare il contenuto del file nella casella di testo **Metadati del provider di identità**.
   4. Fare clic su **Salva modifiche**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configura accesso Single Sign-On")
   
   ## Configurazione del provisioning utente

Per consentire agli utenti di AAD di accedere, è necessario eseguirne il provisioning nell’applicazione LogicMonitor utilizzando i relativi nomi utente di Azure Active Directory.

### Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di LogicMonitor come amministratore.
2. Nel menu in alto fare clic su **Impostazioni**, quindi fare clic su **Ruoli e utenti**.
   
   ![Ruoli e utenti](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Ruoli e utenti")
3. Fare clic su **Aggiungi**.
4. Nella sezione **Aggiungi un account** eseguire la procedura seguente:
   
   ![Aggiungi un account](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Aggiungi un account")
   
   1. Nelle caselle di testo corrispondenti digitare i valori **Nome utente**, **Indirizzo di posta elettronica**, **Password** e **Conferma password** dell’utente di Azure Active Directory di cui si intende eseguire il provisioning.
   2. Selezionare **Ruoli**, **Visualizza autorizzazioni** e **Stato**.
   3. Fare clic su **Submit**.

> [!NOTE]
> È possibile utilizzare qualsiasi altro strumento di creazione di account utente di LogicMonitor o le API fornite da LogicMonitor per eseguire il provisioning degli account utente di Azure Active Directory.
> 
> 

## Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### Per assegnare gli utenti a LogicMonitor eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell’applicazione **LogicMonitor** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Assegna utenti")
3. Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->