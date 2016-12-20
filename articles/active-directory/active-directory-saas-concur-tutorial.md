---
title: 'Esercitazione: Integrazione di Azure Active Directory con Concur | Documentazione Microsoft'
description: Informazioni su come usare Concur con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1eee0a5d-24fa-4986-9aef-3c543cfe3296
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3f2cb86a78d00f4c64fb9b5b961d00946677e3e5


---
# <a name="tutorial-azure-active-directory-integration-with-concur"></a>Esercitazione: Integrazione di Azure Active Directory con Concur
Questa esercitazione descrive l'integrazione di Azure e Concur.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant in Concur

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione Concur
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-concur-tutorial/IC769766.png "Scenario")

> [!NOTE]
> La configurazione della sottoscrizione di Concur per SSO federato tramite SAML è un'attività separata per la cui esecuzione è necessario contattare Concur.
> 
> 

## <a name="enabling-the-application-integration-for-concur"></a>Abilitazione dell'integrazione dell'applicazione Concur
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Concur.

### <a name="to-enable-the-application-integration-for-concur-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Concur, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si vuole abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-concur-tutorial/IC700994.png "Applications")
4. Per aprire la **Raccolta di applicazioni**, fare clic su **Aggiungi app**, quindi fare clic su **Aggiungi un'applicazione che verrà utilizzata dall'organizzazione**.
   
   ![Come procedere](./media/active-directory-saas-concur-tutorial/IC700995.png "What do you want to do?")
5. Nella **casella di ricerca** digitare **Concur**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-concur-tutorial/IC721727.png "Application Gallery")
6. Nel riquadro dei risultati selezionare **Concur** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Concur](./media/active-directory-saas-concur-tutorial/IC721728.png "Concur")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Concur tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

> [!NOTE]
> La configurazione della sottoscrizione di Concur per SSO federato tramite SAML è un'attività separata per la cui esecuzione è necessario contattare Concur.
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Concur** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-concur-tutorial/IC769767.png "Configure single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Concur** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-concur-tutorial/IC769768.png "Configure single sign-on")
3. Nella casella di testo **URL di accesso Concur** della pagina **Configure App URL** (Configura URL app) digitare l'URL di accesso al tenant di Concur e quindi fare clic su **Avanti**: 
   
   ![Configura URL di accesso](./media/active-directory-saas-concur-tutorial/IC769769.png "Configure sign in URL")
4. Nella pagina **Configura accesso Single Sign-On in Concur** seguire questa procedura.
   
   ![Configura URL di accesso](./media/active-directory-saas-concur-tutorial/IC769770.png "Configure sign in URL")
   
   1. Fare clic su Scarica metadati e salvare il file di dati nel computer.
   2. Contattare il team di supporto di Concur per configurare SSO per il tenant.
   3. Selezionare la conferma della configurazione Single Sign-On, quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.  
   
   > [!NOTE]
   > La configurazione della sottoscrizione di Concur per SSO federato tramite SAML è un'attività separata per la cui esecuzione è necessario contattare Concur.
   > 
   > 

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Questa sezione descrive come abilitare il provisioning utente degli account utente di Active Directory in Concur.

Per abilitare le app nel servizio Expense, è necessario configurare e usare in modo corretto un profilo di amministratore di servizi Web. Non è infatti sufficiente aggiungere il ruolo di amministratore dei servizi Web al profilo amministratore esistente usato per le funzioni amministrative di T&E.

I consulenti Concur o l'amministratore client devono creare un diverso profilo di amministratore dei servizi Web e l'amministratore client deve usare tale profilo per le funzioni di amministratore dei servizi Web, ad esempio per abilitare le app. Questi profili devono essere mantenuti separati dal profilo di amministratore T&E giornaliero dell'amministratore del client. Questo significa che al profilo di amministratore T&E non deve essere assegnato il ruolo di amministratore dei servizi Web.

Quando si crea il profilo da usare per abilitare l'app, immettere il nome dell'amministratore client nei campi del profilo utente per assegnare la proprietà al profilo. Dopo la creazione del profilo il client dovrà accedere con questo profilo per poter fare clic sul pulsante "*Abilita*" relativo a un'app dei partner nel menu Servizi web.

Per i seguenti motivi è consigliabile non eseguire questa operazione con il profilo usato per la normale amministrazione T&E.

1. Il client deve essere quello che fa clic su "*Sì*" nella finestra di dialogo visualizzata dopo l'abilitazione di un'app. Tale operazione conferma che il client consente all'applicazione del partner di accedere ai dati, di conseguenza non può essere l'utente o il partner a fare clic sul pulsante Yes.
2. Se un amministratore client che ha abilitato un'app con il profilo di amministratore T&E lascia la società e il relativo profilo viene disattivato, tutte le app abilitate tramite tale profilo non funzioneranno finché l'app non viene abilitata con un altro profilo di amministratore di servizi Web attivo. È per questo motivo che è richiesta la creazione di profili distinti per l'amministratore di servizi Web.
3. Se un amministratore lascia la società, se necessario è possibile modificare il nome associato al profilo dell'amministratore di servizi Web impostandolo su quello di sostituzione senza alcun impatto sull'app abilitata perché non è necessario disattivare tale profilo.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, seguire questa procedura:
1. Accedere al tenant di **Concur** .
2. Nel menu **Administration** (Amministrazione) selezionare **Web Services** (Servizi Web).
   
   ![Tenant di Concur](./media/active-directory-saas-concur-tutorial/IC721729.png "Concur tenant")
3. Sul lato sinistro nel riquadro **Web Services** (Servizi Web) selezionare **Enable Partner Application** (Abilita applicazione partner).
   
   ![Abilita applicazione partner](./media/active-directory-saas-concur-tutorial/IC721730.png "Enable Partner Application")
4. Dall'elenco **Enable Application** (Abilita applicazione) selezionare **Azure Active Directory**, quindi fare clic su **Enable** (Abilita).
   
   ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")
5. Fare clic su **Yes** (Sì) per chiudere la finestra di dialogo **Confirm Action** (Conferma operazione).
   
   ![Conferma dell'operazione](./media/active-directory-saas-concur-tutorial/IC721732.png "Confirm Action")
6. Nel portale di Azure classico selezionare **Concur** dall'elenco delle applicazioni per visualizzare la finestra di dialogo **Concur**.
7. Per aprire la pagina della finestra di dialogo **Configura provisioning utenti** fare clic su **Configura provisioning utenti**.
8. Immettere il nome utente e la password dell'amministratore di Concur, quindi fare clic su **Avanti**.
9. Per completare la configurazione, nella pagina **Conferma** fare clic sul pulsante **Completa**.

È ora possibile creare un account di prova. Attendere 10 minuti e quindi verificare che l'account sia stato sincronizzato con Concur.

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-concur-perform-the-following-steps"></a>Per assegnare gli utenti a Concur seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Concur** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-concur-tutorial/IC769771.png "Assign users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-concur-tutorial/IC767830.png "Yes")

È ora necessario attendere 10 minuti e verificare che l'account sia stato sincronizzato con Concur.

Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


