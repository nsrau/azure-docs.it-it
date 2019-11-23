---
title: 'Esercitazione: configurare Elium per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Elium.
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
ms.openlocfilehash: a4ddcf27869ea7484f98329d14d01bfad83af219
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709528"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Esercitazione: configurare Elium per il provisioning utenti automatico

Questa esercitazione illustra come configurare Elium e Azure Active Directory (Azure AD) per effettuare automaticamente il provisioning e il deprovisioning di utenti o gruppi in Elium.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning utenti Azure AD. Per informazioni importanti sul funzionamento e sul funzionamento di questo servizio e sulle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per le condizioni per l'utilizzo generali per le funzionalità di Azure in anteprima, vedere le [condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

In questa esercitazione si presuppone che siano già stati soddisfatti i prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di Elium](https://www.elium.com/pricing/)
* Un account utente in Elium, con autorizzazioni di amministratore

## <a name="assigning-users-to-elium"></a>Assegnazione di utenti a Elium

Azure AD usa un concetto denominato *assegnazioni* per determinare gli utenti che ricevono l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti e gruppi in Azure AD necessario accedere a Elium. Assegnare quindi tali utenti e gruppi a Elium seguendo i passaggi descritti in [assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Suggerimenti importanti per l'assegnazione di utenti a Elium 

È consigliabile assegnare un singolo utente Azure AD a Elium per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare più utenti e gruppi in un secondo momento.

Quando si assegna un utente a Elium, è necessario selezionare un ruolo valido specifico dell'applicazione, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti che hanno il ruolo di **accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-elium-for-provisioning"></a>Configurare Elium per il provisioning

Prima di configurare Elium per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning di sistema per la gestione delle identità tra domini (SCIM) su Elium. Seguire questa procedura:

1. Accedere a Elium e passare a **profilo personale** > **Impostazioni**.

    ![Voce di menu impostazioni in Elium](media/Elium-provisioning-tutorial/setting.png)

1. Nell'angolo in basso a sinistra, in **Avanzate**, selezionare **sicurezza**.

    ![Collegamento di sicurezza in Elium](media/Elium-provisioning-tutorial/security.png)

1. Copiare l' **URL del tenant** e i valori dei **token segreti** . Questi valori verranno usati in un secondo momento, nei campi corrispondenti nella scheda **provisioning** dell'applicazione Elium nel portale di Azure.

    ![Campi URL tenant e token segreto in Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Aggiungere Elium dalla raccolta

Per configurare Elium per il provisioning utenti automatico con Azure AD, è anche necessario aggiungere Elium dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS (software-as-a-Service) gestite. Seguire questa procedura:

1. Nel riquadro di spostamento a sinistra del [portale di Azure](https://portal.azure.com)selezionare **Azure Active Directory**.

    ![Voce di menu Azure Active Directory](common/select-azuread.png)

1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

     ![Pannello applicazioni aziendali Azure AD](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **nuova applicazione** nella parte superiore del riquadro.

    ![Collegamento nuova applicazione](common/add-new-app.png)

1. Nella casella di ricerca digitare **Elium**, selezionare **Elium** nell'elenco dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Casella di ricerca della raccolta](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Configurare il provisioning utenti automatico in Elium

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e gruppi in Elium, in base alle assegnazioni di utenti e gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare Single Sign-On per Elium in base a Security Assertion Markup Language (SAML) seguendo le istruzioni riportate nell' [esercitazione Single Sign-on di Elium](Elium-tutorial.md). È possibile configurare Single Sign-On indipendentemente dal provisioning utenti automatico, sebbene le due funzionalità siano complementari.

Per configurare il provisioning utenti automatico per Elium in Azure AD, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com), selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello applicazioni aziendali Azure AD](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **Elium**.

    ![Elenco delle applicazioni nel pannello applicazioni aziendali](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Scheda provisioning nel pannello applicazioni aziendali](common/provisioning.png)

1. Impostare **Modalità di provisioning** su **Automatico**.

    ![Impostazione automatica per la modalità di provisioning](common/provisioning-automatic.png)

1. Nella sezione **credenziali amministratore** Digitare **\<urltenant\>/scim/V2** nel campo **URL tenant** . ( **Urltenant** è il valore recuperato in precedenza dalla console di amministrazione di Elium). Digitare anche il valore del **token segreto** Elium nel campo **token segreto** . Infine, selezionare **Test connessione** per verificare che Azure ad possa connettersi a Elium. Se la connessione non riesce, verificare che l'account Elium disponga delle autorizzazioni di amministratore e riprovare.

    ![Campi URL tenant e token segreto nelle credenziali di amministratore](common/provisioning-testconnection-tenanturltoken.png)

1. Nel campo **messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare quindi la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

1. Fare clic su **Salva**.

1. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Elium**.

    ![Sincronizzare il collegamento per il mapping di utenti Azure AD a Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Elium nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Elium per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Mapping degli attributi tra Azure AD e Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'esercitazione relativa al [filtro di ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning Azure AD per Elium, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning impostato su on](common/provisioning-toggle-on.png)

1. Definire gli utenti e i gruppi di cui si desidera eseguire il provisioning in Elium selezionando i valori desiderati nella casella di riepilogo a discesa **ambito** nella sezione **Impostazioni** .

    ![Casella di riepilogo ambito provisioning](common/provisioning-scope.png)

1. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Pulsante Salva per la configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti e i gruppi definiti nell' **ambito** nella sezione **Impostazioni** . Questo processo di sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning, vedere [quanto tempo sarà necessario per eseguire il provisioning degli utenti?](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Usare la sezione **stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning. Il report delle attività di provisioning descrive tutte le azioni eseguite dal servizio di provisioning Azure AD in Elium. Per altre informazioni, vedere [controllare lo stato del provisioning dell'utente](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
