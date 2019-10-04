---
title: 'Esercitazione: Configurare visitando il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente per visitare.
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
ms.openlocfilehash: 942f0aa685ff7e2278aae159f7e97917a105f5fa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840155"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Esercitazione: Configurare visitando il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in visita ed Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti o gruppi per visitare la pagina.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning utenti Azure AD. Per informazioni importanti sul funzionamento di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS (software-as-a-Service) con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant visitabile](https://www.visitly.io/pricing/)
* Un account utente in visita con autorizzazioni di amministratore

## <a name="assign-users-to-visitly"></a>Assegna utenti a visita 

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti o gruppi in Azure AD necessario accedere a visitare. Assegnare quindi tali utenti o gruppi a visitando le istruzioni seguenti:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Suggerimenti importanti per l'assegnazione di utenti a visite 

* È consigliabile assegnare un singolo utente Azure AD per verificare la configurazione del provisioning utenti automatico. È possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a visitabile, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning.

## <a name="set-up-visitly-for-provisioning"></a>Configurare la visita per il provisioning

Prima di configurare visitando il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning di sistema per la gestione delle identità tra domini (SCIM) in visita.

1. Accedere a [visitare](https://app.visitly.io/login). Selezionare **integrations** > **Synchronization host**.

    ![Sincronizzazione host](media/Visitly-provisioning-tutorial/login.png)

2. Selezionare la sezione **Azure ad** .

    ![Sezione Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Copiare la **chiave API**. Questi valori vengono immessi nella casella **token segreto** nella scheda **provisioning** dell'applicazione visita nel portale di Azure.

    ![Chiave API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Aggiungi visita dalla raccolta

Per configurare visitando il provisioning utenti automatico con Azure AD, è possibile aggiungere visitando la raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

Per aggiungere visita dalla raccolta di applicazioni di Azure AD, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **visitly**, scegliere **visita** nel pannello dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Visitly nell'elenco risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Configurare il provisioning utenti automatico per visitare 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti o gruppi in modo visitabile in base alle assegnazioni di utenti o gruppi nel Azure AD.

> [!TIP]
> Per abilitare la Single Sign-On basata su SAML per la visita, seguire le istruzioni riportate nell' [esercitazione Single Sign-on visitare](Visitly-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Configurare il provisioning utenti automatico per visitare il Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali** > **tutte le applicazioni**.

    ![Tutte le applicazioni](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Visitly**.

    ![Collegamento di Visitly nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning impostata su automatica](common/provisioning-automatic.png)

5. Nella sezione credenziali amministratore immettere i valori di `https://api.visitly.io/v1/usersync/SCIM` e delle **chiavi API** recuperati in precedenza rispettivamente in **URL tenant** e **token segreto**. Selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a visitare. Se la connessione non riesce, verificare che l'account visity disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nella casella **posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Indirizzo di posta elettronica per le notifiche](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a visitarli**.

    ![Mapping di utenti visitabili](media/visitly-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a visitabili nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente visitabili](media/visitly-provisioning-tutorial/userattribute.png)

10. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'esercitazione relativa al [filtro di ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning Azure AD per la visita, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

13. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nell' **ambito** nella sezione **Impostazioni** . La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per l'esecuzione del provisioning da parte di utenti o gruppi, vedere [quanto tempo sarà necessario per il provisioning degli utenti](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad in visita. Per altre informazioni, vedere [controllare lo stato del provisioning dell'utente](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

Visitare non supporta le eliminazioni a livello di hardware. Tutto è solo l'eliminazione temporanea.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
