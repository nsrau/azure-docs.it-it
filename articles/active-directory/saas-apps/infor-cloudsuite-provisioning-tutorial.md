---
title: 'Esercitazione: configurare infor CloudSuite per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: a1a274d9a20a5c7e487536e2850f253c3230c0cc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408716"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Esercitazione: configurare infor CloudSuite per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in infor CloudSuite e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in infor CloudSuite.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di CloudSuite infor](https://www.infor.com/products/infor-os)
* Un account utente in infor CloudSuite con autorizzazioni di amministratore.

## <a name="assigning-users-to-infor-cloudsuite"></a>Assegnazione di utenti a infor CloudSuite

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a infor CloudSuite. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a infor CloudSuite seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Suggerimenti importanti per l'assegnazione di utenti a infor CloudSuite

* È consigliabile assegnare un singolo Azure AD utente a infor CloudSuite per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a infor CloudSuite, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurare infor CloudSuite per il provisioning

1. Accedere alla console di [amministrazione di infor CloudSuite](https://www.infor.com/customer-center). Fare clic sull'icona utente e quindi passare a **Gestione utenti**.

    ![Console di amministrazione di infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Fare clic sull'icona di menu nell'angolo in alto a sinistra della schermata. Fare clic su **Gestisci**.

    ![Infor CloudSuite aggiungere SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Passare a **account scim**.

    ![Account infor CloudSuite SCIM](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Aggiungere un utente amministratore facendo clic sull'icona con il segno più. Specificare una **password di SCIM** e digitare la stessa password in **Conferma password**. Fare clic sull'icona della cartella per salvare la password. Verrà quindi visualizzato un **identificatore utente** generato per l'utente amministratore.

    ![Utente amministratore di infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Password CloudSuite infor](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![ID CloudSuite infor](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Per generare la bearer token, copiare l' **identificatore utente** e la **password scim**. Incollarli nel blocco note + + separati da due punti. Per codificare il valore della stringa, passare a plug-in **> strumenti MIME > codifica Basic64**. 

    ![ID CloudSuite infor](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Copiare il bearer token. Questo valore verrà immesso nel campo token segreto nella scheda provisioning dell'applicazione infor CloudSuite nel portale di Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Aggiungere infor CloudSuite dalla raccolta

Prima di configurare infor CloudSuite per il provisioning utenti automatico con Azure AD, è necessario aggiungere infor CloudSuite dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere infor CloudSuite dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **infor CloudSuite**, selezionare **infor CloudSuite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Infor CloudSuite nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configurazione del provisioning utenti automatico in infor CloudSuite 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in infor CloudSuite in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per infor CloudSuite, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per altre informazioni sull'endpoint SCIM di infor CloudSuite, vedere [questo](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#)articolo.

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Per configurare il provisioning utenti automatico per infor CloudSuite in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Infor CloudSuite**.

    ![Collegamento di Infor CloudSuite nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` in **URL tenant**. Immettere il valore bearer token recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per assicurarsi che Azure ad possibile connettersi a infor CloudSuite. Se la connessione non riesce, verificare che l'account infor CloudSuite disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a infor CloudSuite**.

    ![Mapping utente infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a infor CloudSuite nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in infor CloudSuite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di CloudSuite infor](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to infor CloudSuite**.

    ![Mapping dei gruppi di CloudSuite infor](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a infor CloudSuite nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in infor CloudSuite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per infor CloudSuite, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in infor CloudSuite scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad su infor CloudSuite.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)