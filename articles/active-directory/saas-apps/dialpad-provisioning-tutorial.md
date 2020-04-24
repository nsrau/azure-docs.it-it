---
title: 'Esercitazione: configurare un dispositivo per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058366"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Esercitazione: configurare il dispositivo per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Dialpad e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Dialpad.

> [!NOTE]
>  L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant con realpad](https://www.dialpad.com/pricing/).
* Un account utente in Dialpad con autorizzazioni di amministratore.

## <a name="assign-users-to-dialpad"></a>Assegna utenti a Dialpad
Azure Active Directory usa un concetto denominato assegnazioni per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Dialpad. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a realpad seguendo le istruzioni riportate qui:
 
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Suggerimenti importanti per l'assegnazione di utenti a realpad

 * Per testare la configurazione del provisioning utenti automatico, è consigliabile assegnare a un singolo Azure AD utente. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Dialpad, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning.

## <a name="setup-dialpad-for-provisioning"></a>Programma di installazione per il provisioning

Prima di configurare il provisioning utenti automatico con Azure AD, è necessario recuperare alcune informazioni di provisioning da Dialpad.

1. Accedere alla console di [Amministrazione](https://dialpadbeta.com/login) di e selezionare **impostazioni di amministrazione**. Assicurarsi che l' **azienda** sia selezionata nell'elenco a discesa. Passare a **autenticazione > chiavi API**.

    ![Aggiunta SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Per generare una nuova chiave, fare clic su **Aggiungi una chiave** e configurare le proprietà del token segreto.

    ![Aggiunta SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Aggiunta SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Fare clic sul pulsante **fare clic per visualizzare il valore** per la chiave API creata di recente e copiare il valore visualizzato. Questo valore verrà immesso nel campo **token segreto** nella scheda provisioning dell'applicazione Dialpad nel portale di Azure. 

    ![Token di creazione Dialpad](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Aggiungere Dialpad dalla raccolta

Per configurare il provisioning utenti automatico con Azure AD, è necessario aggiungere Dialpad dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Dialpad dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di **ricerca immettere Dialpad**, selezionare **Dialpad** nel pannello dei risultati.
    ![Dialpad nell'elenco dei risultati](common/search-new-app.png)

5. Passare all' **URL** evidenziato di seguito in un browser separato. 

    ![Aggiunta SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. Nell'angolo in alto a destra selezionare **accedi > usare il tastierino online**.

    ![Aggiunta SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Come dipassd è un'app OpenIDConnect, scegliere di eseguire l'accesso a realpad usando l'account Microsoft Work.

    ![Aggiunta SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Una volta completata l'autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e si verrà reindirizzati al proprio account.

    ![Aggiunta SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configurare il provisioning utenti automatico in Dialpad

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Dialpad in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Dialpad**.

    ![Collegamento Dialpad nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **provisioning** .

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://dialpad.com/scim` in **URL tenant**. Immettere il valore recuperato e salvato in precedenza da Dialpad nel **token Secret**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a un. Se la connessione non riesce, verificare che l'account Dialpad disponga di autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Dialpad**.

    ![Mapping degli utenti Dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Dialpad nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Dialpad per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per i Dialpad, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning per il provisioning selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad in Dialpad.

Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Limitazioni dei connettori
* Dialpad non supporta oggi i rinomi di gruppo. Ciò significa che tutte le modifiche apportate al **DisplayName** di un gruppo in Azure ad non verranno aggiornate e riflesse in Dialpad.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
