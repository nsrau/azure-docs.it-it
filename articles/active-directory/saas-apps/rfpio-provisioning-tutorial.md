---
title: 'Esercitazione: Configurare RFPIO per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in RFPIO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 54419db4-47d5-4fb4-ab74-7b0b28afb11b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b7b50ebf9d5d3dec52d368c5bba9ac0e5406ef38
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737810"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Esercitazione: Configurare RFPIO per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in RFPIO e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in RFPIO.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di RFPIO](https://www.rfpio.com/product/).
* Un account utente in RFPIO con autorizzazioni di amministratore.

## <a name="assigning-users-to-rfpio"></a>Assegnazione di utenti a RFPIO

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a RFPIO. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a RFPIO seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Suggerimenti importanti per l'assegnazione di utenti a RFPIO

* È consigliabile assegnare un singolo Azure AD utente a RFPIO per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a RFPIO, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-rfpio-for-provisioning"></a>Configurare RFPIO per il provisioning

Prima di configurare RFPIO per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in RFPIO.

1.  Accedere a RFPIO Admin Console. In basso a sinistra nella console di amministrazione fare clic su **tenant**.

    ![Console di amministrazione di RFPIO](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Fare clic su **Impostazioni organizzazione**.
    
    ![Amministratore di RFPIO](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Passare a **gestione** > utenti**scim** **sicurezza** > .

    ![RFPIO aggiungere SCIM](media/rfpio-provisioning-tutorial/scim.png)

4.  Verificare che sia abilitato il provisioning **utenti automatico** . Fare clic su **genera token API scim**.

    ![RFPIO creare token](media/rfpio-provisioning-tutorial/generate.png)

5.  Salvare il **token dell'API scim** perché questo token non verrà visualizzato di nuovo per finalità di sicurezza. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione RFPIO nel portale di Azure.

    ![RFPIO creare token](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>Aggiungere RFPIO dalla raccolta

Per configurare RFPIO per il provisioning utenti automatico con Azure AD, è necessario aggiungere RFPIO dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere RFPIO dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **RFPIO**, selezionare **RFPIO** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![RFPIO nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Configurazione del provisioning utenti automatico in RFPIO 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in RFPIO in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per RFPIO, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di RFPIO](rfpio-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Per configurare il provisioning utenti automatico per RFPIO in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **RFPIO**.

    ![Collegamento di RFPIO nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` in **URL tenant**. Un valore di esempio `https://Azure-test1.rfpio.com/rfpserver/scim/v2`è. Immettere il valore del **token API scim** recuperato in precedenza nel **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a RFPIO. Se la connessione non riesce, verificare che l'account RFPIO disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a RFPIO**.

    ![Mapping utente RFPIO](media/rfpio-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a RFPIO nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in RFPIO per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di RFPIO](media/rfpio-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per RFPIO, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in RFPIO selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su RFPIO.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* RFPIO non supporta attualmente il provisioning di gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
