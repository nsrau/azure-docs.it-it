---
title: 'Esercitazione: configurare looop per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in looop.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0efe2262-43c3-4e0c-97fa-9344385638e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: cb1329e778f053c664227c5dfc373c0bbedb1f0a
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134697"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Esercitazione: configurare looop per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in looop e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in looop.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di looop](https://www.looop.co/pricing/)
* Un account utente in un looop con autorizzazioni di amministratore.

## <a name="assign-users-to-looop"></a>Assegnare gli utenti a looop

Azure Active Directory usa un concetto denominato assegnazioni per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a looop. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a looop seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Suggerimenti importanti per l'assegnazione di utenti a looop

* È consigliabile assegnare un singolo Azure AD utente a looop per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a looop, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-looop-for-provisioning"></a>Configurare looop per il provisioning

Prima di configurare looop per il provisioning utenti automatico con Azure AD, sarà necessario recuperare alcune informazioni di provisioning da looop.

1. Accedere alla console di [amministrazione di looop](https://app.looop.co/#/login) e selezionare **account**. In **Impostazioni account** selezionare **autenticazione**.

    ![Looop aggiungere SCIM](media/looop-provisioning-tutorial/admin.png)

2. Per generare un nuovo token, fare clic su **Reimposta token** in **integrazione scim**.

    ![Looop aggiungere SCIM](media/looop-provisioning-tutorial/resettoken.png)

3. Copiare l' **endpoint scim** e il **token**. Questi valori verranno immessi nei campi **URL tenant** e **token segreto** nella scheda provisioning dell'applicazione looop nel portale di Azure. 

    ![Looop creare token](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Aggiungere looop dalla raccolta

Per configurare looop per il provisioning utenti automatico con Azure AD, è necessario aggiungere looop dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **looop**, selezionare **looop** nel pannello dei risultati. 

    ![Looop nell'elenco risultati](common/search-new-app.png)

5. Selezionare il pulsante **Iscriviti a looop** che consente di reindirizzare la pagina di accesso di looop. 

    ![Aggiunta looop OIDC](media/looop-provisioning-tutorial/signup.png)

6. Poiché looop è un'app OpenIDConnect, scegliere di accedere a looop con l'account Microsoft Work.

    ![Accesso OIDC di looop](media/looop-provisioning-tutorial/msftlogin.png)

7. Una volta completata l'autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati all'account looop.

    ![Consenso looop OIDc](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Configurare il provisioning utenti automatico in looop 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in looop in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Per configurare il provisioning utenti automatico per looop in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **looop**.

    ![Collegamento di looop nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://<organisation_domain>.looop.co/scim/v2` in **URL tenant**. Ad esempio, `https://demo.looop.co/scim/v2`. Immettere il valore recuperato e salvato in precedenza da looop nel **token Secret**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a looop. Se la connessione non riesce, verificare che l'account looop disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a looop**.

    ![Mapping utente looop](media/looop-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a looop nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in looop per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di looop](media/looop-provisioning-tutorial/userattributes.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to meta Networks Connector**.

    ![Mapping del gruppo looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati dal Azure AD al connettore meta reti nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi nel connettore meta reti per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo looop](media/looop-provisioning-tutorial/groupattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per looop, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in looop selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su looop.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)


