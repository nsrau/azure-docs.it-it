---
title: 'Esercitazione: Configurare StarLeaf per il provisioning automatico degli utenti con Azure Active Directory Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in StarLeaf.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to StarLeaf.
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
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064292"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Esercitazione: Configurare StarLeaf per il provisioning automatico degli utentiTutorial: Configure StarLeaf for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in StarLeaf e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in StarLeaf.The objective of this tutorial is to demonstrate the steps to be performed in StarLeaf and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and/o provision users to StarLeaf.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un inquilino StarLeaf](https://www.starleaf.com/solutions/).
* Un account utente in StarLeaf con autorizzazioni di amministratore.

## <a name="assign-users-to-starleaf"></a>Assegnare utenti a StarLeafAssign users to StarLeaf
Azure Active Directory usa un concetto denominato assegnazioni per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e gruppi in Azure AD devono accedere a StarLeaf.Before you configure and enable automatic user provisioning, you should decide which users and groups in Azure AD need access to StarLeaf. Quindi è possibile assegnare gli utenti e i gruppi a StarLeaf seguendo [queste istruzioni](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Suggerimenti importanti per l'assegnazione di utenti a StarLeaf

* È consigliabile assegnare un singolo utente di Azure AD a StarLeaf per testare la configurazione del provisioning automatico degli utenti. Altri utenti e gruppi possono essere assegnati in un secondo momento.

* Quando si assegna un utente a StarLeaf, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito sono esclusi dal provisioning.

## <a name="set-up-starleaf-for-provisioning"></a>Configurare StarLeaf per il provisioning

Prima di configurare StarLeaf per il provisioning automatico degli utenti con Azure AD, è necessario configurare il provisioning SCIM in StarLeaf:

1. Accedi alla [Console di amministrazione StarLeaf](https://portal.starleaf.com/#page=login). Passare a **Integrazioni** > **Aggiungi integrazione**.

    ![StarLeaf Aggiungi SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Selezionare il **tipo** di Microsoft Azure Active Directory. Immettere un nome appropriato in **Nome**. Fare clic su **Applica**.

    ![StarLeaf Aggiungi SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  Verranno quindi visualizzati i valori **dell'URL di base ECIM** e del token di **accesso.** Questi valori verranno immessi nei campi URL tenant e **Token segreto** nella scheda Provisioning dell'applicazione StarLeaf nel portale di Azure.These values will be entered in the **Tenant URL** and Secret Token fields in the Provisioning tab of your StarLeaf application in the Azure portal. 

    ![StarLeaf Crea token](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Aggiungi StarLeaf dalla galleria

Per configurare StarLeaf per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere StarLeaf dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere StarLeaf dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add StarLeaf from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, inserisci **StarLeaf**, seleziona **StarLeaf** nel pannello dei risultati.
    ![StarLeaf nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Configurare il provisioning automatico degli utenti in StarLeaf

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in StarLeaf in base alle assegnazioni di utenti e/o gruppi in Azure AD.

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **StarLeaf**.

    ![Il collegamento StarLeaf nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione Credenziali amministratore immettere i valori **URL di base SCIM** e **Token di accesso** recuperati in precedenza rispettivamente in **URL tenant** e **Token segreto.** Fare clic su Test connessione per verificare che Azure AD possa connettersi a StarLeaf.Click **Test Connection** to ensure Azure AD can connect to StarLeaf. Se la connessione non riesce, assicurati che l'account StarLeaf disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **E-mail** di notifica immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e selezionare la casella **Invia una notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con StarLeaf**.

    ![StarLeaf Crea token](media/starleaf-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a StarLeaf nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in StarLeaf per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![StarLeaf Crea token](media/starleaf-provisioning-tutorial/userattribute.png)


10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Per abilitare il servizio di provisioning di Azure AD per StarLeaf, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for StarLeaf, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in StarLeaf scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in StarLeaf.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on StarLeaf.

Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utenteFor](../app-provisioning/check-status-user-account-provisioning.md) more information on how to read the Azure AD provisioning logs, see Reporting on automatic user account provisioning

## <a name="connector-limitations"></a>Limitazioni dei connettori

* StarLeaf attualmente non supporta il provisioning dei gruppi. 
* StarLeaf richiede che i valori **email** e **userName** abbiano lo stesso valore di origine.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendaliManage user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* Scopri come [esaminare i log e ottenere report sulle attività](../app-provisioning/check-status-user-account-provisioning.md)di provisioning.
