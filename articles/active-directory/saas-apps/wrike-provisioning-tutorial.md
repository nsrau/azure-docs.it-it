---
title: 'Esercitazione: Configurare Wrike per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Wrike.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 53b1db1a8c4da59055c0af5f448fa0c8a6933daf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95988128"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Esercitazione: Configurare Wrike per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Wrike e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti o gruppi in Wrike.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni importanti su questo servizio e su come funziona, insieme alle domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning degli utenti in applicazioni SaaS (software-as-a-service) con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo generali di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Wrike](https://www.wrike.com/price/)
* Un account utente in Wrike con autorizzazioni di amministratore

## <a name="assign-users-to-wrike"></a>Assegnare utenti a Wrike
Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti o gruppi in Azure AD devono poter accedere a Wrike. Dopo averlo stabilito, assegnare gli utenti o i gruppi a Wrike seguendo le istruzioni fornite qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Suggerimenti importanti per l'assegnazione di utenti a Wrike

* È consigliabile assegnare un singolo utente di Azure AD a Wrike per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Wrike, selezionare qualsiasi ruolo valido specifico dell'applicazione, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning.

## <a name="set-up-wrike-for-provisioning"></a>Configurare Wrike per il provisioning

Prima di configurare Wrike per il provisioning utenti automatico tramite Azure AD, è necessario abilitare il provisioning SCIM (System for Cross-domain Identity Management) in Wrike.

1. Accedere alla [console di amministrazione di Wrike](https://www.Wrike.com/login/). Passare all'ID tenant. Selezionare **Apps & Integrations** (App e integrazioni).

    ![Apps & Integrations](media/Wrike-provisioning-tutorial/admin.png)

2.  Passare a **Azure AD** e selezionarlo.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selezionare SCIM. Copiare l'**URL di base**.

    ![URL di base](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selezionare **API** > **Azure SCIM**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Si aprirà una finestra popup. Immettere la stessa password creata in precedenza per creare un account.

    ![Creazione del token in Wrike](media/Wrike-provisioning-tutorial/password.png)

6.  Copiare il **token segreto** e incollarlo in Azure AD. Selezionare **Save** (Salva) per completare la configurazione del provisioning in Wrike.

    ![Permanent access token](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Aggiungere Wrike dalla raccolta

Prima di configurare Wrike per il provisioning utenti automatico con Azure AD, è necessario aggiungere Wrike dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS gestite.

Per aggiungere Wrike dalla raccolta di Azure AD, seguire questa procedura.

1. Nel riquadro di spostamento sinistro del [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Wrike**, selezionare **Wrike** nel pannello dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Wrike nell'elenco risultati](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Configurare il provisioning utenti automatico per Wrike 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti o gruppi in Wrike in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> Per abilitare l'accesso Single Sign-On basato su SAML per Wrike, seguire le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per Wrike](wrike-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Configurare il provisioning utenti automatico per Wrike in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Tutte le applicazioni](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Wrike**.

    ![Collegamento di Wrike nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning impostata su Automatico](common/provisioning-automatic.png)

5. Nella sezione Credenziali amministratore immettere i valori di **Base URL** (URL di base) e **Permanent access token** (Token di accesso permanente) recuperati in precedenza rispettivamente nei campi **URL tenant** e **Token segreto**. Selezionare **Test connessione** per verificare che Azure AD possa connettersi a Wrike. Se la connessione non riesce, verificare che l'account Wrike abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

7. Nella casella **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![E-mail di notifica](common/provisioning-notification-email.png)

8. Selezionare **Salva**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Wrike** (Sincronizza utenti di Azure Active Directory in Wrike).

    ![Mapping utente di Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a Wrike nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Wrike per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning di Azure AD per Wrike, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti o i gruppi di cui si vuole effettuare il provisioning in Wrike selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti nell'**Ambito** della sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto a quelle successive. Per altre informazioni sul tempo necessario per effettuare il provisioning di utenti o gruppi, vedere [Quanto tempo sarà necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **Stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Wrike. Per altre informazioni, vedere [Controllare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)