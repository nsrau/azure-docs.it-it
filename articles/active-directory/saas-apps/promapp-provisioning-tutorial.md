---
title: 'Esercitazione: configurare Promapp per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Promapp.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 41190ba0-9032-4725-9d2c-b3dd9c7786e6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: Zhchia
ms.openlocfilehash: 3dd7786b4a976e11b263d89b66cbe5c34285606a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535236"
---
# <a name="tutorial-configure-promapp-for-automatic-user-provisioning"></a>Esercitazione: configurare Promapp per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Promapp e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Promapp.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di Promapp](https://www.promapp.com/licensing/)
* Un account utente in Promapp con autorizzazioni di amministratore.

## <a name="assigning-users-to-promapp"></a>Assegnazione di utenti a Promapp

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Promapp. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Promapp seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-promapp"></a>Suggerimenti importanti per l'assegnazione di utenti a Promapp

* È consigliabile assegnare un singolo Azure AD utente a Promapp per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Promapp, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-promapp-for-provisioning"></a>Configurare Promapp per il provisioning

1. Accedere a [Promapp Admin Console](https://freetrial.promapp.com/axelerate/Login.aspx). In nome utente passare a **profilo personale**.

    ![Console di amministrazione di Promapp](media/promapp-provisioning-tutorial/admin.png)

2.  In **token di accesso** fare clic sul pulsante **crea token** .

    ![Promapp aggiungere SCIM](media/promapp-provisioning-tutorial/addtoken.png)

3.  Immettere un nome nel campo **Descrizione** e selezionare **scim** dal menu a discesa **ambito** . Fare clic sull'icona Salva.

    ![Promapp Aggiungi nome](media/promapp-provisioning-tutorial/addname.png)

4.  Copiare il token di accesso e salvarlo perché questo sarà l'unico momento in cui è possibile visualizzarlo. Questo valore verrà immesso nel campo token segreto nella scheda provisioning dell'applicazione Promapp nel portale di Azure.

    ![Promapp creare token](media/promapp-provisioning-tutorial/token.png)

## <a name="add-promapp-from-the-gallery"></a>Aggiungere Promapp dalla raccolta

Prima di configurare Promapp per il provisioning utenti automatico con Azure AD, è necessario aggiungere Promapp dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Promapp dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Promapp**, selezionare **Promapp** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Promapp nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-promapp"></a>Configurazione del provisioning utenti automatico in Promapp 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Promapp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare Single Sign-On basate su SAML per Promapp seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di Promapp](https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-tutorial). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-promapp-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Promapp in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Promapp**.

    ![Collegamento di Promapp nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://api.promapp.com/api/scim` in **URL tenant**. Immettere il valore del **token di autenticazione scim** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Promapp. Se la connessione non riesce, verificare che l'account Promapp disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Promapp**.

    ![Mapping utente Promapp](media/promapp-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Promapp nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Promapp per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Promapp](media/promapp-provisioning-tutorial/userattributes.png)

11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning Azure AD per Promapp, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Promapp selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su Promapp.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

