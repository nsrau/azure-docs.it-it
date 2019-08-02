---
title: 'Esercitazione: Configurare DruVa per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in DruVa.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 27c8ca3a4b68d5f3a42777ff2cf9afa4b923c00b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641825"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Esercitazione: Configurare DruVa per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in DruVa e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in DruVa.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di DruVa](https://www.druva.com/products/pricing-plans/).
* Un account utente in DruVa con autorizzazioni di amministratore.

## <a name="assigning-users-to-druva"></a>Assegnazione di utenti a DruVa

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a DruVa. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a DruVa seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Suggerimenti importanti per l'assegnazione di utenti a DruVa

* È consigliabile assegnare un singolo Azure AD utente a DruVa per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a DruVa, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-druva-for-provisioning"></a>Configurare DruVa per il provisioning

Prima di configurare DruVa per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in DruVa.

1. Accedere a [DruVa Admin Console](https://console.druva.com). Passare a **Druva > InSync**.

    ![Console di amministrazione di DruVa](media/druva-provisioning-tutorial/menubar.png)

2. Passare a **Gestisci** > **distribuzioni** > **utenti**.

    ![DruVa aggiungere SCIM](media/druva-provisioning-tutorial/manage.png)

3.  Passare a **Impostazioni**. Fare clic su **genera token**.

    ![DruVa aggiungere SCIM](media/druva-provisioning-tutorial/settings.png)

4.  Copiare il valore del **token di autenticazione** . Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione Druva nel portale di Azure.
    
    ![DruVa aggiungere SCIM](media/druva-provisioning-tutorial/auth.png)

## <a name="add-druva-from-the-gallery"></a>Aggiungere DruVa dalla raccolta

Per configurare DruVa per il provisioning utenti automatico con Azure AD, è necessario aggiungere DruVa dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere DruVa dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **DruVa**, selezionare **DruVa** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Druva nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Configurazione del provisioning utenti automatico in DruVa 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in DruVa in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per DruVa, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di DruVa](druva-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Per configurare il provisioning utenti automatico per DruVa in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Druva**.

    ![Collegamento di Druva nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5.  Nella sezione credenziali amministratore immettere `https://apis.druva.com/insync/scim` in **URL tenant**. Immettere il valore del **token di autenticazione** nel **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a DruVa. Se la connessione non riesce, verificare che l'account DruVa disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche degli errori di provisioning e selezionare **Invia una notifica di posta elettronica quando si verifica un errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a DruVa**.

    ![Mapping utente DruVa](media/druva-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a DruVa nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in DruVa per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di DruVa](media/druva-provisioning-tutorial/userattribute.png)


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per DruVa, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in DruVa selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su DruVa.

    Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Limitazioni dei connettori

* DruVa richiede un **messaggio di posta elettronica** come attributo obbligatorio. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md).
