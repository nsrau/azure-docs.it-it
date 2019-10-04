---
title: 'Esercitazione: Configurare Smartsheet per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in Smartsheet.
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
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670941"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Esercitazione: Configurare Smartsheet per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Smartsheet e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e deprovisioning di utenti e/o gruppi a Smartsheet.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Smartsheet](https://www.smartsheet.com/pricing)
* Un account utente in un piano Premier Enterprise o Enterprise di Smartsheet con autorizzazioni di amministratore di sistema.

## <a name="assign-users-to-smartsheet"></a>Assegnare gli utenti a Smartsheet

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo di utenti e/o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a Smartsheet. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a Smartsheet seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Suggerimenti importanti per l'assegnazione di utenti a Smartsheet

* È consigliabile che un singolo utente di Azure AD viene assegnato a Smartsheet per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Smartsheet, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

* Per garantire parità nelle assegnazioni di ruolo utente tra Smartsheet e Azure AD, è consigliabile usare le assegnazioni di ruolo stessi popolate nell'elenco di utenti di Smartsheet completo. Per recuperare l'elenco di utenti da Smartsheet, passare a **Account Admin > Gestione utente > altre azioni > Scarica elenco di utenti (csv)** .

* Per accedere a determinate funzionalità nell'app, Smartsheet richiede all'utente di avere più ruoli. Per altre informazioni sui tipi di utente e le autorizzazioni in Smartsheet, passare a [tipi di utente e autorizzazioni](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se un utente dispone di più ruoli assegnati in Smartsheet, si **necessario** assicurarsi che queste assegnazioni di ruolo vengono replicate in Azure AD per evitare uno scenario in cui gli utenti può perdere l'accesso agli oggetti di Smartsheet in modo permanente. Ogni ruolo univoco in Smartsheet **necessario** essere assegnato a un altro gruppo in Azure AD. L'utente **necessario** aggiunti per ognuno dei gruppi corrispondenti a ruoli desiderati. 

## <a name="set-up-smartsheet-for-provisioning"></a>Configurare Smartsheet per il provisioning

Prima di configurare Smartsheet per provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in Smartsheet.

1. Accedere come un **SysAdmin** nel **[portale Smartsheet](https://app.smartsheet.com/b/home)** e passare alla **amministratore dell'Account**.

    ![Amministratore dell'Account di Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Passare a **controlli di sicurezza > utente automatico Provisioning > Modifica**.

    ![Controlli di sicurezza di Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Aggiungere e verificare i domini di posta elettronica per gli utenti che si prevede di eseguire il provisioning da Azure AD in Smartsheet. Scegli **non è abilitato** per assicurarsi che tutte le azioni di provisioning provengono solo da Azure AD e per assicurarsi anche che l'elenco di utenti di Smartsheet sia sincronizzato con le assegnazioni di Azure AD.

    ![Il provisioning degli utenti di Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Dopo aver completata la convalida, è necessario attivare il dominio. 

    ![Smartsheet attivare dominio](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generare il **Token segreto** necessarie per configurare il provisioning utenti automatico con Azure AD, passare a **le App e le integrazioni**.

    ![Installazione di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Scegli **l'accesso all'API**. Fare clic su **Genera nuovo token di accesso**.

    ![Installazione di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definire il nome del Token di accesso API. Fare clic su **OK**.

    ![Installazione di Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copiare il Token di accesso API e salvarlo come sarà l'unica volta che sia possibile esaminarla. Questa operazione è necessaria la **Token segreto** campo in Azure AD.

    ![Token Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Aggiungere Smartsheet dalla raccolta

Per configurare Smartsheet per provisioning utenti automatico con Azure AD, è necessario aggiungere Smartsheet dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel  **[portale di Azure](https://portal.azure.com)** , nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Smartsheet**, selezionare **Smartsheet** nel pannello dei risultati. 

    ![Smartsheet nell'elenco risultati](common/search-new-app.png)

5. Selezionare il **iscriversi a Smartsheet** pulsante che verrà reindirizzati alla pagina di accesso di Smartsheet. 

    ![Aggiungere Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Smartsheet è un'app di openid Connect, scegliere di eseguire l'accesso a Smartsheet con l'account aziendale di Microsoft.

    ![Account di accesso di Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Dopo il completamento dell'autenticazione, accettare la richiesta di consenso per la pagina di consenso. Verrà aggiunti automaticamente all'applicazione nel tenant e si verrà reindirizzati al proprio account Smartsheet.

    ![Consenso OIDc Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Configurare il provisioning utenti automatico in Smartsheet 

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in Smartsheet base alle assegnazioni utente e/o di gruppo in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Smartsheet in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**, quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Smartsheet**.

    ![Il collegamento di Smartsheet nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda provisioning](common/provisioning-automatic.png)

5. Sotto il **le credenziali di amministratore** sezione di input `https://scim.smartsheet.com/v2/` nelle **URL del Tenant**. Il valore che è stato recuperato e salvato in precedenza da Smartsheet in ingresso **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Smartsheet. Se la connessione non riesce, verificare che l'account di Smartsheet disponga delle autorizzazioni SysAdmin e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Notifica tramite posta elettronica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users a Smartsheet**.

    ![Mapping utente Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Smartsheet nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in Smartsheet per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il provisioning del servizio per Smartsheet AD Azure, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Lo stato di provisioning sia attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in Smartsheet selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il provisioning di ambito](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio di configurazione del Provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività, che descrivono tutte le azioni eseguite dal servizio in Smartsheet di provisioning di Azure AD provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Smartsheet nepodporuje eliminato temporaneamente. Quando un utente **active** attributo è impostato su False, Smartsheet Elimina definitivamente l'utente.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
