---
title: 'Esercitazione: Configurare Elium per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Elium.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005504"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Esercitazione: Configurare Elium per il provisioning utenti automatico

Questa esercitazione illustra come configurare Elium e Azure Active Directory (Azure AD) per effettuare automaticamente il provisioning e il deprovisioning di utenti o gruppi in Elium.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio Provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e per leggere le risposte a domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per informazioni sulle condizioni generali per l'utilizzo delle funzionalità di Azure in anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che si disponga già dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant di Elium](https://www.elium.com/pricing/)
* Un account utente in Elium con autorizzazioni di amministratore

## <a name="assigning-users-to-elium"></a>Assegnazione di utenti a Elium

Per determinare gli utenti che devono ricevere l'accesso alle app selezionate, Azure AD usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e gruppi in Azure AD devono poter accedere a Elium. Assegnare quindi tali utenti e gruppi a Elium seguendo la procedura descritta in [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Suggerimenti importanti per l'assegnazione di utenti a Elium 

È consigliabile assegnare un singolo utente di Azure AD a Elium per testare la configurazione del provisioning utenti automatico. In un secondo momento si potranno assegnare altri utenti o gruppi.

Quando si assegna un utente a Elium, è necessario selezionare un ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-elium-for-provisioning"></a>Configurare Elium per il provisioning

Prima di configurare Elium per il provisioning per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM (System for Cross-domain Identity Management) in Elium. Seguire questa procedura:

1. Accedere a Elium e passare a **My Profile** > **Settings** (Profilo personale > Impostazioni).

    ![Voce di menu Settings in Elium](media/Elium-provisioning-tutorial/setting.png)

1. Nell'angolo in basso a sinistra, in **ADVANCED** (Avanzate), selezionare **Security** (Sicurezza).

    ![Collegamento Security in Elium](media/Elium-provisioning-tutorial/security.png)

1. Copiare i valori di **Tenant URL** (URL tenant) e **Secret token** (Token segreto). Questi valori dovranno successivamente essere immessi nei campi corrispondenti nella scheda **Provisioning** dell'applicazione Elium nel portale di Azure.

    ![Campi Tenant URL e Secret token in Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Aggiungere Elium dalla raccolta

Per configurare Elium per il provisioning utenti automatico con Azure AD, è necessario aggiungere Elium dalla raccolta di applicazioni di Azure AD all'elenco di applicazioni SaaS (Software-as-a-Service) gestite. Seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Voce di menu Azure Active Directory](common/select-azuread.png)

1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

     ![Pannello Applicazioni aziendali di Azure AD](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore del riquadro.

    ![Collegamento Nuova applicazione](common/add-new-app.png)

1. Nella casella di ricerca digitare **Elium**, selezionare **Elium** nell'elenco risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Casella di ricerca della raccolta](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Configurare il provisioning utenti automatico per Elium

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e gruppi in Elium in base alle assegnazioni di utenti e gruppi in Azure AD.

> [!TIP]
> Si potrebbe anche scegliere di abilitare l'accesso Single Sign-On per Elium basato su SAML (Security Assertion Markup Language) seguendo le istruzioni riportate nell'[esercitazione sul Single Sign-On per Elium](Elium-tutorial.md). Il Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

Per configurare il provisioning utenti automatico per Elium in Azure AD, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com), selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali di Azure AD](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Elium**.

    ![Elenco delle applicazioni nel pannello Applicazioni aziendali](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning nel pannello Applicazioni aziendali](common/provisioning.png)

1. Impostare **Modalità di provisioning** su **Automatico**.

    ![Impostazione Automatico per Modalità di provisioning](common/provisioning-automatic.png)

1. Nella sezione **Credenziali amministratore** digitare **\<tenantURL\>/scim/v2** nel campo **URL tenant**. Il valore di **tenantURL** è il valore recuperato in precedenza dalla console di amministrazione di Elium. Digitare anche il valore di **Secret token** di Elium nel campo **Token segreto**. Infine, selezionare **Test connessione** per verificare che Azure AD possa connettersi a Elium. Se la connessione non riesce, verificare che l'account Elium abbia autorizzazioni di amministratore e riprovare.

    ![Campi URL tenant e Token segreto in Credenziali amministratore](common/provisioning-testconnection-tenanturltoken.png)

1. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche degli errori di provisioning. Quindi, selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

1. Fare clic su **Salva**.

1. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Elium** (Sincronizza utenti di Azure Active Directory in Elium).

    ![Collegamento Sincronizza per il mapping degli utenti di Azure AD a Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Esaminare gli attributi utente sincronizzati da Azure AD a Elium nella sezione **Mapping di attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Elium per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Mapping di attributi tra Azure AD ed Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning di Azure AD per Elium, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Opzione Stato del provisioning abilitata](common/provisioning-toggle-on.png)

1. Definire gli utenti e i gruppi di cui si vuole effettuare il provisioning in Elium selezionando i valori desiderati nell'elenco a discesa **Ambito** della sezione **Impostazioni**.

    ![Elenco a discesa Ambito per il provisioning](common/provisioning-scope.png)

1. Quando si è pronti per eseguire il provisioning, selezionare **Salva**.

    ![Pulsante Salva per la configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il processo di sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Per altre informazioni sul tempo necessario per il provisioning, vedere [Quanto tempo sarà necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Usare la sezione **Stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning. Questo report descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Elium. Per altre informazioni, vedere [Controllare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
