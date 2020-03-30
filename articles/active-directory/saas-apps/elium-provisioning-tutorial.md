---
title: 'Esercitazione: Configurare Elium per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Elium.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Elium.
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
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058465"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Esercitazione: Configurare Elium per il provisioning automatico degli utentiTutorial: Configure Elium for automatic user provisioning

Questa esercitazione illustra come configurare Elium e Azure Active Directory (Azure AD) per eseguire automaticamente il provisioning e il deprovisioning di utenti o gruppi in Elium.This tutorial shows how to configure Elium and Azure Active Directory (Azure AD) to automatically provision and de-provision users or groups to Elium.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio Provisioning utenti di Azure AD. Per informazioni importanti sulle operazioni eseguite da questo servizio e sul suo funzionamento e per le domande frequenti, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni SaaS con Azure Active Directory.](../app-provisioning/user-provisioning.md)
>
> Questo connettore è attualmente disponibile in anteprima. Per le condizioni generali per l'utilizzo delle funzionalità di Azure in anteprima, vedere [Condizioni di utilizzo supplementari per le anteprime di Microsoft Azure.For](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)the general terms of use for Azure features in preview, see Supplemental Terms of Use for Microsoft Azure Previews.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si presuppone che siano già presenti i prerequisiti seguenti:This tutorial assumes that you already have the following prerequisites:

* Un tenant di Azure AD
* [Un inquilino di Elium](https://www.elium.com/pricing/)
* Un account utente in Elium, con autorizzazioni di amministratore

## <a name="assigning-users-to-elium"></a>Assegnazione di utenti a Elium

Azure AD usa un concetto denominato *assegnazioni* per determinare quali utenti ricevono l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, decidere quali utenti e gruppi in Azure AD devono accedere a Elium. Assegnare quindi tali utenti e gruppi a Elium seguendo la procedura descritta in [Assegnare un utente o un gruppo a un'app aziendale.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-elium"></a>Suggerimenti importanti per l'assegnazione degli utenti a Elium 

È consigliabile assegnare un singolo utente di Azure AD a Elium per testare la configurazione del provisioning automatico degli utenti. Altri utenti e gruppi possono essere assegnati in un secondo momento.

Quando si assegna un utente a Elium, è necessario selezionare un ruolo valido specifico dell'applicazione (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti che dispongono del ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-elium-for-provisioning"></a>Configurare Elium per il provisioning

Prima di configurare Elium per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il sistema per il provisioning SCIM (Cross-domain Identity Management) in Elium. A tale scopo, seguire questa procedura:

1. Accedi a Elium e vai a**Impostazioni** **profilo** > personale .

    ![Voce di menu Impostazioni in Elium](media/Elium-provisioning-tutorial/setting.png)

1. Nell'angolo inferiore sinistro, in **ADVANCED**, selezionare **Sicurezza**.

    ![Collegamento di sicurezza in Elium](media/Elium-provisioning-tutorial/security.png)

1. Copiare i valori **dell'URL tenant** e del **token segreto.** Questi valori verranno utilizzati in un secondo momento, nei campi corrispondenti nella scheda Provisioning dell'applicazione Elium nel portale di Azure.You'll use these values later, in corresponding fields in the **Provisioning** tab of your Elium application in the Azure portal.

    ![Campi URL tenant e token segreto in Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Aggiungi Elium dalla galleria

Per configurare Elium per il provisioning automatico degli utenti con Azure AD, è inoltre necessario aggiungere Elium dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni saaS (software-as-a-service) gestite. A tale scopo, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com)selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Voce di menu di Azure Active DirectoryAzure Active Directory menu item](common/select-azuread.png)

1. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

     ![Pannello Applicazioni aziendali di Azure ADAzure AD](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore del riquadro.

    ![Nuovo collegamento all'applicazione](common/add-new-app.png)

1. Nella casella di ricerca digitare **Elium**, selezionare **Elium** nell'elenco dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Casella di ricerca raccolta](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Configurare il provisioning automatico degli utenti in Elium

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e gruppi in Elium, in base alle assegnazioni di utenti e gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On per Elium basato su SAML (Security Assertion Markup Language) seguendo le istruzioni riportate [nell'esercitazione sull'accesso Single Sign-On eLium](Elium-tutorial.md). È possibile configurare l'accesso Single Sign-On indipendentemente dal provisioning automatico degli utenti, anche se le due funzionalità si completano a vicenda.

Per configurare il provisioning automatico degli utenti per Elium in Azure AD, attenersi alla seguente procedura:

1. Accedere al [portale](https://portal.azure.com)di Azure , selezionare **Applicazioni aziendali**e quindi **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali di Azure ADAzure AD](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Elium**.

    ![Elenco applicazioni nel pannello Applicazioni aziendali](common/all-applications.png)

1. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning nel pannello Applicazioni aziendali](common/provisioning.png)

1. Impostare la **modalità di provisioning** su **Automatico**.

    ![Impostazione automatica per la modalità di provisioning](common/provisioning-automatic.png)

1. Nella sezione **Credenziali amministratore** digitare ** \<\>tenantURL /scim/v2** nel campo URL **tenant.** (Il **tenantURL** è il valore recuperato in precedenza dalla console di amministrazione di Elium.) Digitare anche il valore del token Elium **Secret** nel campo **Token segreto.** Infine, selezionare Test connessione per verificare che Azure AD possa connettersi a Elium.Finally, select **Test Connection** to verify that Azure AD can connect to Elium. Se la connessione non riesce, assicurati che l'account Elium disponga delle autorizzazioni di amministratore e riprova.

    ![Campi URL tenant e Token segreto in Credenziali amministratore](common/provisioning-testconnection-tenanturltoken.png)

1. Nel campo Email di **notifica** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche di errore di provisioning. Selezionare quindi la casella di controllo Invia una **notifica tramite posta elettronica quando si verifica un errore.**

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

1. Fare clic su **Salva**.

1. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Elium**.

    ![Collegamento Sincronizza per il mapping degli utenti di Azure AD a Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Esaminare gli attributi utente sincronizzati da Azure AD a Elium nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Elium per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Mapping degli attributi tra Azure AD ed EliumAttribute mappings between Azure AD and Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Per configurare i filtri di ambito, seguire le istruzioni [nell'esercitazione](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)sui filtri di ambito .

1. Per abilitare il servizio di provisioning di Azure AD per Elium, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Elium, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato provisioning impostato su Attivato](common/provisioning-toggle-on.png)

1. Definire gli utenti e i gruppi di cui si desidera eseguire il provisioning in Elium selezionando i valori desiderati nella casella di riepilogo a discesa **Ambito** nella sezione **Impostazioni.**

    ![Casella di riepilogo Ambito provisioning](common/provisioning-scope.png)

1. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Pulsante Salva per la configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni.** Questo processo di sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning, vedere [Quanto tempo è necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Utilizzare la sezione **Stato corrente** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning. The provisioning activity report describes all actions performed by the Azure AD provisioning service on Elium. Per ulteriori informazioni, consultate [Controllare lo stato del provisioning degli utenti.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Per leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utente.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
