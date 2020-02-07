---
title: 'Esercitazione: configurare Smartsheet per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Smartsheet.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063195"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Esercitazione: configurare Smartsheet per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Smartsheet e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Smartsheet.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisites

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant Smartsheet](https://www.smartsheet.com/pricing)
* Un account utente in un piano Smartsheet Enterprise o Enterprise Premier con autorizzazioni di amministratore di sistema.

## <a name="assign-users-to-smartsheet"></a>Assegnazione di utenti a Smartsheet

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Smartsheet. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a Smartsheet seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Suggerimenti importanti per l'assegnazione di utenti a Smartsheet

* È consigliabile assegnare un singolo Azure AD utente a Smartsheet per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Smartsheet, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

* Per garantire la parità nelle assegnazioni di ruolo utente tra Smartsheet e Azure AD, è consigliabile usare le stesse assegnazioni di ruolo compilate nell'elenco completo degli utenti Smartsheet. Per recuperare l'elenco di utenti da Smartsheet, passare a **amministratore Account > gestione utenti > altre azioni > Scarica elenco utenti (CSV)** .

* Per accedere a determinate funzionalità nell'app, Smartsheet richiede che un utente disponga di più ruoli. Per altre informazioni sui tipi di utente e le autorizzazioni in Smartsheet, vedere [tipi di utente e autorizzazioni](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se un utente dispone di più ruoli assegnati in Smartsheet, è **necessario** assicurarsi che queste assegnazioni di ruolo vengano replicate in Azure ad per evitare uno scenario in cui gli utenti potrebbero perdere l'accesso agli oggetti Smartsheet in modo permanente. Ogni ruolo univoco in Smartsheet **deve** essere assegnato a un altro gruppo in Azure ad. L'utente **deve** quindi essere aggiunto a ognuno dei gruppi corrispondenti ai ruoli desiderati. 

## <a name="set-up-smartsheet-for-provisioning"></a>Configurare Smartsheet per il provisioning

Prima di configurare Smartsheet per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM su Smartsheet.

1. Accedere come **sysadmin** nel **[portale Smartsheet](https://app.smartsheet.com/b/home)** e passare a **account admin**.

    ![Amministratore account Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Passare a **controlli di sicurezza > provisioning automatico dell'utente > modifica**.

    ![Controlli di sicurezza Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Aggiungere e convalidare i domini di posta elettronica per gli utenti di cui si intende eseguire il provisioning da Azure AD a Smartsheet. Scegliere **non abilitato** per assicurarsi che tutte le azioni di provisioning abbiano origine solo da Azure ad e per assicurarsi che l'elenco di utenti Smartsheet sia sincronizzato con Azure ad assegnazioni.

    ![Provisioning utenti Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Al termine della convalida, sarà necessario attivare il dominio. 

    ![Attiva dominio Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generare il **token segreto** necessario per configurare il provisioning utenti automatico con Azure ad passando ad **app e integrazioni**.

    ![Installazione di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Scegliere **accesso all'API**. Fare clic su **genera nuovo token di accesso**.

    ![Installazione di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definire il nome del token di accesso all'API. Fare clic su **OK**.

    ![Installazione di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copiare il token di accesso all'API e salvarlo perché questo sarà l'unico momento in cui è possibile visualizzarlo. Questa operazione è necessaria nel campo **token segreto** in Azure ad.

    ![Token Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Aggiungere Smartsheet dalla raccolta

Per configurare Smartsheet per il provisioning utenti automatico con Azure AD, è necessario aggiungere Smartsheet dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Smartsheet**e selezionare **Smartsheet** nel pannello dei risultati. 

    ![Smartsheet nell'elenco dei risultati](common/search-new-app.png)

5. Selezionare il pulsante **Iscriviti per Smartsheet per** reindirizzare l'utente alla pagina di accesso di Smartsheet. 

    ![Aggiunta OIDC Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Poiché Smartsheet è un'app OpenIDConnect, scegliere di accedere a Smartsheet usando il proprio account Microsoft.

    ![Accesso OIDC Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Una volta completata l'autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati al proprio account Smartsheet.

    ![Consenso OIDc Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Configurare il provisioning utenti automatico in Smartsheet 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Smartsheet in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Smartsheet in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Smartsheet**.

    ![Collegamento di Smartsheet nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://scim.smartsheet.com/v2/` in **URL tenant**. Immettere il valore recuperato e salvato in precedenza da Smartsheet in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Smartsheet. Se la connessione non riesce, verificare che l'account Smartsheet disponga delle autorizzazioni SysAdmin e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Smartsheet**.

    ![Mapping utente Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Smartsheet nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Smartsheet per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per Smartsheet, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Smartsheet scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad su Smartsheet.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Smartsheet non supporta le eliminazioni automatiche. Quando l'attributo **attivo** di un utente è impostato su false, Smartsheet Elimina l'utente in modo permanente.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
