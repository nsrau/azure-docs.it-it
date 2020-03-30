---
title: 'Esercitazione: Configurare il gateway di premi per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Reward Gateway.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Reward Gateway.
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
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 928d48907e43de5e65ca5604ff878bfb83d5e95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060997"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Esercitazione: Configurare il gateway di premi per il provisioning automatico degli utentiTutorial: Configure Reward Gateway for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Reward Gateway e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Reward Gateway.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Questo connettore è attualmente in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* Un [tenant Reward Gateway](https://www.rewardgateway.com/).
* Un account utente in Gateway ricompense con autorizzazioni di amministratore.

## <a name="assigning-users-to-reward-gateway"></a>Assegnazione di utenti a Reward Gateway 

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Reward Gateway. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Reward Gateway seguendo le istruzioni in [Assegnare un utente o un gruppo a un'app aziendale.](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Suggerimenti importanti per l'assegnazione di utenti a Reward Gateway 

* È consigliabile assegnare un singolo utente di Azure AD al gateway di premi per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente al gateway di premi, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-reward-gateway--for-provisioning"></a>Configurazione del gateway di ricompensa per il provisioning
Prima di configurare Reward Gateway per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM in Reward Gateway.

1. Accedi a [Reward Gateway Admin Console](https://rewardgateway.photoshelter.com/login/). Fare clic su **Integrations**.

    ![Console di amministrazione del gateway premiazione](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Selezionare **Integrazione personale**.

    ![Console di amministrazione del gateway premiazione](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Copiare i valori di **URL SCIM (v2)** e **OAuth Bearer Token**. Questi valori verranno immessi nel campo URL tenant e token segreto nella scheda Provisioning dell'applicazione Gateway ricompense nel portale di Azure.These values will be entered in the Tenant URL and Secret Token field in the Provisioning tab of your Reward Gateway application in the Azure portal.

    ![Console di amministrazione del gateway premiazione](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Aggiungere Reward Gateway dalla raccolta

Per configurare Reward Gateway per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Reward Gateway dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere il gateway di premi dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Reward Gateway from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Gateway ricompense**, selezionare **Gateway ricompense** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Reward Gateway nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Configurazione del provisioning automatico degli utenti in Reward Gateway  

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi nel gateway di premi in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Il gateway reward, seguendo le istruzioni fornite [nell'esercitazione single sign-on](reward-gateway-tutorial.md)di Reward Gateway . L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per il gateway di ricompensa in Azure AD:To configure automatic user provisioning for Reward Gateway in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Reward Gateway**.

    ![Collegamento di Reward Gateway nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere i valori **URL SCIM (v2)** e **OAuth Bearer** recuperati in precedenza rispettivamente in **URL tenant** e **token segreto.** Fare clic su **Test connessione** per verificare che Azure AD possa connettersi al gateway di premi. Se la connessione non riesce, verificare che l'account del gateway di premi disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Gateway premi.**

    ![Console di amministrazione del gateway premiazione](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Reward Gateway nella sezione **Attribute-Mapping.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente nel gateway ricompense per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Console di amministrazione del gateway premiazione](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per il gateway premio, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Reward Gateway, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Reward Gateway scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD nel gateway di premi.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

Reward Gateway non supporta attualmente il provisioning dei gruppi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
