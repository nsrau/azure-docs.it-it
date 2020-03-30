---
title: 'Esercitazione: Configurare Harness per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Harness.Learn how to configure Azure Active Directory to automatically provision and deprovision user accounts to Harness.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057839"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Esercitazione: Configurare Harness per il provisioning automatico degli utentiTutorial: Configure Harness for automatic user provisioning

In this article, you learn how to configure Azure Active Directory (Azure AD) to automatically provision and deprovision users or groups to Harness.

> [!NOTE]
> Questo articolo descrive un connettore basato sul servizio di provisioning degli utenti di Azure AD. Per informazioni importanti su questo servizio e per le risposte alle domande frequenti, vedere Automatizzare il provisioning e il [deprovisioning degli utenti nelle applicazioni SaaS con Azure Active Directory.](../app-provisioning/user-provisioning.md)
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questo articolo presuppone che si dispone già dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un inquilino Harness](https://harness.io/pricing/)
* Un account utente in Harness con autorizzazioni *di amministratore*

## <a name="assign-users-to-harness"></a>Assegnare utenti a Harness

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, decidere quali utenti o gruppi in Azure AD devono accedere a Harness.Before you configure and enable automatic user provisioning, decide which users or groups in Azure AD need access to Harness. È quindi possibile assegnare questi utenti o gruppi a Harness seguendo le istruzioni in [Assegnare un utente o un gruppo a un'app aziendale.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Suggerimenti importanti per l'assegnazione di utenti a Harness

* È consigliabile assegnare un singolo utente di Azure AD a Harness per testare la configurazione del provisioning automatico degli utenti. Altri utenti o gruppi possono essere assegnati in un secondo momento.

* Quando si assegna un utente a Harness, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo **Assegnazione.** Gli utenti con il ruolo *Accesso predefinito* sono esclusi dal provisioning.

## <a name="set-up-harness-for-provisioning"></a>Configurare Harness per il provisioning

1. Accedere a [Harness Admin Console](https://app.harness.io/#/login), quindi passare a **Gestione accesso con sicurezza** > **Access Management**continua .

    ![Console di amministrazione di Harness](media/harness-provisioning-tutorial/admin.png)

1. Selezionare **Chiavi API**.

    ![Collegamento Chiavi API Harness](media/harness-provisioning-tutorial/apikeys.png)

1. Selezionare **Aggiungi chiave API**. 

    ![Collegamento alla chiave API Aggiungi harness](media/harness-provisioning-tutorial/addkey.png)

1. Nel riquadro Aggiungi chiave API eseguire le operazioni seguenti:In the **Add Api Key** pane, do the following:

    ![Riquadro Aggiungi chiave API harness](media/harness-provisioning-tutorial/title.png)
   
   a. Nella casella **Nome** specificare un nome per la chiave.  
   b. Nell'elenco a discesa **Autorizzazioni ereditate da** selezionare un'opzione. 
   
1. Selezionare **Submit**.

1. Copiare la **chiave** per un uso successivo in questa esercitazione.

    ![Harness Crea Token](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Aggiungi Harness dalla galleria

Prima di configurare Harness per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Harness dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante "Azure Active Directory"](common/select-azuread.png)

1. Selezionare **Applicazioni** > aziendali**Tutte le applicazioni**.

    ![Collegamento "Tutte le applicazioni"](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante "Nuova applicazione"](common/add-new-app.png)

1. Nella casella di ricerca, immettere **Harness**, selezionare **Harness** nell'elenco dei risultati, quindi selezionare il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Harness nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Configurare il provisioning automatico degli utenti in Harness 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti o gruppi in Harness in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Harness seguendo le istruzioni riportate [nell'esercitazione sul single sign-on di Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). È possibile configurare l'accesso Single Sign-On indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di Harness, vedere l'articolo [Chiavi API](https://docs.harness.io/article/smloyragsm-api-keys) harness.

Per configurare il provisioning automatico degli utenti per Harness in Azure AD, eseguire le operazioni seguenti:To configure automatic user provisioning for Harness in Azure AD, do the following:

1. Nel [portale di Azure](https://portal.azure.com)selezionare **Applicazioni** > aziendali**Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Harness**.

    ![Il collegamento Harness nell'elenco delle applicazioni](common/all-applications.png)

1. Selezionare **Provisioning**.

    ![Il pulsante Provisioning](common/provisioning.png)

1. Nell'elenco a discesa **Modalità di** provisioning selezionare **Automatico**.

    ![Elenco a discesa "Modalità di provisioning"](common/provisioning-automatic.png)

1. In **Credenziali amministratore**eseguire le operazioni seguenti:

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Nella casella **URL** tenant **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** immettere .  
   b. Nella casella **Token segreto** immettere il valore del token di autenticazione SCIM salvato nel passaggio 6 della sezione "Impostare Harness per il provisioning".  
   c. Selezionare Test connessione per assicurarsi che Azure AD possa connettersi a **Harness.Select Test Connection** to ensure that Azure AD can connect to Harness. Se la connessione non riesce, verificare che l'account Harness disponga delle autorizzazioni *di amministratore,* quindi riprovare.

1. Nella casella **Posta elettronica notifica** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e quindi selezionare la casella di controllo Invia una notifica di posta elettronica quando si verifica un **errore.**

    ![La casella "E-mail di notifica"](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. In **Mapping**selezionare **Sincronizza utenti di Azure Active Directory per eseguire il cablaggio**.

    ![Collegamento "Sincronizza utenti di Azure Active Directory per Cablaggio"](media/harness-provisioning-tutorial/usermappings.png)

1. In **Mapping attributi**esaminare gli attributi utente sincronizzati da Azure AD a Harness. Gli attributi selezionati come *corrispondenza* vengono utilizzati per abbinare gli account utente in Harness per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Riquadro "Mapping attributi" dell'utente Harness](media/harness-provisioning-tutorial/userattributes.png)

1. In **Mapping**selezionare Sincronizza gruppi di **Azure Active Directory per il cablaggio**.

    ![Collegamento "Sincronizza gruppi di Azure Active Directory per Cablaggio"](media/harness-provisioning-tutorial/groupmappings.png)

1. In **Mapping attributi**esaminare gli attributi di gruppo sincronizzati da Azure AD a Harness. Gli attributi selezionati come proprietà *corrispondenti* vengono utilizzati per abbinare i gruppi in Harness per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Riquadro "Mapping attributi" del gruppo Harness](media/harness-provisioning-tutorial/groupattributes.png)

1. Per configurare i filtri di ambito, vedere Provisioning di [applicazioni basate su attributi con filtri di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. In **Impostazioni**, per abilitare il servizio di provisioning di Azure AD per Harness, impostare l'opzione **Stato provisioning** su **Attivato**.

    ![Opzione Stato provisioning attivata su "Attivato"](common/provisioning-toggle-on.png)

1. In **Impostazioni**, nell'elenco a discesa **Ambito,** selezionare la modalità di sincronizzazione degli utenti o dei gruppi di cui si sta eseguiil provisioning in Harness.

    ![Ambito di provisioning](common/provisioning-scope.png)

1. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Pulsante Salva provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia la sincronizzazione iniziale degli utenti o dei gruppi di cui si sta esegue il provisioning. L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto a quelle successive. Le sincronizzazioni si verificano circa ogni 40 minuti, purché sia in esecuzione il servizio di provisioning di Azure AD. Per monitorare lo stato di avanzamento, passare alla sezione **Dettagli sincronizzazione.** È anche possibile seguire i collegamenti a un report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Harness.You can also follow links to a provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Harness.

Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere Report sul provisioning automatico degli [account utente.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendaliManage user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
