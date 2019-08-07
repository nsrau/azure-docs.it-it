---
title: 'Esercitazione: Configurare StarLeaf per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in StarLeaf.
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
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 0676eb2830c2e0233eb182cfec0be3f39c6a39e9
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737745"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Esercitazione: Configurare StarLeaf per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in StarLeaf e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in StarLeaf.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di StarLeaf](https://www.starleaf.com/solutions/).
* Un account utente in StarLeaf con autorizzazioni di amministratore.

## <a name="assign-users-to-starleaf"></a>Assegnare gli utenti a StarLeaf
Azure Active Directory usa un concetto denominato assegnazioni per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario decidere quali utenti e gruppi in Azure AD necessario accedere a StarLeaf. È quindi possibile assegnare utenti e gruppi a StarLeaf seguendo [queste istruzioni](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Suggerimenti importanti per l'assegnazione di utenti a StarLeaf

* È consigliabile assegnare un singolo Azure AD utente a StarLeaf per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a StarLeaf, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning.

## <a name="set-up-starleaf-for-provisioning"></a>Configurare StarLeaf per il provisioning

Prima di configurare StarLeaf per il provisioning utenti automatico con Azure AD, sarà necessario configurare il provisioning di SCIM in StarLeaf:

1. Accedere a [StarLeaf Admin Console](https://portal.starleaf.com/#page=login). Passare a **Integrations** > **Aggiungi integrazione**.

    ![StarLeaf aggiungere SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Consente di selezionare il **tipo** da Microsoft Azure Active Directory. Immettere un nome appropriato nel **nome**. Fare clic su **Apply**.

    ![StarLeaf aggiungere SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  Verranno quindi visualizzati i valori dell'URL di base e del **token di accesso** **scim** . Questi valori verranno immessi nei campi **URL tenant** e **token segreto** nella scheda provisioning dell'applicazione StarLeaf nel portale di Azure. 

    ![StarLeaf creare token](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Aggiungere StarLeaf dalla raccolta

Per configurare StarLeaf per il provisioning utenti automatico con Azure AD, è necessario aggiungere StarLeaf dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere StarLeaf dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **StarLeaf**, selezionare **StarLeaf** nel pannello dei risultati.
    ![StarLeaf nell'elenco risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Configurare il provisioning utenti automatico in StarLeaf

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in StarLeaf in base alle assegnazioni di utenti e/o gruppi in Azure AD.

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **StarLeaf**.

    ![Collegamento di StarLeaf nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione credenziali amministratore immettere l'URL di **base di SCIM** e i valori dei **token di accesso** recuperati in precedenza rispettivamente in **URL tenant** e **token segreto** . Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a StarLeaf. Se la connessione non riesce, verificare che l'account StarLeaf disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning e selezionare la casella **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a StarLeaf**.

    ![StarLeaf creare token](media/starleaf-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a StarLeaf nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in StarLeaf per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![StarLeaf creare token](media/starleaf-provisioning-tutorial/userattribute.png)


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).


11. Per abilitare il servizio di provisioning Azure AD per StarLeaf, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in StarLeaf selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su StarLeaf.

Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitazioni dei connettori

* StarLeaf attualmente non supporta il provisioning del gruppo. 
* StarLeaf richiede che i valori di **indirizzo di posta elettronica** e **nome utente** abbiano lo stesso valore di origine.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [esaminare i log e ottenere report sulle attività](../manage-apps/check-status-user-account-provisioning.md)di provisioning.
