---
title: 'Esercitazione: configurare la matrice di priorità per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente nella matrice di priorità.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a4598a99-3c98-4c14-86c2-95cc562e2439
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 15ec201851cf52f651e32959b30c1d8579eb1cea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152398"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Esercitazione: configurare la matrice di priorità per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire nella matrice di priorità e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in una matrice di priorità.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant della matrice di priorità](https://appfluence.com/pricing/)
* Un account utente in una matrice di priorità con autorizzazioni di amministratore.

## <a name="assign-users-to-priority-matrix"></a>Assegnare utenti a una matrice di priorità

Azure Active Directory usa un concetto denominato assegnazioni per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere alla matrice di priorità. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a una matrice di priorità seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Suggerimenti importanti per l'assegnazione di utenti a una matrice di priorità

* È consigliabile assegnare un singolo Azure AD utente alla matrice di priorità per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a una matrice di priorità, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-priority-matrix-for-provisioning"></a>Configurare la matrice di priorità per il provisioning

Prima di configurare la matrice di priorità per il provisioning utenti automatico con Azure AD, sarà necessario recuperare alcune informazioni di provisioning dalla matrice di priorità.

1. Accedere alla console di [amministrazione della matrice di priorità](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Fare clic su **token di accesso OAuth** per la matrice di priorità

    ![Matrice di priorità Aggiungi SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Fare clic sul pulsante **Ottieni nuovo token** . Copiare la **stringa del token**. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione matrice di priorità nel portale di Azure. 

    ![Token di creazione della matrice di priorità](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Aggiungere la matrice di priorità dalla raccolta

Per configurare la matrice di priorità per il provisioning utenti automatico con Azure AD, è necessario aggiungere la matrice di priorità dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Priority Matrix**e selezionare **Priority Matrix** nel pannello dei risultati. 

    ![Matrice di priorità nell'elenco dei risultati](common/search-new-app.png)

5. Selezionare il pulsante **iscriversi per la matrice di priorità** che consente di reindirizzare la pagina di accesso della matrice di priorità. 

    ![Matrice di priorità OIDC aggiunta](media/priority-matrix-provisioning-tutorial/signup.png)

6. Poiché la matrice di priorità è un'app OpenIDConnect, scegliere di accedere alla matrice di priorità usando il proprio account Microsoft.

    ![Accesso OIDC matrice di priorità](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Una volta completata l'autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati all'account della matrice di priorità.

    ![Consenso OIDc matrice di priorità](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Configurare il provisioning utenti automatico in una matrice di priorità 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi nella matrice di priorità in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM della matrice di priorità, vedere [provisioning utenti e matrice di priorità](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Per configurare il provisioning utenti automatico per la matrice di priorità in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **matrice di priorità**.

    ![Collegamento matrice di priorità nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://sync.appfluence.com/scim/v2/` in **URL tenant**. Immettere il valore recuperato e salvato in precedenza dalla matrice di priorità nel **token segreto**. Fare clic su **Test connessione** per verificare che Azure ad possibile connettersi alla matrice di priorità. Se la connessione non riesce, verificare che l'account della matrice di priorità disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to Priority Matrix**.

    ![Mapping utente matrice di priorità](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a matrice di priorità nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente nella matrice di priorità per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente matrice di priorità](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per la matrice di priorità, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning nella matrice di priorità scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad sulla matrice di priorità.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)


