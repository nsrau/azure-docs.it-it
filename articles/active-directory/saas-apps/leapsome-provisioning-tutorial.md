---
title: 'Esercitazione: Configurare Leapsome per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in Leapsome.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e02deaa29b40e64b63d9afb471717feef78b3cee
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672686"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Esercitazione: Configurare Leapsome per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Leapsome e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e deprovisioning di utenti e/o gruppi a Leapsome.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente in anteprima. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* Oggetto [Leapsome](https://www.Leapsome.com/en/pricing) tenant.
* Un account utente in Leapsome con autorizzazioni di amministratore.

## <a name="assigning-users-to-leapsome"></a>Assegnazione di utenti a Leapsome

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a Leapsome. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a Leapsome seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Suggerimenti importanti per l'assegnazione di utenti a Leapsome

* È consigliabile che un singolo utente di Azure AD viene assegnato a Leapsome per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Leapsome, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.


## <a name="setup-leapsome-for-provisioning"></a>Leapsome il programma di installazione per il provisioning

1. Accedi per i [Console di amministrazione Leapsome](https://www.Leapsome.com/app/#/login). Passare a **Impostazioni > Impostazioni di amministrazione**.

    ![Console di amministrazione Leapsome](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Passare a **integrazioni > provisioning SCIM utenti**.

    ![Leapsome aggiungere SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Copia il **Token di autenticazione SCIM**. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Leapsome nel portale di Azure.

    ![Creare Token Leapsome](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Aggiungere Leapsome dalla raccolta

Prima di configurare Leapsome per provisioning utenti automatico con Azure AD, è necessario aggiungere Leapsome dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Leapsome dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel  **[portale di Azure](https://portal.azure.com)** , nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Leapsome**, selezionare **Leapsome** nel pannello dei risultati e quindi fare clic sui **Add** pulsante per aggiungere l'applicazione.

    ![Leapsome nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Configurazione del provisioning utenti automatico in Leapsome 

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in Leapsome base assegnazioni utente e/o di gruppo in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare basato su SAML single sign-on per Leapsome, seguendo le istruzioni disponibili nel [esercitazione Leapsome Single sign-on](Leapsome-tutorial.md). Accesso Single sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità complementari

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Leapsome in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**, quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Leapsome**.

    ![Collegamento di Leapsome nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda provisioning](common/provisioning-automatic.png)

5. Sotto il **le credenziali di amministratore** sezione di input `https://www.leapsome.com/api/scim` nelle **URL del Tenant**. Input di **Token di autenticazione SCIM** valore recuperato in precedenza nella **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Leapsome. Se la connessione non riesce, verificare che l'account Leapsome abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e Token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Notifica tramite posta elettronica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users per Leapsome**.

    ![Mapping utente Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Leapsome nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in Leapsome per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. Sotto il **mapping** sezione, selezionare **sincronizzare Azure gruppi di Active Directory per Leapsome**.

    ![Mapping dei gruppi Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Esaminare gli attributi gruppo sincronizzati da Azure AD a Leapsome nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** proprietà usate devono rispettare i gruppi in Leapsome per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il provisioning del servizio per Leapsome AD Azure, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Lo stato di provisioning sia attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in Leapsome selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il provisioning di ambito](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio di configurazione del Provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal servizio su Leapsome di provisioning di Azure AD provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* È necessario Leapsome **userName** sia univoco.
* Leapsome consente solo indirizzi di posta elettronica di lavoro deve essere salvato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
