---
title: "Esercitazione: configurare l'harness per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs"
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente per l'harness.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 56a865de8cb1be079f4935ef2a8f840f10589b26
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550011"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Esercitazione: configurare l'harness per il provisioning utenti automatico

Questo articolo illustra come configurare Azure Active Directory (Azure AD) per effettuare automaticamente il provisioning e il deprovisioning di utenti o gruppi per l'harness.

> [!NOTE]
> Questo articolo descrive un connettore basato sul servizio di provisioning utenti Azure AD. Per informazioni importanti su questo servizio e sulle risposte alle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questo articolo presuppone che siano già stati soddisfatti i prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant harness](https://harness.io/pricing/)
* Un account utente in Harness con autorizzazioni di *amministratore*

## <a name="assign-users-to-harness"></a>Assegnazione di utenti a harness

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti o gruppi in Azure AD necessario accedere a harness. È quindi possibile assegnare tali utenti o gruppi a harness seguendo le istruzioni riportate in [assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Suggerimenti importanti per l'assegnazione di utenti al cablaggio

* È consigliabile assegnare un singolo utente Azure AD per eseguire il test della configurazione del provisioning utenti automatico. È possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a harness, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di **assegnazione** . Gli utenti con il ruolo *Accesso predefinito* vengono esclusi dal provisioning.

## <a name="set-up-harness-for-provisioning"></a>Configurare l'harness per il provisioning

1. Accedere alla console di [amministrazione di harness](https://app.harness.io/#/login), quindi passare a **Continuous Security**  >  **Access Management**.

    ![Console di amministrazione harness](media/harness-provisioning-tutorial/admin.png)

1. Selezionare **chiavi API**.

    ![Collegamento chiavi API harness](media/harness-provisioning-tutorial/apikeys.png)

1. Selezionare **Aggiungi chiave API**. 

    ![Collegamento harness Aggiungi chiave API](media/harness-provisioning-tutorial/addkey.png)

1. Nel riquadro **Aggiungi chiave API** eseguire le operazioni seguenti:

    ![Riquadro Aggiungi chiave API](media/harness-provisioning-tutorial/title.png)
   
   a. Nella casella **nome** specificare un nome per la chiave.  
   b. Selezionare un'opzione nell'elenco a discesa **autorizzazioni ereditate da** . 
   
1. Selezionare **Submit** (Invia).

1. Copiare la **chiave** per usarla in seguito in questa esercitazione.

    ![Crea token di harness](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Aggiungere harness dalla raccolta

Prima di configurare l'harness per il provisioning utenti automatico con Azure AD, è necessario aggiungere harness dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

1. Nel riquadro sinistro della [portale di Azure](https://portal.azure.com)selezionare **Azure Active Directory**.

    ![Pulsante "Azure Active Directory"](common/select-azuread.png)

1. Selezionare **applicazioni aziendali**  >  **tutte le applicazioni**.

    ![Collegamento "Tutte le applicazioni"](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante "Nuova applicazione"](common/add-new-app.png)

1. Nella casella di ricerca immettere **harness**, selezionare **harness** nell'elenco dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Harness nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Configurare il provisioning utenti automatico per il cablaggio 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti o gruppi in harness in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per harness seguendo le istruzioni riportate nell' [esercitazione Harness Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). È possibile configurare Single Sign-On indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di harness, vedere l'articolo sulle [chiavi dell'API](https://docs.harness.io/article/smloyragsm-api-keys) harness.

Per configurare il provisioning utenti automatico per harness in Azure AD, procedere come segue:

1. Nella [portale di Azure](https://portal.azure.com)selezionare **applicazioni aziendali**  >  **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Harness**.

    ![Collegamento harness nell'elenco delle applicazioni](common/all-applications.png)

1. Selezionare **provisioning**.

    ![Pulsante di provisioning](common/provisioning.png)

1. Nell'elenco a discesa **modalità di provisioning** selezionare **automatico**.

    ![Elenco a discesa "modalità di provisioning"](common/provisioning-automatic.png)

1. In **credenziali amministratore**eseguire le operazioni seguenti:

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Nella casella **URL tenant** immettere **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** .  
   b. Nella casella **token segreto** immettere il valore del token di autenticazione scim salvato nel passaggio 6 della sezione "configurare l'harness per il provisioning".  
   c. Selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi al cablaggio. Se la connessione non riesce, verificare che l'account harness disponga di autorizzazioni di *amministratore* , quindi riprovare.

1. Nella casella **posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning, quindi selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Casella di messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. In **mapping**selezionare **Sincronizza Azure Active Directory utenti per l'harness**.

    ![Collegamento "Sincronizza utenti Azure Active Directory al cablaggio"](media/harness-provisioning-tutorial/usermappings.png)

1. In **mapping degli attributi**esaminare gli attributi utente che vengono sincronizzati da Azure ad a harness. Gli attributi selezionati come *corrispondenti* vengono usati per trovare le corrispondenze con gli account utente in harness per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Riquadro "mapping degli attributi" dell'utente harness](media/harness-provisioning-tutorial/userattributes.png)

1. In **mapping**selezionare **Sincronizza Azure Active Directory gruppi da sfruttare**.

    ![Collegamento "Sincronizza i gruppi di Azure Active Directory al cablaggio"](media/harness-provisioning-tutorial/groupmappings.png)

1. In **mapping degli attributi**esaminare gli attributi di gruppo sincronizzati da Azure ad a harness. Gli attributi selezionati come proprietà *corrispondenti* vengono usati per trovare le corrispondenze con i gruppi in harness per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Riquadro "mapping degli attributi" del gruppo di harness](media/harness-provisioning-tutorial/groupattributes.png)

1. Per configurare i filtri di ambito, vedere [provisioning di applicazioni basate su attributi con filtri di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. In **Impostazioni**, per abilitare il servizio di provisioning Azure ad per harness, impostare lo **stato del provisioning** **su**attivato.

    ![Opzione stato provisioning impostata su "attivato"](common/provisioning-toggle-on.png)

1. In **Impostazioni**, nell'elenco a discesa **ambito** , selezionare come si desidera sincronizzare gli utenti o i gruppi di cui si sta eseguendo il provisioning.

    ![Ambito di provisioning](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Pulsante Salva del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia la sincronizzazione iniziale degli utenti o dei gruppi di cui si sta eseguendo il provisioning. La sincronizzazione iniziale richiede più tempo rispetto a quelle successive. Le sincronizzazioni si verificano approssimativamente ogni 40 minuti, purché il servizio di provisioning Azure AD sia in esecuzione. Per monitorare lo stato di avanzamento, passare alla sezione **Dettagli sincronizzazione** . È anche possibile seguire i collegamenti a un report dell'attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning Azure AD in harness.

Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere creare [report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
