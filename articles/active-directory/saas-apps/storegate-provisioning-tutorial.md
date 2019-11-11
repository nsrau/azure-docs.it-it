---
title: 'Esercitazione: configurare Storegate per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 1769b46210c766adc876dc36bf4e646fb23a6823
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905303"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Esercitazione: configurare Storegate per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Storegate e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Storegate.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di Storegate](https://www.storegate.com)
* Un account utente in un Storegate con autorizzazioni di amministratore.

## <a name="assign-users-to-storegate"></a>Assegnare gli utenti a Storegate

Azure Active Directory usa un concetto denominato assegnazioni per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Storegate. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Storegate seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Suggerimenti importanti per l'assegnazione di utenti a Storegate

* È consigliabile assegnare un singolo Azure AD utente a Storegate per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Storegate, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-storegate-for-provisioning"></a>Configurare Storegate per il provisioning

Prima di configurare Storegate per il provisioning utenti automatico con Azure AD, sarà necessario recuperare alcune informazioni di provisioning da Storegate.

1. Accedere alla console di [amministrazione di Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e passare alle impostazioni facendo clic sull'icona utente nell'angolo in alto a destra e selezionare **Impostazioni account**.

    ![Storegate aggiungere SCIM](media/storegate-provisioning-tutorial/admin.png)

2. In impostazioni passare a **Team > impostazioni** e verificare che l'interruttore di attivazione sia attivato nella sezione **Single Sign-on** .

    ![Impostazioni Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Interruttore Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Copiare l' **URL** e il **token**del tenant. Questi valori verranno immessi rispettivamente nei campi **URL tenant** e **token Secret** nella scheda provisioning dell'applicazione Storegate nel portale di Azure. 

    ![Storegate creare token](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Aggiungere Storegate dalla raccolta

Per configurare Storegate per il provisioning utenti automatico con Azure AD, è necessario aggiungere Storegate dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Storegate**, selezionare **Storegate** nel pannello dei risultati. 

    ![Storegate nell'elenco risultati](common/search-new-app.png)

5. Selezionare il pulsante **Iscriviti a Storegate** che consente di reindirizzare la pagina di accesso di Storegate. 

    ![Aggiunta Storegate OIDC](media/storegate-provisioning-tutorial/signup.png)

6.  Accedere alla console di [amministrazione di Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e passare alle impostazioni facendo clic sull'icona utente nell'angolo in alto a destra e selezionare **Impostazioni account**.

    ![Accesso Storegate](media/storegate-provisioning-tutorial/admin.png)

7. In impostazioni passare a **Team > impostazioni** e fare clic su Attiva/disattiva passa alla sezione Single Sign-on. verrà avviato il flusso di consenso. Fare clic su **attiva**.

    ![Team Storegate](media/storegate-provisioning-tutorial/team.png)

    ![SSO Storegate](media/storegate-provisioning-tutorial/sso.png)

    ![Attivazione di Storegate](media/storegate-provisioning-tutorial/activate.png)

8. Poiché Storegate è un'app OpenIDConnect, scegliere di accedere a Storegate con l'account Microsoft Work.

    ![Accesso OIDC di Storegate](media/storegate-provisioning-tutorial/login.png)

9. Una volta completata l'autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati all'account Storegate.

    ![Consenso Storegate OIDc](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Configurare il provisioning utenti automatico in Storegate 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Storegate in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di Storegate, vedere [questo](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)articolo.

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Storegate in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Storegate**.

    ![Collegamento di Storegate nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://dialpad.com/scim` in **URL tenant**. Immettere il valore recuperato e salvato in precedenza da Storegate nel **token Secret**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Storegate. Se la connessione non riesce, verificare che l'account Storegate disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Storegate**.

    ![Mapping utente Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Storegate nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Storegate per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per Storegate, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Storegate selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su Storegate.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
