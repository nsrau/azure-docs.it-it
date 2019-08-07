---
title: 'Esercitazione: Configurare SmartFile per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in SmartFile.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: da7c95461696d3246995b5ad36a906cc3dd909c2
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775448"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Esercitazione: Configurare SmartFile per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in SmartFile e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in SmartFile.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)le anteprime di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Tenant di SmartFile](https://www.SmartFile.com/pricing/).
* Un account utente in SmartFile con autorizzazioni di amministratore.

## <a name="assigning-users-to-smartfile"></a>Assegnazione di utenti a SmartFile

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a SmartFile. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a SmartFile seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Suggerimenti importanti per l'assegnazione di utenti a SmartFile

* È consigliabile assegnare un singolo Azure AD utente a SmartFile per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a SmartFile, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-smartfile-for-provisioning"></a>Configurare SmartFile per il provisioning

Prima di configurare SmartFile per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning di SCIM su SmartFile e raccogliere ulteriori dettagli necessari.

1. Accedere a SmartFile Admin Console. Passare all'angolo superiore destro della console di amministrazione di SmartFile. Selezionare **Product Key**.

    ![Console di amministrazione di SmartFile](media/smartfile-provisioning-tutorial/login.png)

2. Per generare una bearer token, copiare il **codice Product Key** e la relativa **password**. Incollarli in un blocco note con i due punti tra di essi.
    
     ![SmartFile aggiungere SCIM](media/smartfile-provisioning-tutorial/auth.png)

    ![SmartFile aggiungere SCIM](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Aggiungere SmartFile dalla raccolta

Per configurare SmartFile per il provisioning utenti automatico con Azure AD, è necessario aggiungere SmartFile dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere SmartFile dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **SmartFile**, selezionare **SmartFile** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SmartFile nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Configurazione del provisioning utenti automatico in SmartFile 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in SmartFile in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per SmartFile, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di SmartFile](SmartFile-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Per configurare il provisioning utenti automatico per SmartFile in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SmartFile**.

    ![Collegamento di SmartFile nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5.  Nella sezione **credenziali amministratore** immettere `https://<SmartFile sitename>.smartfile.com/ftp/scim` in **URL tenant**. Un esempio potrebbe essere simile `https://demo1test.smartfile.com/ftp/scim`a. Immettere il valore del **token di porta** (ProductKey: ProductPassword) recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a SmartFile. Se la connessione non riesce, verificare che l'account SmartFile disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a SmartFile**.

    ![Mapping utente SmartFile](media/smartfile-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a SmartFile nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in SmartFile per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di SmartFile](media/smartfile-provisioning-tutorial/userattribute.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to SmartFile**.

    ![Mapping del gruppo SmartFile](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a SmartFile nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in SmartFile per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo SmartFile](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per SmartFile, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in SmartFile selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

    L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su SmartFile.

    Per altre informazioni su come leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Limitazioni dei connettori

* SmartFile supporta solo le eliminazioni rigide. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

 [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
