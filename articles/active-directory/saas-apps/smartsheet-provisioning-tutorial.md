---
title: 'Esercitazione: Configurare Smartsheet per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Smartsheet.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Smartsheet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063195"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Esercitazione: Configurare Smartsheet per il provisioning automatico degli utenti

The objective of this tutorial is to demonstrate the steps to be performed in Smartsheet and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Smartsheet.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Smartsheet](https://www.smartsheet.com/pricing)
* Un account utente in un piano Smartsheet Enterprise o Enterprise Premier con autorizzazioni di amministratore di sistema.

## <a name="assign-users-to-smartsheet"></a>Assegnare utenti a Smartsheet

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Smartsheet. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Smartsheet seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Suggerimenti importanti per l'assegnazione di utenti a Smartsheet

* È consigliabile assegnare un singolo utente di Azure AD a Smartsheet per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando assegni un utente a Smartsheet, devi selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

* Per garantire la parità nelle assegnazioni dei ruoli utente tra Smartsheet e Azure AD, è consigliabile utilizzare le stesse assegnazioni di ruolo popolate nell'elenco completo degli utenti di Smartsheet. Per recuperare questo elenco di utenti da Smartsheet, passare a **Amministratore account > Gestione utenti > Altre azioni > Scarica elenco utenti (csv)**.

* Per accedere a determinate funzioni dell'app, Smartsheet richiede che un utente abbia più ruoli. Per ulteriori informazioni sui tipi di utenti e sulle autorizzazioni in Smartsheet, vai a [Tipi di utente e autorizzazioni](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se un utente ha più ruoli assegnati in Smartsheet, è **necessario** assicurarsi che queste assegnazioni di ruolo vengano replicate in Azure AD per evitare uno scenario in cui gli utenti potrebbero perdere l'accesso agli oggetti Smartsheet in modo permanente. Ogni ruolo univoco in Smartsheet **DEVE** essere assegnato a un gruppo diverso in Azure AD. L'utente **DEVE** quindi essere aggiunto a ciascuno dei gruppi corrispondenti ai ruoli desiderati. 

## <a name="set-up-smartsheet-for-provisioning"></a>Configurare Smartsheet per il provisioning

Prima di configurare Smartsheet per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM in Smartsheet.

1. Accedere come **SysAdmin** nel **[portale Smartsheet](https://app.smartsheet.com/b/home)** e passare a **Amministratore account**.

    ![Amministratore dell'account Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Passare a **Controlli di sicurezza > Modifica > provisioning automatico utente**.

    ![Controlli di sicurezza Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Aggiungere e convalidare i domini di posta elettronica per gli utenti di cui si prevede di eseguire il provisioning da Azure AD a Smartsheet.Add and validate the email domains for the users that you plan for provision from Azure AD to Smartsheet. Scegliere **Non abilitato** per assicurarsi che tutte le azioni di provisioning provengano solo da Azure AD e che l'elenco di utenti Smartsheet sia sincronizzato con le assegnazioni di Azure AD.

    ![Provisioning degli utenti Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Una volta completata la convalida, sarà necessario attivare il dominio. 

    ![Smartsheet Attiva Dominio](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generare il **token segreto** necessario per configurare il provisioning automatico degli utenti con Azure AD passando ad **App e integrazioni**.

    ![Installazione Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Scegliere **Accesso API**. Fare clic su **Genera nuovo token**di accesso .

    ![Installazione Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definire il nome del token di accesso API. Fare clic su **OK**.

    ![Installazione Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copiare il token di accesso API e salvarlo come questo sarà l'unico momento in cui è possibile visualizzarlo. Questa operazione è necessaria nel campo **Token segreto** in Azure AD.

    ![Token Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Aggiungere Smartsheet dalla galleria

Per configurare Smartsheet per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Smartsheet dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, inserisci **Smartsheet,** seleziona **Smartsheet** nel pannello dei risultati. 

    ![Smartsheet nell'elenco dei risultati](common/search-new-app.png)

5. Seleziona il pulsante **Iscriviti** a Smartsheet che ti reindirizzerà alla pagina di accesso di Smartsheet. 

    ![Smartsheet OIDC Aggiungi](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Poiché Smartsheet è un'app OpenIDConnect, scegli di accedere a Smartsheet utilizzando il tuo account di lavoro Microsoft.

    ![Accesso Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Dopo una corretta autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e verrà reindirizzato all'account Smartsheet.

    ![Consenso OIDc Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Configurare il provisioning automatico degli utenti in Smartsheet 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Smartsheet in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Smartsheet in Azure AD:To configure automatic user provisioning for Smartsheet in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Smartsheet**.

    ![Il collegamento Smartsheet nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://scim.smartsheet.com/v2/` amministratore immettere **l'input**in URL tenant. Inserisci il valore che hai recuperato e salvato in precedenza da Smartsheet in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Smartsheet.Click **Test Connection** to ensure Azure AD can connect to Smartsheet. Se la connessione non riesce, verificare che l'account Smartsheet disponga delle autorizzazioni SysAdmin e riprovare.

    ![token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con Smartsheet.**

    ![Mappature degli utenti Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Smartsheet nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Smartsheet per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Smartsheet, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for Smartsheet, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definisci gli utenti e/o i gruppi di cui desideri eseguire il provisioning in Smartsheet scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Smartsheet.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Smartsheet.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Smartsheet non supporta le eliminazioni soft. Quando l'attributo **attivo** di un utente è impostato su False, Smartsheet elimina l'utente in modo permanente.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
