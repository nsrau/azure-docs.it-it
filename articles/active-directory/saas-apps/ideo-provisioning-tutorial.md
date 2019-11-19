---
title: 'Esercitazione: configurare IDEO per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in IDEO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: d6bc3170162710e86359b374d1ef707bba0ce268
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152541"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Esercitazione: configurare IDEO per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in IDEO e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in IDEO.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant IDEO](https://www.shape.space/product/pricing)
* Un account utente in IDEO | Forma con autorizzazioni di amministratore.

## <a name="assign-users-to-ideo"></a>Assegnare gli utenti a IDEO

Azure Active Directory usa un concetto denominato assegnazioni per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a IDEO. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a IDEO seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>Suggerimenti importanti per l'assegnazione di utenti a IDEO

* È consigliabile assegnare un singolo Azure AD utente a IDEO per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a IDEO, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-ideo-for-provisioning"></a>Configurare IDEO per il provisioning

Prima di configurare IDEO per il provisioning utenti automatico con Azure AD, sarà necessario recuperare alcune informazioni di provisioning da IDEO.

1. Per il team di supporto del **token** di contatto IDEO al productsupport@ideo.com. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione IDEO nell'portale di Azure. 

## <a name="add-ideo-from-the-gallery"></a>Aggiungere IDEO dalla raccolta

Per configurare IDEO per il provisioning utenti automatico con Azure AD, è necessario aggiungere IDEO dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **IDEO**, selezionare **IDEO** nel pannello dei risultati. 

    ![IDEO nell'elenco dei risultati](common/search-new-app.png)

5. Selezionare il pulsante **Iscriviti a IDEO per** reindirizzare l'utente alla pagina di accesso di IDEO. 

    ![IDEO OIDC Add](media/ideo-provisioning-tutorial/signup.png)

6. Quando IDEO è un'app OpenIDConnect, scegli di accedere a IDEO usando il tuo account Microsoft Work.

    ![Account di accesso OIDC di IDEO](media/ideo-provisioning-tutorial/login.png)

7. Una volta completata l'autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati al proprio account IDEO.

    ![Consenso OIDc IDEO](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>Configurare il provisioning utenti automatico a IDEO 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in IDEO in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Per configurare il provisioning utenti automatico per IDEO in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **IDEO**.

    ![Collegamento di IDEO nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://profile.ideo.com/api/scim/v2` in **URL tenant**. Immettere il valore recuperato dal team di supporto di IDEO nel **token Secret**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a IDEO. Se la connessione non riesce, verificare che l'account IDEO disponga di autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a IDEO**.

    ![Mapping utente IDEO](media/ideo-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a IDEO nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in IDEO per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di IDEO](media/ideo-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per IDEO, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](media/ideo-provisioning-tutorial/groupmappings.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in IDEO scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](media/ideo-provisioning-tutorial/groupattributes.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in IDEO.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)


