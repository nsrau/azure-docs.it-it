---
title: 'Esercitazione: Configurare Foodee per il provisioning utenti automatico tramite Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Foodee.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 8b4bfa7e9bf457d79c6c4a0b5255bce4fe36dff4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358678"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Esercitazione: Configurare Foodee per il provisioning utenti automatico

Questo articolo illustra come configurare Azure Active Directory (Azure AD) in Foodee e Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti o gruppi in Foodee.

> [!NOTE]
> L'articolo descrive un connettore basato sul servizio Provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio e per risposte a domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulla funzionalità Condizioni per l'utilizzo di Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che siano soddisfatti i prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Foodee](https://www.food.ee/about/)
* Un account utente in Foodee con autorizzazioni di amministratore

## <a name="assign-users-to-foodee"></a>Assegnare utenti a Foodee 

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure AD usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti o gruppi in Azure AD devono poter accedere a Foodee. Dopo averlo stabilito, è possibile assegnare questi utenti o gruppi a Foodee seguendo le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Suggerimenti importanti per l'assegnazione di utenti a Foodee 

Per l'assegnazione degli utenti, tenere presenti i suggerimenti seguenti:

* È consigliabile assegnare un singolo utente di Azure AD a Foodee per testare la configurazione del provisioning utenti automatico. Sarà possibile assegnare altri utenti o gruppi in un secondo momento.

* Per assegnare un utente a Foodee, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nel riquadro **Assegnazione**. Gli utenti con il ruolo *Accesso predefinito* sono esclusi dal provisioning.

## <a name="set-up-foodee-for-provisioning"></a>Configurare Foodee per il provisioning

Prima di configurare Foodee per il provisioning utenti automatico tramite Azure AD, è necessario abilitare il provisioning SCIM (System for Cross-domain Identity Management) in Foodee.

1. Accedere a [Foodee](https://www.food.ee/login/) e selezionare l'ID del tenant.

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Screenshot del menu principale del portale aziendale di Foodee. Nel menu è visibile un segnaposto di ID tenant." border="false":::

1. In **Enterprise Portal** (Portale aziendale) selezionare **Single Sign On**.

    ![Menu del riquadro sinistro del portale aziendale di Foodee](media/Foodee-provisioning-tutorial/scim.png)

1. Copiare il valore della casella **API Token** (Token API), che dovrà essere usato più avanti. Dovrà essere immesso nella casella **Token segreto** nella scheda **Provisioning** dell'applicazione Foodee nel portale di Azure.

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Screenshot di una pagina del portale aziendale di Foodee. È evidenziato un valore di token API." border="false":::

## <a name="add-foodee-from-the-gallery"></a>Aggiungere Foodee dalla raccolta

Per configurare Foodee per il provisioning utenti automatico tramite Azure AD, è necessario aggiungere Foodee dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Foodee dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Comando Azure Active Directory](common/select-azuread.png)

1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

1. Nella casella di ricerca immettere **Foodee**, selezionare **Foodee** nel riquadro dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Foodee nell'elenco risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Configurare il provisioning utenti automatico in Foodee 

In questa sezione si configura il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti o gruppi in Foodee in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare l'accesso Single Sign-On basato su SAML per Foodee seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per Foodee](Foodee-tutorial.md). Il Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

Per configurare il provisioning utenti automatico per Foodee in Azure AD, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco **Applicazioni** selezionare **Foodee**.

    ![Collegamento di Foodee nell'elenco delle applicazioni](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

1. Nell'elenco a discesa **Modalità di provisioning** selezionare **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

1. In **Credenziali amministratore** eseguire questa procedura:

   a. Nella casella **URL tenant** immettere il valore **https:\//concierge.food.ee/scim/v2** recuperato in precedenza.

   b. Nella casella **Token segreto** immettere il valore di **Token API** recuperato in precedenza.
   
   c. Per verificare che Azure AD possa connettersi a Foodee, selezionare **Test connessione**. Se la connessione non riesce, verificare che l'account Foodee abbia autorizzazioni di amministratore, quindi riprovare.

    ![Collegamento Test connessione](common/provisioning-testconnection-tenanturltoken.png)

1. Nella casella **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche relative agli errori di provisioning, quindi selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Casella di testo Indirizzo di posta elettronica per le notifiche](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. In **Mapping** selezionare **Synchronize Azure Active Directory Users to Foodee** (Sincronizza utenti di Azure Active Directory in Foodee).

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="Screenshot della sezione Mapping. In Nome è evidenziata l'opzione Synchronize Azure Active Directory Users to Foodee (Sincronizza utenti di Azure Active Directory in Foodee)." border="false":::

1. In **Mapping attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Foodee. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli *account utente* in Foodee per le operazioni di aggiornamento. 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="Screenshot della pagina Mapping attributi. In una tabella sono elencati gli attributi di Azure Active Directory e di Foodee e la precedenza abbinamento." border="false":::

1. Per eseguire il commit delle modifiche, selezionare **Salva**.
1. In **Mapping** selezionare **Synchronize Azure Active Directory Groups to Foodee** (Sincronizza gruppi di Azure Active Directory in Foodee).

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="Screenshot della sezione Mapping. In Nome è evidenziata l'opzione Synchronize Azure Active Directory Groups to Foodee (Sincronizza gruppi di Azure Active Directory in Foodee)." border="false":::

1. In **Mapping attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Foodee. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli *account di gruppo* in Foodee per le operazioni di aggiornamento.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="Screenshot della pagina Mapping attributi. In una tabella sono elencati gli attributi di Azure Active Directory, gli attributi di Foodee e la precedenza abbinamento." border="false":::

1. Per eseguire il commit delle modifiche, selezionare **Salva**.
1. Configurare i filtri di ambito. Per istruzioni, vedere l'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning di Azure AD per Foodee, nella sezione **Impostazioni** impostare **Stato del provisioning** su **Sì**.

    ![Opzione Stato del provisioning](common/provisioning-toggle-on.png)

1. In **Impostazioni** definire gli utenti o i gruppi di cui si vuole effettuare il provisioning in Foodee nell'elenco a discesa **Ambito**.

    ![Elenco a discesa Ambito per il provisioning](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Pulsante Salva della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia la sincronizzazione iniziale degli utenti o dei gruppi definiti nell'elenco a discesa **Ambito**. La sincronizzazione iniziale richiede più tempo rispetto a quelle successive. Per altre informazioni, vedere [Quanto tempo sarà necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **Stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Foodee. Per altre informazioni, vedere [Controllare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
