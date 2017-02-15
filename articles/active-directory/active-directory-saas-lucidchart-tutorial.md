---
title: 'Esercitazione: Integrazione di Azure Active Directory con Lucidchart | Documentazione Microsoft'
description: Informazioni su come utilizzare Lucidchart con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8386497b457f63ba0b62f50301ce3948ce060b97


---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Esercitazione: Integrazione di Azure Active Directory con Lucidchart
In questa esercitazione viene illustrata l'integrazione di Azure e Lucidchart.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Lucidchart abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Lucidchart saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Lucidchart (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Lucidchart
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenario")

## <a name="enabling-the-application-integration-for-lucidchart"></a>Abilitazione dell'integrazione dell'applicazione per Lucidchart
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Lucidchart.

### <a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Lucidchart, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **Lucidchart**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **Lucidchart** e quindi fare clic su **Complete** (Completa) per aggiungere l'applicazione.
   
   ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Lucidchart tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Lucidchart** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configure Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Lucidchart** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configure Single Sign-On")
3. Nella casella di testo **URL accesso Lucidchart** della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Lucidchart, ad esempio "*https://chart2.office.lucidchart.com/saml/sso/azure*", e quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in Lucidchart** fare clic su **Scarica metadati** e quindi salvare il file di dati localmente nel computer.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configure Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Lucidchart come amministratore.
6. Nel menu in alto fare clic su **Team**.
   
   ![Team](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")
7. Fare clic su **Application (Applicazione) \> Manage SAML (Gestisci SAML)**.
   
   ![Gestisci SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Manage SAML")
8. Nella pagina **Impostazioni di autenticazione SAML** eseguire la procedura seguente:
   
   1. Selezionare **Enable SAML Authentication** (Abilita autenticazione SAML) e quindi fare clic su **Optional** (Facoltativo).
      ![Impostazioni di autenticazione SAML](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML Authentication Settings")
   2. Nella casella di testo **Domain** (Dominio) digitare il dominio e quindi fare clic su **Change Certificate** (Cambia certificato).
      ![Cambia certificato](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Change Certificate")
   3. Aprire il file di metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **Upload Metadata**.
      ![Carica metadati](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Upload Metadata")
   4. Selezionare **Automatically Add new user to the team** (Aggiungi automaticamente nuovo utente al team) e quindi fare clic su **Save changes** (Salva modifiche).
      ![Salva modifiche](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Save Changes")
9. Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Non è richiesta alcuna operazione per configurare il provisioning degli utenti in Lucidchart.  
Quando un utente assegnato tenta di accedere a Lucidchart utilizzando il pannello di accesso, Lucidchart controlla se l'utente esiste.  
Se l’account utente non è ancora disponibile, viene creato automaticamente da Lucidchart.

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Per assegnare gli utenti a Lucidchart eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Lucidchart **fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


