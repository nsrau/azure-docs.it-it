---
title: 'Esercitazione: Configurare Visitly per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Visitly.Learn how to configure Azure Active Directory to automatically provision and deprovision user accounts to Visitly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063165"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Esercitazione: Configurare Visitly per il provisioning automatico degli utentiTutorial: Configure Visitly for automatic user provisioning

The objective of this tutorial is to demonstrate the steps you perform in Visitly and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and deprovision users or groups to Visitly.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning degli utenti di Azure AD. Per informazioni importanti sulle operazioni di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni software as-a-service (SaaS) con Azure Active Directory.](../app-provisioning/user-provisioning.md)
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni generali per l'utilizzo delle funzionalità di anteprima di Microsoft Azure, vedere Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure.For more information on the general Microsoft Azure terms of use for preview features, see [Supplemental terms of use for Microsoft Azure previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Visitly](https://www.visitly.io/pricing/)
* Un account utente in Visitly con autorizzazioni di amministratore

## <a name="assign-users-to-visitly"></a>Assegnare gli utenti a Visitly 

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, decidere quali utenti o gruppi in Azure AD devono accedere a Visitly.Before you configure and enable automatic user provisioning, decide which users or groups in Azure AD need access to Visitly. Quindi assegna questi utenti o gruppi a Visitly seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Suggerimenti importanti per l'assegnazione di utenti a Visitly 

* È consigliabile assegnare un singolo utente di Azure AD a Visitly per testare la configurazione del provisioning automatico degli utenti. Altri utenti o gruppi possono essere assegnati in un secondo momento.

* Quando si assegna un utente a Visitly, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo Accesso predefinito sono esclusi dal provisioning.

## <a name="set-up-visitly-for-provisioning"></a>Configurare Visitly per il provisioning

Prima di configurare Visitly per il provisioning automatico degli utenti con Azure AD, è necessario abilitare System for Cross-domain Identity Management (SCIM) provisioning su Visitly.

1. Accedi a [Visitly](https://app.visitly.io/login). Selezionare **Integrations** > **Host Synchronization**.

    ![Sincronizzazione host](media/Visitly-provisioning-tutorial/login.png)

2. Selezionare la sezione **Azure AD.**

    ![Sezione Azure ADAzure AD section](media/Visitly-provisioning-tutorial/integration.png)

3. Copiare la **chiave API**. Questi valori vengono immessi nella casella **Token segreto** nella scheda Provisioning dell'applicazione Visitly nel portale di Azure.These values are entered in the Secret Token box on the **Provisioning** tab of your Visitly application in the Azure portal.

    ![API key](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Aggiungi Visitly dalla galleria

Per configurare Visitly per il provisioning automatico degli utenti con Azure AD, aggiungere Visitly dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

Per aggiungere Visitly dalla raccolta di applicazioni di Azure AD, seguire questi passaggi.

1. Nel [portale di Azure](https://portal.azure.com)selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Visitly**, selezionare **Visitly** nel pannello dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Visitly nell'elenco risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Configurare il provisioning automatico degli utenti su Visitly 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti o gruppi in Visitly in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> Per abilitare l'accesso Single Sign-On basato su SAML per Visitly, seguire le istruzioni [nell'esercitazione Visitly Single Sign-On](Visitly-tutorial.md). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Configurare il provisioning automatico degli utenti per Visitly in Azure ADConfigure automatic user provisioning for Visitly in Azure AD

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni** > aziendali**Tutte le applicazioni**.

    ![Tutte le applicazioni](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Visitly**.

    ![Collegamento di Visitly nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning impostata su Automatico](common/provisioning-automatic.png)

5. Nella `https://api.visitly.io/v1/usersync/SCIM` sezione Credenziali amministratore immettere i valori della chiave **API** e recuperati in precedenza rispettivamente in URL **tenant** e token **segreto.** Selezionare Test connessione per assicurarsi che Azure AD possa connettersi a Visitly.Select **Test Connection** to ensure that Azure AD can connect to Visitly. Se la connessione non riesce, assicurati che l'account Visitly disponga delle autorizzazioni di amministratore e riprova.

    ![URL tenant : token](common/provisioning-testconnection-tenanturltoken.png)

6. Nella casella **Email di notifica** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning. Selezionare la casella di controllo **Invia una notifica tramite posta elettronica quando si verifica un errore.**

    ![E-mail di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con Visitly**.

    ![Mapping degli utenti visitly](media/visitly-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Visitly nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Visitly per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente visitly](media/visitly-provisioning-tutorial/userattribute.png)

10. Per configurare i filtri di ambito, seguire le istruzioni [nell'esercitazione](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)sui filtri di ambito .

11. Per abilitare il servizio di provisioning di Azure AD per Visitly, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for Visitly, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato provisioning attivato/attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti o i gruppi di cui si desidera eseguire il provisioning in Visitly scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salvataggio della configurazione di provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning di utenti o gruppi, vedere [Quanto tempo è necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione Stato corrente per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Visitly.You can use the **Current Status** section to monitor progress and follow links to your provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Visitly. Per ulteriori informazioni, consultate [Controllare lo stato del provisioning degli utenti.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Per leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utente.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitazioni dei connettori

Visitly non supporta le eliminazioni forzate. Tutto è solo soft delete.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendaliManage user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
