---
title: 'Esercitazione: Configurare Storegate per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Storegate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357941"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Esercitazione: Configurare Storegate per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Storegate e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Storegate.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Storegate](https://www.storegate.com)
* Un account utente in Storegate con autorizzazioni di amministratore.

## <a name="assign-users-to-storegate"></a>Assegnare gli utenti a Storegate

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle assegnazioni. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Storegate. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Storegate seguendo le istruzioni fornite qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Suggerimenti importanti per l'assegnazione di utenti a Storegate

* È consigliabile assegnare un singolo utente di Azure AD a Storegate per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Storegate, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-storegate-for-provisioning"></a>Configurare Storegate per il provisioning

Prima di configurare Storegate per il provisioning utenti automatico con Azure AD, sarà necessario recuperare alcune informazioni di provisioning da Storegate.

1. Accedere alla [console di amministrazione di Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e passare alle impostazioni facendo clic sull'icona dell'utente nell'angolo in alto a destra e selezionando **Account Settings** (Impostazioni account).

    ![Aggiunta di SCIM a Storegate](media/storegate-provisioning-tutorial/admin.png)

2. Nelle impostazioni passare a **Team > Settings** (Team > Impostazioni) e verificare che l'interruttore nella sezione **Single Sign-on** sia attivato.

    ![Impostazioni di Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Interruttore Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Copiare i valori di **Tenant URL** (URL tenant) e **Token**. Questi valori verranno immessi nei campi **URL tenant** e **Token segreto** rispettivamente, nella scheda Provisioning dell'applicazione Storegate nel portale di Azure. 

    ![Creazione token in Storegate](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Aggiungere Storegate dalla raccolta

Per configurare Storegate per il provisioning utenti automatico con Azure AD, è necessario aggiungere Storegate dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Storegate** e quindi selezionare **Storegate** nel riquadro dei risultati. 

    ![Storegate nell'elenco dei risultati](common/search-new-app.png)

5. Fare clic sul pulsante **Sign-up for Storegate** (Iscrizione per Storegate) per essere reindirizzati alla pagina di accesso di Storegate. 

    ![Aggiunta di OIDC in Storegate](media/storegate-provisioning-tutorial/signup.png)

6.  Accedere alla [console di amministrazione di Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e passare alle impostazioni facendo clic sull'icona dell'utente nell'angolo in alto a destra e selezionando **Account Settings** (Impostazioni account).

    ![Accesso a Storegate](media/storegate-provisioning-tutorial/admin.png)

7. Nelle impostazioni passare a **Team > Settings** (Team > Impostazioni) e fare clic sull'interruttore nella sezione Single Sign-on per avviare il flusso di consenso. Fare clic su **Activate** (Attiva).

    ![Team Storegate](media/storegate-provisioning-tutorial/team.png)

    ![SSO Storegate](media/storegate-provisioning-tutorial/sso.png)

    ![Attivazione di Storegate](media/storegate-provisioning-tutorial/activate.png)

8. Poiché Storegate è un'app OpenIDConnect, scegliere di accedere a Storegate usando l'account aziendale Microsoft.

    ![Accesso OIDC a Storegate](media/storegate-provisioning-tutorial/login.png)

9. Una volta eseguita l'autenticazione, l'utente accetta la richiesta di consenso nella pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati all'account Storegate.

    ![Consenso OIDC per Storegate](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Configurare il provisioning automatico degli utenti per Storegate 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Storegate in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di Storegate, vedere [qui](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Storegate in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Storegate**.

    ![Collegamento di Storegate nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere `https://dialpad.com/scim` in **URL tenant**. Immettere il valore recuperato e salvato in precedenza da Storegate in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Storegate. Se la connessione non riesce, verificare che l'account Storegate disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Storegate** (Sincronizza utenti di Azure Active Directory con Storegate).

    ![Mapping utente Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Storegate nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Storegate per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Storegate, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in Storegate selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Storegate.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
