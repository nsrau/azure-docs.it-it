---
title: 'Esercitazione: Configurare Elium per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
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
ms.openlocfilehash: 30f6f81234ce34e40a29a04f6b795aab991a8824
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802862"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Esercitazione: Configurare Elium per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in Elium e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Elium.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di Elium](https://www.elium.com/pricing/)
* Un account utente in Elium con autorizzazioni di amministratore.

## <a name="assigning-users-to-elium"></a>Assegnazione di utenti a Elium

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Elium. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Elium seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-elium"></a>Suggerimenti importanti per l'assegnazione di utenti a Elium 

* È consigliabile assegnare un singolo Azure AD utente a Elium per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Elium, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-elium-for-provisioning"></a>Configurare Elium per il provisioning

Prima di configurare Elium per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in Elium.

1. Accedere a Elium. Passare a**Impostazioni** **profilo** > personale.

    ![Elium](media/Elium-provisioning-tutorial/setting.png)

2. Nell'angolo in basso a sinistra in ADVANCED selezionare **sicurezza**.

    ![Elium](media/Elium-provisioning-tutorial/security.png)

3. Copiare il **token Secret**. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione Elium nel portale di Azure.

    ![Elium](media/Elium-provisioning-tutorial/token.png)


## <a name="add-elium--from-the-gallery"></a>Aggiungere Elium dalla raccolta

Per configurare Elium per il provisioning utenti automatico con Azure AD, è necessario aggiungere Elium dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Elium dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Elium**, selezionare **Elium** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Elium nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-elium"></a>Configurazione del provisioning utenti automatico in Elium  

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Elium in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per Elium, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di Elium](Elium-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari

### <a name="to-configure-automatic-user-provisioning-for-elium--in-azure-ad"></a>Per configurare il provisioning utenti automatico per Elium in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Elium**.

    ![Collegamento di Elium nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione credenziali amministratore immettere `<tenantURL>/scim/v2` dove **{TeanantURL}** corrisponde al valore recuperato in precedenza dalla console di amministrazione di Elium. Immettere il valore del **token segreto** in **token segreto** fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a Elium. Se la connessione non riesce, assicurarsi che Elium disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Elium**.

    ![Mapping utente Elium](media/Elium-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Elium nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Elium per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Elium](media/Elium-provisioning-tutorial/userattribute.png)


11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning Azure AD per Elium, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Elium selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning di utenti e/o gruppi, vedere [quanto tempo sarà necessario per il provisioning degli utenti](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su Elium. Per altre informazioni, vedere [controllare lo stato del provisioning dell'utente](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
