---
title: "Esercitazione: configurare l'autenticazione utente Netskope per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs"
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Netskope autenticazione utente.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: 5e71566ea7cd2e8953c84d58128e3380a782ba40
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516716"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>Esercitazione: configurare l'autenticazione utente Netskope per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire nell'autenticazione utente Netskope e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Netskope autenticazione utente.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di autenticazione utente Netskope](https://www.netskope.com/)
* Un account utente in Netskope autenticazione utente con autorizzazioni di amministratore.

## <a name="assigning-users-to-netskope-user-authentication"></a>Assegnazione di utenti a Netskope User Authentication

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere all'autenticazione utente di Netskope. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi all'autenticazione utente Netskope seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>Suggerimenti importanti per l'assegnazione di utenti all'autenticazione utente di Netskope

* È consigliabile assegnare un singolo Azure AD utente all'autenticazione utente di Netskope per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Netskope User Authentication, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>Configurare l'autenticazione utente di Netskope per il provisioning

1. Accedere alla console di [amministrazione dell'autenticazione utente di Netskope](https://netskope.goskope.com/). Passare a **Home > impostazioni**.

    ![Console di amministrazione dell'autenticazione utente Netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Passare a **strumenti**. Nel menu **strumenti** passare a **strumenti di directory > integrazione scim**.

    ![Strumenti di autenticazione utente Netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Autenticazione utente Netskope aggiungere SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Scorrere verso il basso e fare clic sul pulsante **Aggiungi token** . Nella finestra di dialogo **Aggiungi nome client OAuth** specificare un **nome client** e fare clic sul pulsante **Salva** .

    ![Aggiunta del token di autenticazione utente Netskope](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Nome del CLient di autenticazione utente Netskope](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Copiare l' **URL del server scim** e il **token**. Questi valori verranno immessi rispettivamente nei campi URL tenant e token Secret nella scheda provisioning dell'applicazione di autenticazione utente Netskope nel portale di Azure.

    ![Token di creazione autenticazione utente Netskope](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Aggiungere l'autenticazione utente di Netskope dalla raccolta

Prima di configurare l'autenticazione utente di Netskope per il provisioning utenti automatico con Azure AD, è necessario aggiungere l'autenticazione utente di Netskope dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere l'autenticazione utente Netskope dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **autenticazione utente Netskope**, selezionare **autenticazione utente Netskope** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Netskope l'autenticazione utente nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>Configurazione del provisioning utenti automatico per l'autenticazione utente Netskope 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Netskope autenticazione utente in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per l'autenticazione utente Netskope seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di autenticazione utente di Netskope](./netskope-cloud-security-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM dell'autenticazione utente di Netskope, vedere [questo](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)articolo.

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>Per configurare il provisioning utenti automatico per l'autenticazione utente di Netskope in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Netskope User Authentication**.

    ![Collegamento di autenticazione utente di Netskope nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni Gestisci con l'opzione di provisioning denominata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa modalità di provisioning con l'opzione automatica chiamata.](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** , immettere il valore dell' **URL del server scim** recuperato in precedenza nell' **URL del tenant**. Immettere il valore del **token** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi all'autenticazione utente di Netskope. Se la connessione non riesce, verificare che l'account di autenticazione utente di Netskope disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti per Netskope l'autenticazione utente**.

    ![Mapping utente di autenticazione utente Netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD all'autenticazione utente Netskope nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Netskope autenticazione utente per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di autenticazione utente Netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory gruppi per Netskope l'autenticazione utente**.

    ![Mapping dei gruppi di autenticazione utente Netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD all'autenticazione utente Netskope nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi nell'autenticazione utente Netskope per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo di autenticazione utente Netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per l'autenticazione utente Netskope, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning per Netskope l'autenticazione utente scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad per l'autenticazione utente di Netskope.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)