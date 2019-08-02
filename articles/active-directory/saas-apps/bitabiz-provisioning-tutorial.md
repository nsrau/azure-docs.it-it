---
title: 'Esercitazione: Configurare BitaBIZ per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in BitaBIZ.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d0d38abe-c041-482a-9d3f-ca340678c226
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: a143164f43f999dcb0740cfc25951882000eafbe
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641643"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Esercitazione: Configurare BitaBIZ per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in BitaBIZ e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in BitaBIZ.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di BitaBIZ](https://bitabiz.dk/en/price/).
* Un account utente in BitaBIZ con autorizzazioni di amministratore.

## <a name="assigning-users-to-bitabiz"></a>Assegnazione di utenti a BitaBIZ

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a BitaBIZ. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a BitaBIZ seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Suggerimenti importanti per l'assegnazione di utenti a BitaBIZ

* È consigliabile assegnare un singolo Azure AD utente a BitaBIZ per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a BitaBIZ, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-bitabiz-for-provisioning"></a>Configurare BitaBIZ per il provisioning

Prima di configurare BitaBIZ per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in BitaBIZ.

1. Accedere a [BitaBIZ Admin Console](https://www.bitabiz.com/login?lang=en). Fare clic su **SETUP ADMIN** (CONFIGURA AMMINISTRATORE).

    ![Console di amministrazione di BitaBIZ](media/bitabiz-provisioning-tutorial/setup-admin.png)

2.  Passare all' **integrazione**.

    ![Console di amministrazione di BitaBIZ](media/bitabiz-provisioning-tutorial/integration.png)

2.  Passare a **Microsoft Azure ad**provisioning.  Selezionare **abilitato** nel provisioning utenti automatico. Copiare i valori per l' **URL dell'endpoint** di provisioning di SCIM e il token di **porta**. Questi valori verranno immessi nei campi URL tenant e token segreto nella scheda provisioning dell'applicazione BitaBIZ nel portale di Azure.

    ![BitaBIZ aggiungere SCIM](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Aggiungere BitaBIZ dalla raccolta

Per configurare BitaBIZ per il provisioning utenti automatico con Azure AD, è necessario aggiungere BitaBIZ dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere BitaBIZ dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **BitaBIZ**, selezionare **BitaBIZ** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![BitaBIZ nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Configurazione del provisioning utenti automatico in BitaBIZ 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in BitaBIZ in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per BitaBIZ, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di BitaBIZ](BitaBIZ-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Per configurare il provisioning utenti automatico per BitaBIZ in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **BitaBIZ**.

    ![Collegamento di BitaBIZ nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione credenziali amministratore immettere rispettivamente i valori dell'URL di provisioning dell'endpoint e del **token di porta** **scim** recuperati in precedenza nell'URL del tenant e nel token del segreto. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a BitaBIZ. Se la connessione non riesce, verificare che l'account BitaBIZ disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a BitaBIZ**.

    ![Mapping utente BitaBIZ](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a BitaBIZ nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in BitaBIZ per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di BitaBIZ](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per BitaBIZ, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in BitaBIZ selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su BitaBIZ.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* BitaBIZ richiede **nome utente**, **indirizzo di posta elettronica**, **FirstName** e **LastName** come attributi obbligatori. 
* BitaBIZ non supporta attualmente le eliminazioni a livello di hardware.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md).
