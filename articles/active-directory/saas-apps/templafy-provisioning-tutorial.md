---
title: 'Esercitazione: Configurare Templafy per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Templafy.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 230877d5-e466-4449-82c8-88cfa42f6501
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 96270ccccd8f043f116f686938c8f47c5f4f0a41
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2019
ms.locfileid: "68842400"
---
# <a name="tutorial-configure-templafy-for-automatic-user-provisioning"></a>Esercitazione: Configurare Templafy per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Templafy e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Templafy.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di Templafy](https://www.templafy.com/pricing/).
* Un account utente in Templafy con autorizzazioni di amministratore.

## <a name="assigning-users-to-templafy"></a>Assegnazione di utenti a Templafy

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Templafy. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Templafy seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy"></a>Suggerimenti importanti per l'assegnazione di utenti a Templafy

* È consigliabile assegnare un singolo Azure AD utente a Templafy per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Templafy, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-templafy-for-provisioning"></a>Configurare Templafy per il provisioning

Prima di configurare Templafy per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in Templafy.

1. Accedere a Templafy Admin Console. Fare clic su **Amministrazione**.

    ![Console di amministrazione di Templafy](media/templafy-provisioning-tutorial/image00.png)

2. Fare clic su **metodo di autenticazione**.

    ![Templafy aggiungere SCIM](media/templafy-provisioning-tutorial/image01.png)

3. Copiare il valore della **chiave API scim** . Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione Templafy nel portale di Azure.

    ![Templafy aggiungere SCIM](media/templafy-provisioning-tutorial/image02.png)

## <a name="add-templafy-from-the-gallery"></a>Aggiungere Templafy dalla raccolta

Per configurare Templafy per il provisioning utenti automatico con Azure AD, è necessario aggiungere Templafy dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Templafy dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Templafy**, selezionare **Templafy** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Templafy nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-templafy"></a>Configurazione del provisioning utenti automatico in Templafy 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Templafy in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per Templafy, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di Templafy](templafy-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-templafy-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Templafy in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Templafy**.

    ![Collegamento di Templafy nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://scim.templafy.com/scim` in **URL tenant**. Immettere il valore della **chiave API scim** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Templafy. Se la connessione non riesce, verificare che l'account Templafy disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Templafy**.

    ![Mapping utente Templafy](media/templafy-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Templafy nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Templafy per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Templafy](media/templafy-provisioning-tutorial/userattribute.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to Templafy**.

    ![Mapping del gruppo Templafy](media/templafy-provisioning-tutorial/groupmapping.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Templafy nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Templafy per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Templafy](media/templafy-provisioning-tutorial/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Templafy, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Templafy selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su Templafy.

    Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md)
    
## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
